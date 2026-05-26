Analyzed only `src` and `webgoat.json`. I did not execute the API.

### Function 1: complete intercepted request challenge

Function name:
complete intercepted request challenge

Core endpoint(s):
- `GET /HttpProxies/intercept-request`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The request-interception lesson is marked solved.
- Invocation:
  Step 1: `GET /HttpProxies/intercept-request` with header `x-request-intercepted=true` and query `changeMe=Requests are tampered easily`.
- Constraints:
  The method must be `GET`; `POST` is documented but the implementation always fails it. The query value is compared case-insensitively.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The documented `POST` variant is used.
  - Failure endpoint:
    `POST /HttpProxies/intercept-request`
  - Why this fails:
    The implementation immediately fails all POST requests.
  - Intentionally violated constraints:
    The required method is `GET`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The header or query value is missing or incorrect.
  - Failure endpoint:
    `GET /HttpProxies/intercept-request`
  - Why this fails:
    The implementation requires both the header and the exact tampered query value.
  - Intentionally violated constraints:
    Missing or mismatched `x-request-intercepted` / `changeMe`.

Endpoint coverage:
- Covers:
  `GET /HttpProxies/intercept-request`
- Distinct meaning:
  `GET` can solve the lesson; `POST` is a documented but deliberately failing branch.

### Function 2: retrieve challenge logo

Function name:
retrieve challenge logo

Core endpoint(s):
- `GET /challenge/logo`
- `POST /challenge/logo`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the challenge logo PNG with a runtime PIN embedded in fixed bytes.
- Invocation:
  Step 1: `GET /challenge/logo` with required path/query/body/form/header values
- Constraints:
  `POST /challenge/logo` behaves the same as `GET /challenge/logo`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /challenge/logo`
  - Why this fails:
    It does not fail under normal implemented conditions unless the classpath image cannot be read.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /challenge/logo`
  `POST /challenge/logo`
- Distinct meaning:
  Retrieves the image used to discover the PIN for challenge 1.

### Function 3: solve challenge 1 admin login

Function name:
solve challenge 1 admin login

Core endpoint(s):
- `POST /challenge/1`

Preconditions:
- A prerequisite state or response value produced by `GET /challenge/logo` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /challenge/logo` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the admin credentials and returns flag 1.
- Invocation:
  Step 1: `POST /challenge/1` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /challenge/logo` exposes the runtime PIN embedded in the logo. The core endpoint `POST /challenge/1` must use `username=admin` and `password=!!webgoat_admin_{PIN}!!`, where `{PIN}` is the four-digit value from the precondition endpoint `GET /challenge/logo`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The username is not `admin` or the password does not contain the embedded PIN.
  - Failure endpoint:
    `POST /challenge/1`
  - Why this fails:
    The implementation compares the submitted password with the constant template after replacing `1234` with the runtime PIN.
  - Intentionally violated constraints:
    Wrong username or wrong PIN-derived password.

Endpoint coverage:
- Covers:
  `POST /challenge/1`
- Distinct meaning:
  Uses the logo endpoint as setup for the challenge login workflow.

### Function 4: submit captured challenge flag

Function name:
submit captured challenge flag

Core endpoint(s):
- `POST /challenge/flag/{flagNumber}`

Preconditions:
- A prerequisite state or response value produced by `POST /challenge/1` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /challenge/1` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The submitted flag is accepted for the requested challenge number.
- Invocation:
  Step 1: `POST /challenge/flag/{flagNumber}` with required path/query/body/form/header values
- Constraints:
  One supported sequence is to solve challenge 1 first, then call the core endpoint `POST /challenge/flag/{flagNumber}` with `flagNumber=1` and `flag` equal to the flag returned by the precondition endpoint `POST /challenge/1`. Other challenge-solving endpoints can similarly produce flags for their own numbers.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The submitted `flag` does not equal the server-side flag for `{flagNumber}`.
  - Failure endpoint:
    `POST /challenge/flag/{flagNumber}`
  - Why this fails:
    The implementation compares the value against `flags.getFlag(flagNumber)`.
  - Intentionally violated constraints:
    Incorrect flag value.

Endpoint coverage:
- Covers:
  `POST /challenge/flag/{flagNumber}`
- Distinct meaning:
  Confirms a flag obtained from a challenge workflow.

### Function 5: register SQL challenge user

Function name:
register SQL challenge user

Core endpoint(s):
- `PUT /SqlInjectionAdvanced/challenge`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  A new SQL challenge user is inserted.
- Invocation:
  Step 1: `PUT /SqlInjectionAdvanced/challenge` with required path/query/body/form/header values
- Constraints:
  `username_reg`, `email_reg`, and `password_reg` must be non-empty. `username_reg` must be at most 250 characters; `email_reg` and `password_reg` must be at most 30 characters. The username must not already exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: A required field is empty or too long.
  - Failure endpoint:
    `PUT /SqlInjectionAdvanced/challenge`
  - Why this fails:
    `checkArguments` rejects empty values and length violations.
  - Intentionally violated constraints:
    Invalid registration field.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The username already exists and the username does not contain `tom'`.
  - Failure endpoint:
    `PUT /SqlInjectionAdvanced/challenge`
  - Why this fails:
    The duplicate check returns `user.exists` as a failure for normal duplicate usernames.
  - Intentionally violated constraints:
    Duplicate `username_reg`.

Endpoint coverage:
- Covers:
  `PUT /SqlInjectionAdvanced/challenge`
- Distinct meaning:
  Creates a user when the user does not already exist.

### Function 6: trigger SQL challenge existing-user success

Function name:
trigger SQL challenge existing-user success

Core endpoint(s):
- `PUT /SqlInjectionAdvanced/challenge`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The challenge is marked solved by making the duplicate-user branch return success.
- Invocation:
  Step 1: `PUT /SqlInjectionAdvanced/challenge` with required path/query/body/form/header values
- Constraints:
  `username_reg` must cause the duplicate-user query to find an existing row and must contain `tom'`. This is a distinct success meaning from normal user creation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The injected username does not find an existing row.
  - Failure endpoint:
    `PUT /SqlInjectionAdvanced/challenge`
  - Why this fails:
    The endpoint inserts a new user instead of taking the `user.exists` challenge-success branch.
  - Intentionally violated constraints:
    Missing duplicate-match condition.

Endpoint coverage:
- Covers:
  `PUT /SqlInjectionAdvanced/challenge`
- Distinct meaning:
  Uses the existing-user branch as a challenge solution, not as ordinary registration.

### Function 7: log in to SQL injection challenge

Function name:
log in to SQL injection challenge

Core endpoint(s):
- `POST /SqlInjectionAdvanced/challenge_Login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  Login succeeds for user `tom`.
- Invocation:
  Step 1: `POST /SqlInjectionAdvanced/challenge_Login` with required path/query/body/form/header values
- Constraints:
  `username_login=tom` and `password_login=thisisasecretfortomonly`, as seeded in `src/main/resources/lessons/sqlinjection/db/migration/V2019_09_26_5__challenge_assignment.sql`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Credentials match a non-`tom` user.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/challenge_Login`
  - Why this fails:
    The query finds a row, but the implementation only succeeds when `username_login` is `tom`.
  - Intentionally violated constraints:
    User is not `tom`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: No row matches the submitted credentials.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/challenge_Login`
  - Why this fails:
    The prepared statement returns no result.
  - Intentionally violated constraints:
    Wrong username or password.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionAdvanced/challenge_Login`
- Distinct meaning:
  Authenticates against the SQL challenge user table.

### Function 8: log in for IDOR lesson

Function name:
log in for IDOR lesson

Core endpoint(s):
- `POST /IDOR/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The session is authenticated as `tom` and stores Tom’s user id.
- Invocation:
  Step 1: `POST /IDOR/login` with required path/query/body/form/header values
- Constraints:
  `username=tom` and `password=cat`. The response state is stored in lesson session keys `idor-authenticated-as` and `idor-authenticated-user-id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username is not `tom` or password is not `cat`.
  - Failure endpoint:
    `POST /IDOR/login`
  - Why this fails:
    Only Tom’s credentials are accepted even though Bill’s profile data exists.
  - Intentionally violated constraints:
    Wrong IDOR credentials.

Endpoint coverage:
- Covers:
  `POST /IDOR/login`
- Distinct meaning:
  Establishes session state required by later IDOR functions.

### Function 9: retrieve own IDOR profile

Function name:
retrieve own IDOR profile

Core endpoint(s):
- `GET /IDOR/own`

Preconditions:
- A prerequisite state or response value produced by `POST /IDOR/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /IDOR/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API returns Tom’s own profile.
- Invocation:
  Step 1: `GET /IDOR/own` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /IDOR/login` must authenticate as `tom`. `GET /IDOR/profile` is an alternate path for the same implementation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The required IDOR login prerequisite is omitted.
  - Failure endpoint:
    `GET /IDOR/own`
  - Why this fails:
    The session does not contain `idor-authenticated-as=tom`, so the implementation returns no profile details.
  - Intentionally violated constraints:
    Omitted `POST /IDOR/login`.

Endpoint coverage:
- Covers:
  `GET /IDOR/own`
- Distinct meaning:
  Reads the authenticated user’s profile from session state.

### Function 10: verify alternate own-profile URL

Function name:
verify alternate own-profile URL

Core endpoint(s):
- `POST /IDOR/profile/alt-path`

Preconditions:
- A prerequisite state or response value produced by `POST /IDOR/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /IDOR/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The lesson accepts a URL pointing to Tom’s own profile id.
- Invocation:
  Step 1: `POST /IDOR/profile/alt-path` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /IDOR/profile/alt-path` must submit `url=WebGoat/IDOR/profile/{authUserId}`, where `{authUserId}` is the Tom id stored by the precondition endpoint `POST /IDOR/login`, `2342384`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The URL path does not match `WebGoat/IDOR/profile/{authUserId}`.
  - Failure endpoint:
    `POST /IDOR/profile/alt-path`
  - Why this fails:
    The implementation splits the submitted URL and checks fixed path parts plus the authenticated user id.
  - Intentionally violated constraints:
    Mismatched URL path or id.

Endpoint coverage:
- Covers:
  `POST /IDOR/profile/alt-path`
- Distinct meaning:
  Verifies the alternate URL construction for the authenticated user’s profile.

### Function 11: identify IDOR-exposed attributes

Function name:
identify IDOR-exposed attributes

Core endpoint(s):
- `POST /IDOR/diff-attributes`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts `userId` and `role` as the differing attributes.
- Invocation:
  Step 1: `POST /IDOR/diff-attributes` with required path/query/body/form/header values
- Constraints:
  The comma-separated `attributes` value must contain both `userid` and `role`, in either order, case-insensitively.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Fewer than two attributes are submitted.
  - Failure endpoint:
    `POST /IDOR/diff-attributes`
  - Why this fails:
    The implementation requires at least two comma-separated values.
  - Intentionally violated constraints:
    Missing second attribute.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The pair is not `userid` and `role`.
  - Failure endpoint:
    `POST /IDOR/diff-attributes`
  - Why this fails:
    The endpoint checks exactly those two names.
  - Intentionally violated constraints:
    Wrong attribute names.

Endpoint coverage:
- Covers:
  `POST /IDOR/diff-attributes`
- Distinct meaning:
  Validates the attribute-discovery answer.

### Function 12: retrieve another IDOR profile

Function name:
retrieve another IDOR profile

Core endpoint(s):
- `GET /IDOR/profile/{userId}`

Preconditions:
- A prerequisite state or response value produced by `POST /IDOR/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /IDOR/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API returns Bill’s profile while authenticated as Tom.
- Invocation:
  Step 1: `GET /IDOR/profile/{userId}` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /IDOR/login` must authenticate as Tom. The core endpoint `GET /IDOR/profile/{userId}` must use `{userId}=2342388`, which is Bill’s seeded profile id and must differ from Tom’s id `2342384`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The required login prerequisite is omitted.
  - Failure endpoint:
    `GET /IDOR/profile/{userId}`
  - Why this fails:
    The session lacks `idor-authenticated-as=tom`.
  - Intentionally violated constraints:
    Omitted `POST /IDOR/login`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `{userId}` equals Tom’s own id or is not Bill’s id.
  - Failure endpoint:
    `GET /IDOR/profile/{userId}`
  - Why this fails:
    Own id is rejected as too close, and unknown ids do not resolve to Bill’s profile.
  - Intentionally violated constraints:
    Wrong target id.

Endpoint coverage:
- Covers:
  `GET /IDOR/profile/{userId}`
- Distinct meaning:
  Reads another user’s profile via an object id.

### Function 13: edit another IDOR profile

Function name:
edit another IDOR profile

Core endpoint(s):
- `PUT /IDOR/profile/{userId}`

Preconditions:
- A prerequisite state or response value produced by `POST /IDOR/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /IDOR/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API updates another profile in session and marks the lesson solved.
- Invocation:
  Step 1: `PUT /IDOR/profile/{userId}` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /IDOR/login` must authenticate as Tom. The core endpoint `PUT /IDOR/profile/{userId}` must use a body `userId` that is present and not equal to Tom’s authenticated id. The submitted body must set `color=red` and `role<=1`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The submitted body `userId` equals Tom’s authenticated id.
  - Failure endpoint:
    `PUT /IDOR/profile/{userId}`
  - Why this fails:
    The endpoint rejects editing the authenticated user through the “other profile” branch.
  - Intentionally violated constraints:
    Body `userId` matches authenticated id.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Role is greater than 1 or color is not `red`.
  - Failure endpoint:
    `PUT /IDOR/profile/{userId}`
  - Why this fails:
    The success branch checks both `role<=1` and red color.
  - Intentionally violated constraints:
    Wrong role/color combination.

Endpoint coverage:
- Covers:
  `PUT /IDOR/profile/{userId}`
- Distinct meaning:
  Modifies another profile’s role and color through client-supplied profile data.

### Function 14: submit simple XXE comment

Function name:
submit simple XXE comment

Core endpoint(s):
- `POST /xxe/simple`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API parses an XML comment, stores it for the current user, and marks the lesson solved if the text contains an operating-system directory marker.
- Invocation:
  Step 1: `POST /xxe/simple` with required path/query/body/form/header values
- Constraints:
  The body is parsed as XML `comment`. The parsed `text` must include one of `usr`, `etc`, or `var` on Unix-like systems, or one of the Windows directory markers on Windows.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The XML cannot be parsed.
  - Failure endpoint:
    `POST /xxe/simple`
  - Why this fails:
    XML parsing throws and the stack trace is returned in the failed `AttackResult`.
  - Intentionally violated constraints:
    Invalid XML body.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Parsed comment text does not contain an expected directory marker.
  - Failure endpoint:
    `POST /xxe/simple`
  - Why this fails:
    The parsed comment is stored, but `checkSolution` does not match.
  - Intentionally violated constraints:
    Missing expected file-content text.

Endpoint coverage:
- Covers:
  `POST /xxe/simple`
- Distinct meaning:
  Adds and evaluates an XML comment for XXE content.

### Function 15: retrieve XXE comments

Function name:
retrieve XXE comments

Core endpoint(s):
- `GET /xxe/comments`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns default comments plus comments stored for the current user.
- Invocation:
  Step 1: `GET /xxe/comments` with required path/query/body/form/header values
- Constraints:
  No setup endpoint is required because default comments exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /xxe/comments`
  - Why this fails:
    Normal execution always returns the comment collection.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /xxe/comments`
- Distinct meaning:
  Lists XXE lesson comments.

### Function 16: retrieve sample DTD template

Function name:
retrieve sample DTD template

Core endpoint(s):
- `GET /xxe/sampledtd`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a plain-text DTD template for the blind XXE lesson.
- Invocation:
  Step 1: `GET /xxe/sampledtd` with required path/query/body/form/header values
- Constraints:
  The source uses `@RequestMapping` without a method restriction, so all documented methods return the same template.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /xxe/sampledtd`
  - Why this fails:
    Normal execution always returns the static template.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /xxe/sampledtd`
- Distinct meaning:
  All documented methods map to the same DTD-template retrieval function.

### Function 17: submit content-type XXE payload

Function name:
submit content-type XXE payload

Core endpoint(s):
- `POST /xxe/content-type`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts an XML request body and solves the content-type XXE lesson when parsed text contains expected directory content.
- Invocation:
  Step 1: `POST /xxe/content-type` with required path/query/body/form/header values
- Constraints:
  Header `Content-Type` must contain `application/xml`; the body must parse as XML `comment`; parsed `text` must contain an OS directory marker.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `Content-Type` is exactly `application/json`.
  - Failure endpoint:
    `POST /xxe/content-type`
  - Why this fails:
    The implementation parses the body as JSON, stores the comment globally if valid, and returns JSON-specific failure feedback.
  - Intentionally violated constraints:
    Used JSON instead of XML.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: XML body is invalid or lacks expected directory content.
  - Failure endpoint:
    `POST /xxe/content-type`
  - Why this fails:
    XML parsing fails or `checkSolution` does not match.
  - Intentionally violated constraints:
    Invalid XML or missing file-content marker.

Endpoint coverage:
- Covers:
  `POST /xxe/content-type`
- Distinct meaning:
  Uses content type to choose JSON comment handling versus vulnerable XML parsing.

### Function 18: solve blind XXE by submitting extracted secret

Function name:
solve blind XXE by submitting extracted secret

Core endpoint(s):
- `POST /xxe/blind`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API marks the blind XXE lesson solved when the submitted body contains the current user’s generated secret file content.
- Invocation:
  Step 1: `POST /xxe/blind` with required path/query/body/form/header values
- Constraints:
  The secret file content is created by lesson initialization, not by a documented Swagger endpoint. The request body must contain the exact generated secret string.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The body does not contain the generated secret.
  - Failure endpoint:
    `POST /xxe/blind`
  - Why this fails:
    The endpoint parses the body as XML comment and stores it, then returns failure.
  - Intentionally violated constraints:
    Missing exact secret string.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: XML parsing fails.
  - Failure endpoint:
    `POST /xxe/blind`
  - Why this fails:
    The parse exception is returned in a failed `AttackResult`.
  - Intentionally violated constraints:
    Invalid XML body.

Endpoint coverage:
- Covers:
  `POST /xxe/blind`
- Distinct meaning:
  Either stores an XML comment or solves the lesson if the secret is submitted directly.

### Function 19: solve sample lesson-template attack

Function name:
solve sample lesson-template attack

Core endpoint(s):
- `POST /lesson-template/sample-attack`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The sample lesson returns success.
- Invocation:
  Step 1: `POST /lesson-template/sample-attack` with required path/query/body/form/header values
- Constraints:
  `param1` must equal `secr37Value`. `param2` is required by the OpenAPI contract but is not used by the implementation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `param1` is not `secr37Value`.
  - Failure endpoint:
    `POST /lesson-template/sample-attack`
  - Why this fails:
    Only `param1` is compared with the hard-coded secret.
  - Intentionally violated constraints:
    Wrong `param1`.

Endpoint coverage:
- Covers:
  `POST /lesson-template/sample-attack`
- Distinct meaning:
  Demonstrates a simple assignment endpoint.

### Function 20: retrieve sample shop basket

Function name:
retrieve sample shop basket

Core endpoint(s):
- `GET /lesson-template/shop/{user}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a fixed sample basket for the path user.
- Invocation:
  Step 1: `GET /lesson-template/shop/{user}` with required path/query/body/form/header values
- Constraints:
  `{user}` is accepted but not used to customize the returned items.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /lesson-template/shop/{user}`
  - Why this fails:
    Normal execution always returns the fixed item list.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /lesson-template/shop/{user}`
- Distinct meaning:
  Auxiliary sample data retrieval.

### Function 21: list CSRF reviews

Function name:
list CSRF reviews

Core endpoint(s):
- `GET /csrf/review`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the current user’s reviews plus default reviews.
- Invocation:
  Step 1: `GET /csrf/review` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required because default reviews are always present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /csrf/review`
  - Why this fails:
    Normal execution always returns a collection.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /csrf/review`
- Distinct meaning:
  Reads CSRF lesson reviews.

### Function 22: create forged CSRF review

Function name:
create forged CSRF review

Core endpoint(s):
- `POST /csrf/review`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API stores a review for the current user and marks the CSRF review lesson solved.
- Invocation:
  Step 1: `POST /csrf/review` with required path/query/body/form/header values
- Constraints:
  `reviewText`, `stars`, and `validateReq` are submitted. `validateReq` must equal `2aa14227b9a13d0bede0388a7fba9aa9`. The `Referer` header must be missing or from a different host than `Host`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `validateReq` is missing or wrong.
  - Failure endpoint:
    `POST /csrf/review`
  - Why this fails:
    The review is still stored, but the lesson returns missing-token feedback.
  - Intentionally violated constraints:
    Wrong anti-CSRF value.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `Referer` host equals `Host`.
  - Failure endpoint:
    `POST /csrf/review`
  - Why this fails:
    The implementation rejects same-host referers.
  - Intentionally violated constraints:
    Same-origin request headers.

Endpoint coverage:
- Covers:
  `POST /csrf/review`
- Distinct meaning:
  Creates a review and validates the CSRF bypass conditions.

### Function 23: verify CSRF login username

Function name:
verify CSRF login username

Core endpoint(s):
- `POST /csrf/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The lesson succeeds when the authenticated username starts with `csrf`.
- Invocation:
  Step 1: `POST /csrf/login` with required path/query/body/form/header values
- Constraints:
  The current authenticated WebGoat username must start with `csrf`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Current username does not start with `csrf`.
  - Failure endpoint:
    `POST /csrf/login`
  - Why this fails:
    The implementation checks only the current username prefix.
  - Intentionally violated constraints:
    Wrong authenticated username.

Endpoint coverage:
- Covers:
  `POST /csrf/login`
- Distinct meaning:
  Validates that the CSRF login flow reached a `csrf*` account.

### Function 24: get and confirm CSRF basic flag

Function name:
get and confirm CSRF basic flag

Core endpoint(s):
- `POST /csrf/confirm-flag-1`

Preconditions:
- A prerequisite state or response value produced by `POST /csrf/basic-get-flag` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /csrf/basic-get-flag` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API generates a CSRF flag and then accepts that same flag.
- Invocation:
  Step 1: `POST /csrf/confirm-flag-1` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /csrf/basic-get-flag` succeeds when `Referer` is absent or from a different host. The precondition endpoint `POST /csrf/basic-get-flag` stores `csrf-get-success`; the core endpoint `POST /csrf/confirm-flag-1` must send `confirmFlagVal` equal to that stored value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Step 1 uses a same-host `Referer`.
  - Failure endpoint:
    `POST /csrf/basic-get-flag`
  - Why this fails:
    The response has `success=false` and no flag.
  - Intentionally violated constraints:
    Same-host referer.
- Branch 2:
  - Preconditions:
    - Step 2 sends the wrong confirmation value or Step 1 was omitted.
  - Failure endpoint:
    `POST /csrf/confirm-flag-1`
  - Why this fails:
    The session does not contain the matching `csrf-get-success` value.
  - Intentionally violated constraints:
    Missing or mismatched generated flag.

Endpoint coverage:
- Covers:
  `POST /csrf/confirm-flag-1`
- Distinct meaning:
  Generates and confirms the CSRF GET-style lesson flag.

### Function 25: create and confirm CSRF feedback flag

Function name:
create and confirm CSRF feedback flag

Core endpoint(s):
- `POST /csrf/feedback`

Preconditions:
- A prerequisite state or response value produced by `POST /csrf/feedback/message` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /csrf/feedback/message` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts a forged feedback message, returns a flag, and then confirms it.
- Invocation:
  Step 1: `POST /csrf/feedback` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /csrf/feedback/message` body must be valid JSON text, request must contain `JSESSIONID`, `Content-Type` must contain `text/plain`, and `Referer` must be absent or different from `Host`. The core endpoint `POST /csrf/feedback` must send `confirmFlagVal` equal to the flag stored by the precondition endpoint `POST /csrf/feedback/message`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Feedback body is invalid JSON.
  - Failure endpoint:
    `POST /csrf/feedback/message`
  - Why this fails:
    Jackson parsing fails and the stack trace is returned as feedback.
  - Intentionally violated constraints:
    Invalid JSON body.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie/content-type/referer conditions are not met.
  - Failure endpoint:
    `POST /csrf/feedback/message`
  - Why this fails:
    `correctCSRF` remains false.
  - Intentionally violated constraints:
    Missing `JSESSIONID`, wrong content type, or same-host referer.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Confirmation value does not match the generated flag.
  - Failure endpoint:
    `POST /csrf/feedback`
  - Why this fails:
    The submitted value is compared to session key `csrf-feedback`.
  - Intentionally violated constraints:
    Wrong `confirmFlagVal`.

Endpoint coverage:
- Covers:
  `POST /csrf/feedback`
- Distinct meaning:
  Creates and confirms a CSRF feedback flag.

### Function 26: retrieve RSA private key and verify signature

Function name:
retrieve RSA private key and verify signature

Core endpoint(s):
- `POST /crypto/signing/verify`

Preconditions:
- A prerequisite state or response value produced by `GET /crypto/signing/getprivate` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /crypto/signing/getprivate` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API generates or returns the session private key, then accepts a matching modulus and signature.
- Invocation:
  Step 1: `POST /crypto/signing/verify` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /crypto/signing/getprivate` creates session attributes `privateKeyString` and `keyPair` if missing. The core endpoint `POST /crypto/signing/verify` must submit `modulus` equal to the generated public key modulus and `signature` that verifies against that public key. Any documented method on `/crypto/signing/getprivate` returns the same key.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Step 1 is omitted.
  - Failure endpoint:
    `POST /crypto/signing/verify`
  - Why this fails:
    The implementation expects `keyPair` in the session.
  - Intentionally violated constraints:
    Missing key-generation endpoint.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Modulus or signature is wrong.
  - Failure endpoint:
    `POST /crypto/signing/verify`
  - Why this fails:
    The modulus check or signature verification fails.
  - Intentionally violated constraints:
    Mismatched cryptographic values.

Endpoint coverage:
- Covers:
  `POST /crypto/signing/verify`
- Distinct meaning:
  The all-method key endpoint is setup for signature verification.

### Function 27: verify secure defaults secret

Function name:
verify secure defaults secret

Core endpoint(s):
- `POST /crypto/secure/defaults`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API marks the secure-defaults lesson solved.
- Invocation:
  Step 1: `POST /crypto/secure/defaults` with required path/query/body/form/header values
- Constraints:
  `secretFileName` must be `default_secret`. `secretText` must hash with SHA-256 to `34de66e5caf2cb69ff2bebdc1f3091ecf6296852446c718e38ebfa60e4aa75d2`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `secretFileName` is not `default_secret`.
  - Failure endpoint:
    `POST /crypto/secure/defaults`
  - Why this fails:
    The filename check is the outer success gate.
  - Intentionally violated constraints:
    Wrong secret filename.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `secretText` hashes to a different value.
  - Failure endpoint:
    `POST /crypto/secure/defaults`
  - Why this fails:
    The SHA-256 comparison fails.
  - Intentionally violated constraints:
    Wrong secret text.

Endpoint coverage:
- Covers:
  `POST /crypto/secure/defaults`
- Distinct meaning:
  Validates recovered secure-defaults secret content.

### Function 28: crack generated MD5 and SHA-256 hashes

Function name:
crack generated MD5 and SHA-256 hashes

Core endpoint(s):
- `POST /crypto/hashing`

Preconditions:
- A prerequisite state or response value produced by `GET /crypto/hashing/md5` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /crypto/hashing/md5` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.
- A prerequisite state or response value produced by `GET /crypto/hashing/sha256` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /crypto/hashing/sha256` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API returns session hashes and then accepts the recovered plaintexts.
- Invocation:
  Step 1: `POST /crypto/hashing` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /crypto/hashing/md5` stores `md5Secret`; the precondition endpoint `GET /crypto/hashing/sha256` stores `sha256Secret`. The core endpoint `POST /crypto/hashing` must submit `answer_pwd1` equal to `md5Secret` and `answer_pwd2` equal to `sha256Secret`. Any documented method on the two hash endpoints behaves as the same retrieval operation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - One or both retrieval endpoints are omitted.
  - Failure endpoint:
    `POST /crypto/hashing`
  - Why this fails:
    The expected session secrets are missing.
  - Intentionally violated constraints:
    Omitted hash-generation endpoints.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Only one recovered password is correct.
  - Failure endpoint:
    `POST /crypto/hashing`
  - Why this fails:
    The implementation returns a specific “one ok” failure when only one answer matches.
  - Intentionally violated constraints:
    One mismatched plaintext.

Endpoint coverage:
- Covers:
  `POST /crypto/hashing`
- Distinct meaning:
  The hash endpoints create session state consumed by the answer endpoint.

### Function 29: solve XOR encoding password

Function name:
solve XOR encoding password

Core endpoint(s):
- `POST /crypto/encoding/xor`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the decoded XOR password.
- Invocation:
  Step 1: `POST /crypto/encoding/xor` with required path/query/body/form/header values
- Constraints:
  `answer_pwd1` must equal `databasepassword`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `answer_pwd1` is missing or not `databasepassword`.
  - Failure endpoint:
    `POST /crypto/encoding/xor`
  - Why this fails:
    The endpoint performs a direct string comparison.
  - Intentionally violated constraints:
    Wrong decoded password.

Endpoint coverage:
- Covers:
  `POST /crypto/encoding/xor`
- Distinct meaning:
  Validates the XOR-decoding answer.

### Function 30: decode generated Basic Auth header

Function name:
decode generated Basic Auth header

Core endpoint(s):
- `POST /crypto/encoding/basic-auth`

Preconditions:
- A prerequisite state or response value produced by `GET /crypto/encoding/basic` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /crypto/encoding/basic` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API returns a Basic Auth header and then accepts the decoded username/password pair.
- Invocation:
  Step 1: `POST /crypto/encoding/basic-auth` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /crypto/encoding/basic` stores `basicAuth` in the session for the current principal. The core endpoint `POST /crypto/encoding/basic-auth` must submit `answer_user` and `answer_pwd` such that Base64 of `answer_user:answer_pwd` equals that stored value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Step 1 is omitted.
  - Failure endpoint:
    `POST /crypto/encoding/basic-auth`
  - Why this fails:
    The session has no generated `basicAuth` value.
  - Intentionally violated constraints:
    Omitted `GET /crypto/encoding/basic`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Decoded user/password pair does not match.
  - Failure endpoint:
    `POST /crypto/encoding/basic-auth`
  - Why this fails:
    The recomputed Basic Auth token differs from the session token.
  - Intentionally violated constraints:
    Wrong decoded credentials.

Endpoint coverage:
- Covers:
  `POST /crypto/encoding/basic-auth`
- Distinct meaning:
  Retrieves and verifies a Basic Auth encoding challenge.

### Function 31: retrieve coupon codes

Function name:
retrieve coupon codes

Core endpoint(s):
- `GET /clientSideFiltering/challenge-store/coupons`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns all checkout coupon codes, including the 100% coupon.
- Invocation:
  Step 1: `GET /clientSideFiltering/challenge-store/coupons` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required; coupon codes are in memory.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /clientSideFiltering/challenge-store/coupons`
  - Why this fails:
    Normal execution always returns the coupon list.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /clientSideFiltering/challenge-store/coupons`
- Distinct meaning:
  Lists the client-side filtering store coupons.

### Function 32: retrieve one coupon code

Function name:
retrieve one coupon code

Core endpoint(s):
- `GET /clientSideFiltering/challenge-store/coupons/{code}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the matching coupon code and discount.
- Invocation:
  Step 1: `GET /clientSideFiltering/challenge-store/coupons/{code}` with required path/query/body/form/header values
- Constraints:
  `{code}` can be one of the built-in codes such as `webgoat`, `owasp`, `owasp-webgoat`, or `get_it_for_free`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `{code}` is unknown.
  - Failure endpoint:
    `GET /clientSideFiltering/challenge-store/coupons/{code}`
  - Why this fails:
    The endpoint still returns 200, but with code `no` and discount `0`.
  - Intentionally violated constraints:
    Unknown coupon code.

Endpoint coverage:
- Covers:
  `GET /clientSideFiltering/challenge-store/coupons/{code}`
- Distinct meaning:
  Looks up a single coupon.

### Function 33: complete free checkout

Function name:
complete free checkout

Core endpoint(s):
- `POST /clientSideFiltering/getItForFree`

Preconditions:
- A prerequisite state or response value produced by `GET /clientSideFiltering/challenge-store/coupons` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /clientSideFiltering/challenge-store/coupons` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the super coupon and marks the assignment solved.
- Invocation:
  Step 1: `POST /clientSideFiltering/getItForFree` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /clientSideFiltering/challenge-store/coupons` is one documented way to discover `get_it_for_free`. The core endpoint `POST /clientSideFiltering/getItForFree` must submit `checkoutCode=get_it_for_free`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `checkoutCode` is not `get_it_for_free`.
  - Failure endpoint:
    `POST /clientSideFiltering/getItForFree`
  - Why this fails:
    The endpoint compares directly with the super coupon constant.
  - Intentionally violated constraints:
    Wrong checkout code.

Endpoint coverage:
- Covers:
  `POST /clientSideFiltering/getItForFree`
- Distinct meaning:
  Uses the discovered coupon to solve the checkout task.

### Function 34: retrieve salaries and submit hidden salary

Function name:
retrieve salaries and submit hidden salary

Core endpoint(s):
- `POST /clientSideFiltering/attack1`

Preconditions:
- A prerequisite state or response value produced by `GET /clientSideFiltering/salaries` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /clientSideFiltering/salaries` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API exposes employee salary data and accepts the target salary answer.
- Invocation:
  Step 1: `POST /clientSideFiltering/attack1` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /clientSideFiltering/attack1` must submit `answer=450000`. The precondition endpoint `GET /clientSideFiltering/salaries` is the documented endpoint that exposes salary data.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `answer` is not `450000`.
  - Failure endpoint:
    `POST /clientSideFiltering/attack1`
  - Why this fails:
    The endpoint performs a direct string comparison.
  - Intentionally violated constraints:
    Wrong salary answer.

Endpoint coverage:
- Covers:
  `POST /clientSideFiltering/attack1`
- Distinct meaning:
  Reads client-side data and submits the discovered value.

### Function 35: submit CIA quiz and read results

Function name:
submit CIA quiz and read results

Core endpoint(s):
- `POST /cia/quiz`
- `GET /cia/quiz`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API records all CIA quiz answers as correct and returns the correctness array.
- Invocation:
  Step 1: `POST /cia/quiz` with required path/query/body/form/header values
  Step 2: `GET /cia/quiz` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /cia/quiz` must include answers containing `Solution 3`, `Solution 1`, `Solution 4`, and `Solution 2` for questions 0 through 3. The core endpoint `GET /cia/quiz` reads the `guesses` state set by the core endpoint `POST /cia/quiz`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: One or more submitted answers do not contain the expected solution string.
  - Failure endpoint:
    `POST /cia/quiz`
  - Why this fails:
    The endpoint sets false for each wrong answer and returns failed unless all four are correct.
  - Intentionally violated constraints:
    Wrong quiz answers.

Endpoint coverage:
- Covers:
  `POST /cia/quiz`
  `GET /cia/quiz`
- Distinct meaning:
  Submits the quiz and retrieves stored answer status.

### Function 36: bypass account verification questions

Function name:
bypass account verification questions

Core endpoint(s):
- `POST /auth-bypass/verify-account`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API verifies the account and stores the verified account id in the lesson session.
- Invocation:
  Step 1: `POST /auth-bypass/verify-account` with required path/query/body/form/header values
- Constraints:
  `userId` is required by Swagger but not actually used by `verifyAccount` for lookup. The request must include exactly two parameters whose names contain `secQuestion`. To avoid the “cheated” branch, use alternate question names rather than both exact `secQuestion0` and `secQuestion1` with exact answers.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Exact known answers are submitted for both exact keys.
  - Failure endpoint:
    `POST /auth-bypass/verify-account`
  - Why this fails:
    `didUserLikelylCheat` detects the direct answers and returns cheated feedback.
  - Intentionally violated constraints:
    Used exact expected keys and values.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The number of submitted security-question parameters is not exactly two, or one known exact key has a wrong value.
  - Failure endpoint:
    `POST /auth-bypass/verify-account`
  - Why this fails:
    `verifyAccount` rejects missing question count or wrong exact-key answers.
  - Intentionally violated constraints:
    Wrong submitted question set.

Endpoint coverage:
- Covers:
  `POST /auth-bypass/verify-account`
- Distinct meaning:
  Verifies an account through vulnerable security-question handling.

### Function 37: list access-control users

Function name:
list access-control users

Core endpoint(s):
- `GET /access-control/users`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns users with hashes generated using the simple salt.
- Invocation:
  Step 1: `GET /access-control/users` with required path/query/body/form/header values
- Constraints:
  No setup is required because the users table is seeded.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /access-control/users`
  - Why this fails:
    Normal execution returns the seeded users.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /access-control/users`
- Distinct meaning:
  Lists users without the admin check.

### Function 38: add access-control user

Function name:
add access-control user

Core endpoint(s):
- `POST /access-control/users`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API inserts a new access-control user and returns that user.
- Invocation:
  Step 1: `POST /access-control/users` with required path/query/body/form/header values
- Constraints:
  The JSON body maps to `User` with `username`, `password`, and `admin`. The same add method is also exposed at `/access-control/users-admin-fix`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Insert fails, for example because the body cannot be saved.
  - Failure endpoint:
    `POST /access-control/users`
  - Why this fails:
    The repository exception is caught and the endpoint returns `null`.
  - Intentionally violated constraints:
    Invalid or unsavable user body.

Endpoint coverage:
- Covers:
  `POST /access-control/users`
- Distinct meaning:
  Creates an access-control user; there is no additional admin check on either POST path.

### Function 39: list users through admin-fixed endpoint

Function name:
list users through admin-fixed endpoint

Core endpoint(s):
- `GET /access-control/users-admin-fix`

Preconditions:
- A prerequisite state or response value produced by `POST /access-control/users-admin-fix` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /access-control/users-admin-fix` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API returns users with hashes generated using the admin salt.
- Invocation:
  Step 1: `GET /access-control/users-admin-fix` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /access-control/users-admin-fix` is one documented way to create a row for the current WebGoat username with `admin=true`. The core endpoint `GET /access-control/users-admin-fix` checks `@CurrentUsername` against the repository and succeeds only if that user is admin.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Current username is missing from the access-control table or is not admin.
  - Failure endpoint:
    `GET /access-control/users-admin-fix`
  - Why this fails:
    The endpoint returns HTTP 403 when `currentUser` is null or not admin.
  - Intentionally violated constraints:
    Omitted or non-admin current-user row.

Endpoint coverage:
- Covers:
  `GET /access-control/users-admin-fix`
- Distinct meaning:
  POST can establish the admin row; GET enforces it.

### Function 40: submit simple user hash

Function name:
submit simple user hash

Core endpoint(s):
- `POST /access-control/user-hash`

Preconditions:
- A prerequisite state or response value produced by `GET /access-control/users` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /access-control/users` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts Jerry’s hash generated with the simple salt.
- Invocation:
  Step 1: `POST /access-control/user-hash` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /access-control/users` exposes Jerry’s simple-salt `userHash`. The core endpoint `POST /access-control/user-hash` must submit that exact value as `userHash`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Submitted hash is not Jerry’s simple-salt hash.
  - Failure endpoint:
    `POST /access-control/user-hash`
  - Why this fails:
    The endpoint compares against `new DisplayUser(Jerry, PASSWORD_SALT_SIMPLE).userHash`.
  - Intentionally violated constraints:
    Wrong hash or wrong user’s hash.

Endpoint coverage:
- Covers:
  `POST /access-control/user-hash`
- Distinct meaning:
  Uses the exposed simple-salt user list to solve the hash check.

### Function 41: submit admin-salt user hash

Function name:
submit admin-salt user hash

Core endpoint(s):
- `POST /access-control/user-hash-fix`

Preconditions:
- A prerequisite state or response value produced by `POST /access-control/users-admin-fix` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /access-control/users-admin-fix` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.
- A prerequisite state or response value produced by `GET /access-control/users-admin-fix` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /access-control/users-admin-fix` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts Jerry’s hash generated with the admin salt.
- Invocation:
  Step 1: `POST /access-control/user-hash-fix` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /access-control/users-admin-fix` makes the current WebGoat username an admin in the access-control table. The precondition endpoint `GET /access-control/users-admin-fix` exposes Jerry’s admin-salt hash. The core endpoint `POST /access-control/user-hash-fix` must submit that exact hash as `userHash`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Admin-fixed user list cannot be accessed.
  - Failure endpoint:
    `GET /access-control/users-admin-fix`
  - Why this fails:
    The current user is not an admin access-control user.
  - Intentionally violated constraints:
    Omitted admin setup.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Submitted hash is not Jerry’s admin-salt hash.
  - Failure endpoint:
    `POST /access-control/user-hash-fix`
  - Why this fails:
    The endpoint compares against `PASSWORD_SALT_ADMIN`.
  - Intentionally violated constraints:
    Wrong hash.

Endpoint coverage:
- Covers:
  `POST /access-control/user-hash-fix`
- Distinct meaning:
  Uses the admin-protected list to solve the stronger hash check.

### Function 42: submit hidden-menu names

Function name:
submit hidden-menu names

Core endpoint(s):
- `POST /access-control/hidden-menu`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the hidden menu names.
- Invocation:
  Step 1: `POST /access-control/hidden-menu` with required path/query/body/form/header values
- Constraints:
  `hiddenMenu1=Users` and `hiddenMenu2=Config`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Values are reversed.
  - Failure endpoint:
    `POST /access-control/hidden-menu`
  - Why this fails:
    Reversed values are treated as close but not successful.
  - Intentionally violated constraints:
    `hiddenMenu1=Config`, `hiddenMenu2=Users`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Values are not the expected pair.
  - Failure endpoint:
    `POST /access-control/hidden-menu`
  - Why this fails:
    The endpoint checks exact strings.
  - Intentionally violated constraints:
    Wrong menu names.

Endpoint coverage:
- Covers:
  `POST /access-control/hidden-menu`
- Distinct meaning:
  Validates discovery of hidden menu labels.

### Function 43: send and verify WebWolf mail code

Function name:
send and verify WebWolf mail code

Core endpoint(s):
- `POST /WebWolf/mail`

Preconditions:
- A prerequisite state or response value produced by `POST /WebWolf/mail/send` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /WebWolf/mail/send` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API sends a mail containing the reversed username and then accepts that code.
- Invocation:
  Step 1: `POST /WebWolf/mail` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /WebWolf/mail/send` `email` local part must equal the current WebGoat username. The mail content contains `reverse(username)`. The core endpoint `POST /WebWolf/mail` must submit `uniqueCode=reverse(username)`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Email local part does not match the current username.
  - Failure endpoint:
    `POST /WebWolf/mail/send`
  - Why this fails:
    The endpoint returns mismatch information and does not send the intended mail.
  - Intentionally violated constraints:
    Mismatched email local part.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Submitted code is not the reversed username.
  - Failure endpoint:
    `POST /WebWolf/mail`
  - Why this fails:
    The code is compared to `StringUtils.reverse(username)`.
  - Intentionally violated constraints:
    Wrong `uniqueCode`.

Endpoint coverage:
- Covers:
  `POST /WebWolf/mail`
- Distinct meaning:
  Sends and validates the WebWolf email code.

### Function 44: verify WebWolf landing code

Function name:
verify WebWolf landing code

Core endpoint(s):
- `POST /WebWolf/landing`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the landing-page code.
- Invocation:
  Step 1: `POST /WebWolf/landing` with required path/query/body/form/header values
- Constraints:
  `uniqueCode` must equal the current username reversed. The source has a `GET /WebWolf/landing/password-reset` helper that exposes this code, but that helper is not present in the Swagger file.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `uniqueCode` is not the reversed current username.
  - Failure endpoint:
    `POST /WebWolf/landing`
  - Why this fails:
    The endpoint performs a direct comparison with `reverse(username)`.
  - Intentionally violated constraints:
    Wrong landing code.

Endpoint coverage:
- Covers:
  `POST /WebWolf/landing`
- Distinct meaning:
  Confirms a code received through the WebWolf landing flow.

### Function 45: exploit vulnerable XML component

Function name:
exploit vulnerable XML component

Core endpoint(s):
- `POST /VulnerableComponents/attack1`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API parses the submitted XStream XML and marks the lesson solved when parsing produces a non-`ContactImpl` object or triggers an exception during contact access.
- Invocation:
  Step 1: `POST /VulnerableComponents/attack1` with required path/query/body/form/header values
- Constraints:
  `payload` must be XML acceptable to XStream and must not simply deserialize to the normal `ContactImpl` object.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: XML parsing fails.
  - Failure endpoint:
    `POST /VulnerableComponents/attack1`
  - Why this fails:
    XStream throws and the endpoint returns failure feedback.
  - Intentionally violated constraints:
    Invalid XStream XML.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Payload deserializes to an ordinary `ContactImpl`.
  - Failure endpoint:
    `POST /VulnerableComponents/attack1`
  - Why this fails:
    The endpoint treats normal contact parsing as not solved.
  - Intentionally violated constraints:
    Non-exploit contact payload.

Endpoint coverage:
- Covers:
  `POST /VulnerableComponents/attack1`
- Distinct meaning:
  Exercises the vulnerable XStream parsing function.

### Function 46: run SQL injection introduction queries

Function name:
run SQL injection introduction queries

Core endpoint(s):
- `POST /SqlInjection/attack2`
- `POST /SqlInjection/attack3`
- `POST /SqlInjection/attack4`
- `POST /SqlInjection/attack5`
- `POST /SqlInjection/attack8`
- `POST /SqlInjection/attack9`
- `POST /SqlInjection/attack10`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API executes submitted SQL and marks specific introductory SQL injection lessons solved when their database-side checks pass.
- Invocation:
  Step 1: `POST /SqlInjection/attack2` with required path/query/body/form/header values
  Step 2: `POST /SqlInjection/attack3` with required path/query/body/form/header values
  Step 3: `POST /SqlInjection/attack4` with required path/query/body/form/header values
  Step 4: `POST /SqlInjection/attack5` with required path/query/body/form/header values
  Step 5: `POST /SqlInjection/attack8` with required path/query/body/form/header values
  Step 6: `POST /SqlInjection/attack9` with required path/query/body/form/header values
  Step 7: `POST /SqlInjection/attack10` with required path/query/body/form/header values
- Constraints:
  Each endpoint is independently executable; the sequence shown covers the family, not a required workflow across all seven. Successful inputs must respectively: return a Marketing employee, change Tobi Barnett’s department to Sales, add a `phone` column, grant rights to `UNAUTHORIZED_USER`, return more than one employee row, raise John Smith’s salary above the old max without changing other salaries, and make `access_log` unavailable.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: SQL does not produce the endpoint-specific expected database state.
  - Failure endpoint:
    `POST /SqlInjection/attack2`
  - Why this fails:
    The result department is not Marketing.
  - Intentionally violated constraints:
    Wrong query effect.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: SQL syntax or database execution fails.
  - Failure endpoint:
    `POST /SqlInjection/attack3`
  - Why this fails:
    The implementation catches `SQLException` and returns failure output.
  - Intentionally violated constraints:
    Invalid SQL.

Endpoint coverage:
- Covers:
  `POST /SqlInjection/attack2`
  `POST /SqlInjection/attack3`
  `POST /SqlInjection/attack4`
  `POST /SqlInjection/attack5`
  `POST /SqlInjection/attack8`
  `POST /SqlInjection/attack9`
  `POST /SqlInjection/attack10`
- Distinct meaning:
  Each endpoint executes SQL for a different confidentiality, integrity, availability, or privilege objective.

### Function 47: solve SQL injection assignment 5a

Function name:
solve SQL injection assignment 5a

Core endpoint(s):
- `POST /SqlInjection/assignment5a`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns multiple `user_data` rows and marks the assignment solved.
- Invocation:
  Step 1: `POST /SqlInjection/assignment5a` with required path/query/body/form/header values
- Constraints:
  `account`, `operator`, and `injection` are concatenated into the `last_name` predicate. The resulting query must return at least six rows.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The injected predicate returns no rows or fewer than six rows.
  - Failure endpoint:
    `POST /SqlInjection/assignment5a`
  - Why this fails:
    The endpoint checks `results.getRow() >= 6`.
  - Intentionally violated constraints:
    Insufficient query results.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: SQL execution fails.
  - Failure endpoint:
    `POST /SqlInjection/assignment5a`
  - Why this fails:
    The implementation catches `SQLException`.
  - Intentionally violated constraints:
    Invalid query construction.

Endpoint coverage:
- Covers:
  `POST /SqlInjection/assignment5a`
- Distinct meaning:
  Demonstrates string-based SQL injection in a name predicate.

### Function 48: solve SQL injection assignment 5b

Function name:
solve SQL injection assignment 5b

Core endpoint(s):
- `POST /SqlInjection/assignment5b`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns multiple `user_data` rows and marks the assignment solved.
- Invocation:
  Step 1: `POST /SqlInjection/assignment5b` with required path/query/body/form/header values
- Constraints:
  `login_count` must parse as an integer because it is bound as a prepared-statement parameter. `userid` is concatenated into SQL and must cause at least six rows to return.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `login_count` is not numeric.
  - Failure endpoint:
    `POST /SqlInjection/assignment5b`
  - Why this fails:
    `Integer.parseInt(login_count)` fails before query execution.
  - Intentionally violated constraints:
    Non-numeric `login_count`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Query returns no rows or fewer than six rows.
  - Failure endpoint:
    `POST /SqlInjection/assignment5b`
  - Why this fails:
    The endpoint requires at least six result rows.
  - Intentionally violated constraints:
    Insufficient injected `userid` result set.

Endpoint coverage:
- Covers:
  `POST /SqlInjection/assignment5b`
- Distinct meaning:
  Demonstrates injection in the unbound `userid` portion while `login_count` is bound.

### Function 49: perform advanced SQL injection extraction

Function name:
perform advanced SQL injection extraction

Core endpoint(s):
- `POST /SqlInjectionAdvanced/attack6a`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns output containing Dave’s password and marks the advanced injection lesson solved.
- Invocation:
  Step 1: `POST /SqlInjectionAdvanced/attack6a` with required path/query/body/form/header values
- Constraints:
  `userid_6a` is embedded in a `last_name` query and must produce output containing both `dave` and `passW0rD`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Query returns no rows.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/attack6a`
  - Why this fails:
    The endpoint checks for a first result row before validating output.
  - Intentionally violated constraints:
    No matching/extracted rows.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Output does not contain Dave’s username and password.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/attack6a`
  - Why this fails:
    The final verification checks for both strings.
  - Intentionally violated constraints:
    Missing extracted credentials.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionAdvanced/attack6a`
- Distinct meaning:
  Extracts credentials through advanced SQL injection.

### Function 50: submit extracted Dave password

Function name:
submit extracted Dave password

Core endpoint(s):
- `POST /SqlInjectionAdvanced/attack6b`

Preconditions:
- A prerequisite state or response value produced by `POST /SqlInjectionAdvanced/attack6a` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /SqlInjectionAdvanced/attack6a` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts Dave’s password from `user_system_data`.
- Invocation:
  Step 1: `POST /SqlInjectionAdvanced/attack6b` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /SqlInjectionAdvanced/attack6a` is the documented way to extract the password. The core endpoint `POST /SqlInjectionAdvanced/attack6b` must submit `userid_6b` equal to the database password for user `dave`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `userid_6b` does not equal Dave’s password.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/attack6b`
  - Why this fails:
    The endpoint compares the submitted value to `getPassword()`.
  - Intentionally violated constraints:
    Wrong extracted password.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionAdvanced/attack6b`
- Distinct meaning:
  Confirms the credential extracted in the previous advanced SQL function.

### Function 51: submit SQL advanced quiz and read results

Function name:
submit SQL advanced quiz and read results

Core endpoint(s):
- `POST /SqlInjectionAdvanced/quiz`
- `GET /SqlInjectionAdvanced/quiz`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API records all advanced SQL quiz answers as correct and returns the correctness array.
- Invocation:
  Step 1: `POST /SqlInjectionAdvanced/quiz` with required path/query/body/form/header values
  Step 2: `GET /SqlInjectionAdvanced/quiz` with required path/query/body/form/header values
- Constraints:
  Answers must contain `Solution 4`, `Solution 3`, `Solution 2`, `Solution 3`, and `Solution 4` for questions 0 through 4.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Any answer is wrong.
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/quiz`
  - Why this fails:
    The endpoint succeeds only when all five answers match.
  - Intentionally violated constraints:
    Wrong quiz answer.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionAdvanced/quiz`
  `GET /SqlInjectionAdvanced/quiz`
- Distinct meaning:
  Submits and reads advanced SQL quiz status.

### Function 52: solve SQL mitigation fill-in task

Function name:
solve SQL mitigation fill-in task

Core endpoint(s):
- `POST /SqlInjectionMitigations/attack10a`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the prepared-statement code fragments.
- Invocation:
  Step 1: `POST /SqlInjectionMitigations/attack10a` with required path/query/body/form/header values
- Constraints:
  Fields 1 through 7 must contain, in order: `getConnection`, `PreparedStatement`, `prepareStatement`, `?`, `?`, `setString`, and `setString`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Any field lacks the expected fragment at its position.
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10a`
  - Why this fails:
    The loop fails immediately on the first mismatch.
  - Intentionally violated constraints:
    Wrong field content or order.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionMitigations/attack10a`
- Distinct meaning:
  Validates prepared-statement code fragments.

### Function 53: submit compiling SQL mitigation code

Function name:
submit compiling SQL mitigation code

Core endpoint(s):
- `POST /SqlInjectionMitigations/attack10b`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts submitted Java code that uses a prepared statement safely and compiles.
- Invocation:
  Step 1: `POST /SqlInjectionMitigations/attack10b` with required path/query/body/form/header values
- Constraints:
  `editor` must contain connection setup, `PreparedStatement`, a `=?` placeholder, `setString`, and `execute` or `executeUpdate`. The stripped code must compile inside the generated wrapper class.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `editor` is empty.
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10b`
  - Why this fails:
    Empty code is rejected before compilation.
  - Intentionally violated constraints:
    Empty code.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Code contains required fragments but does not compile.
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10b`
  - Why this fails:
    Compiler diagnostics are returned.
  - Intentionally violated constraints:
    Non-compiling Java.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Code compiles but lacks required prepared-statement patterns.
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10b`
  - Why this fails:
    Regex checks do not all pass.
  - Intentionally violated constraints:
    Missing required mitigation constructs.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionMitigations/attack10b`
- Distinct meaning:
  Validates a complete mitigation code snippet.

### Function 54: verify production server IP

Function name:
verify production server IP

Core endpoint(s):
- `POST /SqlInjectionMitigations/attack12a`

Preconditions:
- A prerequisite state or response value produced by `GET /SqlInjectionMitigations/servers` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /SqlInjectionMitigations/servers` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the IP for `webgoat-prd`.
- Invocation:
  Step 1: `POST /SqlInjectionMitigations/attack12a` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /SqlInjectionMitigations/servers` lists non-out-of-order servers only, so it may not expose `webgoat-prd` because source data marks that server out of order. The core endpoint `POST /SqlInjectionMitigations/attack12a` succeeds only when `ip=104.130.219.202` and hostname `webgoat-prd` exists in the database.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Submitted IP does not belong to `webgoat-prd`.
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack12a`
  - Why this fails:
    The prepared statement finds no row for `ip` and `hostname=webgoat-prd`.
  - Intentionally violated constraints:
    Wrong IP.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `column` in Step 1 is invalid SQL.
  - Failure endpoint:
    `GET /SqlInjectionMitigations/servers`
  - Why this fails:
    The `column` parameter is concatenated into `order by`.
  - Intentionally violated constraints:
    Invalid sort expression.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionMitigations/attack12a`
- Distinct meaning:
  Lists sortable servers and validates the production server IP.

### Function 55: bypass space-only input validation

Function name:
bypass space-only input validation

Core endpoint(s):
- `POST /SqlOnlyInputValidation/attack`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API forwards the input to the advanced SQL extraction logic and returns that result under the mitigation assignment.
- Invocation:
  Step 1: `POST /SqlOnlyInputValidation/attack` with required path/query/body/form/header values
- Constraints:
  `userid_sql_only_input_validation` must not contain a literal space. After that check, the value must still make the delegated `SqlInjectionLesson6a.injectableQuery` succeed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Input contains a literal space.
  - Failure endpoint:
    `POST /SqlOnlyInputValidation/attack`
  - Why this fails:
    The endpoint rejects any input containing a space before delegation.
  - Intentionally violated constraints:
    Space in input.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Delegated advanced SQL injection does not extract the expected output.
  - Failure endpoint:
    `POST /SqlOnlyInputValidation/attack`
  - Why this fails:
    The wrapped `SqlInjectionLesson6a` result remains failed.
  - Intentionally violated constraints:
    Injection payload does not solve advanced extraction.

Endpoint coverage:
- Covers:
  `POST /SqlOnlyInputValidation/attack`
- Distinct meaning:
  Demonstrates bypass of simplistic space validation.

### Function 56: bypass keyword-stripping input validation

Function name:
bypass keyword-stripping input validation

Core endpoint(s):
- `POST /SqlOnlyInputValidationOnKeywords/attack`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API strips `FROM` and `SELECT`, then forwards the transformed input to advanced SQL extraction and returns that result.
- Invocation:
  Step 1: `POST /SqlOnlyInputValidationOnKeywords/attack` with required path/query/body/form/header values
- Constraints:
  The submitted `userid_sql_only_input_validation_on_keywords` is uppercased, has `FROM` and `SELECT` removed, and must not contain a space after that transformation. The transformed value must still solve the delegated advanced SQL injection.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Transformed input contains a space.
  - Failure endpoint:
    `POST /SqlOnlyInputValidationOnKeywords/attack`
  - Why this fails:
    The endpoint rejects spaces after keyword removal.
  - Intentionally violated constraints:
    Space remains after transformation.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Delegated advanced SQL extraction fails.
  - Failure endpoint:
    `POST /SqlOnlyInputValidationOnKeywords/attack`
  - Why this fails:
    The wrapped `SqlInjectionLesson6a` result remains failed.
  - Intentionally violated constraints:
    Payload no longer solves extraction after keyword stripping.

Endpoint coverage:
- Covers:
  `POST /SqlOnlyInputValidationOnKeywords/attack`
- Distinct meaning:
  Demonstrates bypass of keyword-stripping validation.

### Function 57: log in with spoofable cookie

Function name:
log in with spoofable cookie

Core endpoint(s):
- `POST /SpoofCookie/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API marks the spoof-cookie lesson solved when the `spoof_auth` cookie decodes to `tom`.
- Invocation:
  Step 1: `POST /SpoofCookie/login` with required path/query/body/form/header values
  Step 2: `POST /SpoofCookie/login` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /SpoofCookie/login` can authenticate a known non-target user, such as `webgoat/webgoat`, and returns a cookie format in the response output. The core endpoint `POST /SpoofCookie/login` must send a forged `spoof_auth` cookie that decodes to `tom`. Direct Tom credential login returns cheating information, not success.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie is absent and credentials are wrong.
  - Failure endpoint:
    `POST /SpoofCookie/login`
  - Why this fails:
    The credentials flow returns wrong-login information.
  - Intentionally violated constraints:
    Wrong username/password and no cookie.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie decodes to a known user other than `tom`.
  - Failure endpoint:
    `POST /SpoofCookie/login`
  - Why this fails:
    Cookie login for non-target users returns failure with cookie details.
  - Intentionally violated constraints:
    Cookie username is not `tom`.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie cannot be decoded.
  - Failure endpoint:
    `POST /SpoofCookie/login`
  - Why this fails:
    Decoder throws and the endpoint returns failed output.
  - Intentionally violated constraints:
    Invalid cookie encoding.

Endpoint coverage:
- Covers:
  `POST /SpoofCookie/login`
- Distinct meaning:
  Supports credential login for cookie discovery and cookie login for spoofing.

### Function 58: clear spoof cookie

Function name:
clear spoof cookie

Core endpoint(s):
- `GET /SpoofCookie/cleanup`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API expires the `spoof_auth` cookie.
- Invocation:
  Step 1: `GET /SpoofCookie/cleanup` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required; it always adds an expired cookie.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /SpoofCookie/cleanup`
  - Why this fails:
    Normal execution always writes the expired cookie.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /SpoofCookie/cleanup`
- Distinct meaning:
  Auxiliary cleanup for the spoof-cookie workflow.

### Function 59: evaluate password strength

Function name:
evaluate password strength

Core endpoint(s):
- `POST /SecurePasswords/assignment`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns password-strength output and marks the assignment solved when the score is 4.
- Invocation:
  Step 1: `POST /SecurePasswords/assignment` with required path/query/body/form/header values
- Constraints:
  `password` must score at least 4 according to zxcvbn.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Password score is 0 through 3.
  - Failure endpoint:
    `POST /SecurePasswords/assignment`
  - Why this fails:
    The endpoint returns the strength report but marks the lesson failed.
  - Intentionally violated constraints:
    Weak password.

Endpoint coverage:
- Covers:
  `POST /SecurePasswords/assignment`
- Distinct meaning:
  Evaluates and gates password strength.

### Function 60: solve SSRF local image task

Function name:
solve SSRF local image task

Core endpoint(s):
- `POST /SSRF/task1`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the Jerry image and marks the task solved.
- Invocation:
  Step 1: `POST /SSRF/task1` with required path/query/body/form/header values
- Constraints:
  `url=images/jerry.png`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `url=images/tom.png`.
  - Failure endpoint:
    `POST /SSRF/task1`
  - Why this fails:
    The endpoint returns Tom image feedback as failure.
  - Intentionally violated constraints:
    Wrong image target.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: URL is neither Tom nor Jerry.
  - Failure endpoint:
    `POST /SSRF/task1`
  - Why this fails:
    The endpoint returns the fallback image and failure.
  - Intentionally violated constraints:
    Unknown URL.

Endpoint coverage:
- Covers:
  `POST /SSRF/task1`
- Distinct meaning:
  Validates selection of the internal Jerry resource.

### Function 61: solve SSRF external URL task

Function name:
solve SSRF external URL task

Core endpoint(s):
- `POST /SSRF/task2`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API fetches `http://ifconfig.pro` or uses fallback success text if the external site is unavailable.
- Invocation:
  Step 1: `POST /SSRF/task2` with required path/query/body/form/header values
- Constraints:
  `url=http://ifconfig.pro`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: URL is not exactly `http://ifconfig.pro`.
  - Failure endpoint:
    `POST /SSRF/task2`
  - Why this fails:
    The endpoint returns fallback failure output for all other URLs.
  - Intentionally violated constraints:
    Wrong URL.

Endpoint coverage:
- Covers:
  `POST /SSRF/task2`
- Distinct meaning:
  Validates a specific external SSRF target.

### Function 62: upload profile file with path traversal

Function name:
upload profile file with path traversal

Core endpoint(s):
- `POST /PathTraversal/profile-upload`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API writes the uploaded file and marks the lesson solved if the path escapes into the `PathTraversal` parent directory.
- Invocation:
  Step 1: `POST /PathTraversal/profile-upload` with required path/query/body/form/header values
- Constraints:
  Multipart parameter `uploadedFile` must be non-empty. `fullName` must be non-empty and crafted so the resulting canonical parent differs from the user upload directory and ends with `PathTraversal`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Uploaded file is empty or `fullName` is empty.
  - Failure endpoint:
    `POST /PathTraversal/profile-upload`
  - Why this fails:
    Base upload validation rejects empty file or name.
  - Intentionally violated constraints:
    Missing multipart file or filename.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Upload stays inside the user directory.
  - Failure endpoint:
    `POST /PathTraversal/profile-upload`
  - Why this fails:
    The endpoint returns an informational update, not solved.
  - Intentionally violated constraints:
    No traversal attempt.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/profile-upload`
- Distinct meaning:
  Uploads a profile image and evaluates path traversal.

### Function 63: retrieve profile picture

Function name:
retrieve profile picture

Core endpoint(s):
- `GET /PathTraversal/profile-picture`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the current user’s uploaded image if present, otherwise the default image.
- Invocation:
  Step 1: `GET /PathTraversal/profile-picture` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required because the default image is returned when no uploaded picture exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /PathTraversal/profile-picture`
  - Why this fails:
    Missing files fall back to the default image.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /PathTraversal/profile-picture`
- Distinct meaning:
  Reads the path traversal profile image.

### Function 64: upload profile file with traversal removed

Function name:
upload profile file with traversal removed

Core endpoint(s):
- `POST /PathTraversal/profile-upload-fix`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API stores the uploaded file after removing `../` from the supplied name.
- Invocation:
  Step 1: `POST /PathTraversal/profile-upload-fix` with required path/query/body/form/header values
- Constraints:
  Multipart parameter `uploadedFileFix` must be non-empty. `fullNameFix` is sanitized by replacing `../` with an empty string before storage.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Uploaded file or sanitized name is empty.
  - Failure endpoint:
    `POST /PathTraversal/profile-upload-fix`
  - Why this fails:
    Base upload validation rejects it.
  - Intentionally violated constraints:
    Missing file or name.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/profile-upload-fix`
- Distinct meaning:
  Demonstrates the fixed upload path handling.

### Function 65: retrieve fixed profile picture

Function name:
retrieve fixed profile picture

Core endpoint(s):
- `GET /PathTraversal/profile-picture-fix`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the fixed-upload profile image or default image.
- Invocation:
  Step 1: `GET /PathTraversal/profile-picture-fix` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required because the default image is returned when no image exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /PathTraversal/profile-picture-fix`
  - Why this fails:
    Missing files fall back to the default image.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /PathTraversal/profile-picture-fix`
- Distinct meaning:
  Reads the fixed upload profile image.

### Function 66: upload profile file using original filename

Function name:
upload profile file using original filename

Core endpoint(s):
- `POST /PathTraversal/profile-upload-remove-user-input`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API stores the uploaded file using the multipart original filename.
- Invocation:
  Step 1: `POST /PathTraversal/profile-upload-remove-user-input` with required path/query/body/form/header values
- Constraints:
  Multipart parameter `uploadedFileRemoveUserInput` must be non-empty. The stored name comes from `file.getOriginalFilename()`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Uploaded file or original filename is empty.
  - Failure endpoint:
    `POST /PathTraversal/profile-upload-remove-user-input`
  - Why this fails:
    Base upload validation rejects it.
  - Intentionally violated constraints:
    Missing file or filename.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/profile-upload-remove-user-input`
- Distinct meaning:
  Demonstrates removing user-controlled filename input.

### Function 67: retrieve random or targeted cat picture

Function name:
retrieve random or targeted cat picture

Core endpoint(s):
- `GET /PathTraversal/random-picture`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a random cat picture or the requested cat picture.
- Invocation:
  Step 1: `GET /PathTraversal/random-picture` with required path/query/body/form/header values
- Constraints:
  Query parameter `id` is optional. If present, the raw query string must not contain `..` or `/`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Query string contains `..` or `/`.
  - Failure endpoint:
    `GET /PathTraversal/random-picture`
  - Why this fails:
    The endpoint returns HTTP 400 with illegal-character text.
  - Intentionally violated constraints:
    Path traversal characters in query.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Requested file does not exist.
  - Failure endpoint:
    `GET /PathTraversal/random-picture`
  - Why this fails:
    The endpoint returns HTTP 404 and lists files in the parent directory.
  - Intentionally violated constraints:
    Unknown image id.

Endpoint coverage:
- Covers:
  `GET /PathTraversal/random-picture`
- Distinct meaning:
  Reads cat images and exposes path traversal lesson feedback.

### Function 68: submit path traversal secret hash

Function name:
submit path traversal secret hash

Core endpoint(s):
- `POST /PathTraversal/random`

Preconditions:
- A prerequisite state or response value produced by `GET /PathTraversal/random-picture` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /PathTraversal/random-picture` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the SHA-512 hash of the current username.
- Invocation:
  Step 1: `POST /PathTraversal/random` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /PathTraversal/random-picture` can expose the secret-file hint through the retrieval lesson. The core endpoint `POST /PathTraversal/random` must submit `secret` equal to `Sha512DigestUtils.shaHex(currentUsername)`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `secret` does not match the current username hash.
  - Failure endpoint:
    `POST /PathTraversal/random`
  - Why this fails:
    The endpoint compares `secret` to the SHA-512 hex of the username.
  - Intentionally violated constraints:
    Wrong or missing hash.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/random`
- Distinct meaning:
  Confirms the secret discovered through the random-picture traversal lesson.

### Function 69: exploit zip slip upload

Function name:
exploit zip slip upload

Core endpoint(s):
- `POST /PathTraversal/zip-slip`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API extracts the uploaded ZIP and marks the lesson solved if the current profile image changes.
- Invocation:
  Step 1: `POST /PathTraversal/zip-slip` with required path/query/body/form/header values
- Constraints:
  Multipart parameter `uploadedFileZipSlip` must have an original filename ending with `.zip`. The ZIP entries must write a new profile image so the before/after image bytes differ.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Uploaded file name does not end with `.zip`.
  - Failure endpoint:
    `POST /PathTraversal/zip-slip`
  - Why this fails:
    The endpoint rejects non-ZIP uploads.
  - Intentionally violated constraints:
    Non-ZIP upload.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Extraction does not change the profile image.
  - Failure endpoint:
    `POST /PathTraversal/zip-slip`
  - Why this fails:
    `isSolved` compares image bytes and returns failed if unchanged.
  - Intentionally violated constraints:
    ZIP does not affect profile image.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/zip-slip`
- Distinct meaning:
  Uploads and extracts a ZIP for Zip Slip function.

### Function 70: retrieve zip-slip profile picture

Function name:
retrieve zip-slip profile picture

Core endpoint(s):
- `GET /PathTraversal/zip-slip/`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the current profile picture for the zip-slip lesson.
- Invocation:
  Step 1: `GET /PathTraversal/zip-slip/` with required path/query/body/form/header values
- Constraints:
  No prerequisite is required because a default image is returned when no uploaded picture exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /PathTraversal/zip-slip/`
  - Why this fails:
    Missing files fall back to the default image.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /PathTraversal/zip-slip/`
- Distinct meaning:
  Reads the zip-slip lesson image state.

### Function 71: perform simple password reset by email

Function name:
perform simple password reset by email

Core endpoint(s):
- `POST /PasswordReset/simple-mail`

Preconditions:
- A prerequisite state or response value produced by `POST /PasswordReset/simple-mail/reset` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /PasswordReset/simple-mail/reset` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API sends a reset email and accepts the new password.
- Invocation:
  Step 1: `POST /PasswordReset/simple-mail` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /PasswordReset/simple-mail/reset` `emailReset` local part must equal the current username; it sends a password equal to the reversed username. The core endpoint `POST /PasswordReset/simple-mail` must use `email` for the current user and `password=reverse(username)`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Reset email local part does not match current username.
  - Failure endpoint:
    `POST /PasswordReset/simple-mail/reset`
  - Why this fails:
    The endpoint returns mismatch information instead of sending the intended mail.
  - Intentionally violated constraints:
    Wrong reset email.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Login password is not the reversed username.
  - Failure endpoint:
    `POST /PasswordReset/simple-mail`
  - Why this fails:
    The login endpoint compares password to `reverse(username)`.
  - Intentionally violated constraints:
    Wrong reset password.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/simple-mail`
- Distinct meaning:
  Sends and uses a simple password reset email.

### Function 72: create Tom password reset link and log in

Function name:
create Tom password reset link and log in

Core endpoint(s):
- `POST /PasswordReset/reset/login`

Preconditions:
- A prerequisite state or response value produced by `POST /PasswordReset/ForgotPassword/create-password-reset-link` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /PasswordReset/ForgotPassword/create-password-reset-link` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API creates a reset link for Tom and later accepts Tom’s changed password.
- Invocation:
  Step 1: `POST /PasswordReset/reset/login` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /PasswordReset/ForgotPassword/create-password-reset-link` must use `email=tom@webgoat-cloud.org` and a `Host` header containing the configured WebWolf host and port to store the generated link under the current username. Source code shows the actual password change is performed by `POST /PasswordReset/reset/change-password`, but that endpoint is not present in `webgoat.json`; therefore the documented Swagger sequence alone cannot set `usersToTomPassword`. The core endpoint `POST /PasswordReset/reset/login` must submit Tom’s email and the password set through that missing documented step.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Step 1 uses a normal host or non-Tom email.
  - Failure endpoint:
    `POST /PasswordReset/reset/login`
  - Why this fails:
    The generated Tom reset link is not associated with the current user, so Tom’s password remains the default impossible value.
  - Intentionally violated constraints:
    Wrong email or missing WebWolf Host header.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Step 2 uses an email other than Tom’s or a password that was not set.
  - Failure endpoint:
    `POST /PasswordReset/reset/login`
  - Why this fails:
    The login endpoint only accepts `tom@webgoat-cloud.org` and the stored per-user Tom password.
  - Intentionally violated constraints:
    Wrong email or password.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/reset/login`
- Distinct meaning:
  Creates reset-link state and attempts Tom password login. There is an OpenAPI/source discrepancy because a required password-change endpoint exists in source but is absent from Swagger.

### Function 73: answer password reset security question

Function name:
answer password reset security question

Core endpoint(s):
- `POST /PasswordReset/questions`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the known color answer for a non-WebGoat user.
- Invocation:
  Step 1: `POST /PasswordReset/questions` with required path/query/body/form/header values
- Constraints:
  Form data must include `username` and `securityQuestion`. Valid pairs include `admin/green`, `jerry/orange`, `tom/purple`, and `larry/yellow`. `webgoat` is explicitly rejected.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `username=webgoat`.
  - Failure endpoint:
    `POST /PasswordReset/questions`
  - Why this fails:
    The endpoint has a specific wrong-user branch.
  - Intentionally violated constraints:
    Forbidden username.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Unknown username or wrong color.
  - Failure endpoint:
    `POST /PasswordReset/questions`
  - Why this fails:
    The username is absent from the color map or the answer does not match.
  - Intentionally violated constraints:
    Unknown/wrong security answer.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/questions`
- Distinct meaning:
  Validates known security-question answers.

### Function 74: classify weak security questions

Function name:
classify weak security questions

Core endpoint(s):
- `POST /PasswordReset/SecurityQuestions`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API eventually marks the assignment solved after enough known questions have been tried.
- Invocation:
  Step 1: `POST /PasswordReset/SecurityQuestions` with required path/query/body/form/header values
- Constraints:
  `question` must be one of the known weak security-question strings. Completion depends on `TriedQuestions.isComplete()`, which tracks repeated/required tried questions in lesson state.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Question is unknown.
  - Failure endpoint:
    `POST /PasswordReset/SecurityQuestions`
  - Why this fails:
    The endpoint returns informational output saying the question is unknown.
  - Intentionally violated constraints:
    Question not in the static map.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Known question is submitted before the tried-question state is complete.
  - Failure endpoint:
    `POST /PasswordReset/SecurityQuestions`
  - Why this fails:
    It records the question and returns informational feedback until the completion condition is met.
  - Intentionally violated constraints:
    Incomplete tried-question progression.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/SecurityQuestions`
- Distinct meaning:
  Records and evaluates weak security-question choices.

### Function 75: solve log spoofing

Function name:
solve log spoofing

Core endpoint(s):
- `POST /LogSpoofing/log-spoofing`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts a username containing a newline before `admin`.
- Invocation:
  Step 1: `POST /LogSpoofing/log-spoofing` with required path/query/body/form/header values
- Constraints:
  `username` must be non-empty, must not contain `<p>` or `<div>`, and after newline replacement must have `<br/>` before `admin`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username is empty or contains blocked tags.
  - Failure endpoint:
    `POST /LogSpoofing/log-spoofing`
  - Why this fails:
    Empty usernames and `<p>`/`<div>` payloads are rejected.
  - Intentionally violated constraints:
    Empty or blocked HTML payload.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Newline marker is absent or after `admin`.
  - Failure endpoint:
    `POST /LogSpoofing/log-spoofing`
  - Why this fails:
    The index check requires `<br/>` before `admin`.
  - Intentionally violated constraints:
    Wrong spoofing order.

Endpoint coverage:
- Covers:
  `POST /LogSpoofing/log-spoofing`
- Distinct meaning:
  Validates a log-spoofing username payload.

### Function 76: solve log bleeding

Function name:
solve log bleeding

Core endpoint(s):
- `POST /LogSpoofing/log-bleeding`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the runtime admin password.
- Invocation:
  Step 1: `POST /LogSpoofing/log-bleeding` with required path/query/body/form/header values
- Constraints:
  `username=Admin`. `password` must equal the random UUID generated in the controller constructor and logged Base64-encoded at startup; no documented endpoint produces this value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username or password is empty.
  - Failure endpoint:
    `POST /LogSpoofing/log-bleeding`
  - Why this fails:
    The endpoint requires both fields.
  - Intentionally violated constraints:
    Empty input.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username is not `Admin` or password is not the generated UUID.
  - Failure endpoint:
    `POST /LogSpoofing/log-bleeding`
  - Why this fails:
    The direct comparison fails.
  - Intentionally violated constraints:
    Wrong runtime password.

Endpoint coverage:
- Covers:
  `POST /LogSpoofing/log-bleeding`
- Distinct meaning:
  Validates extraction of a secret from logs.

### Function 77: log in and list JWT votes as user

Function name:
log in and list JWT votes as user

Core endpoint(s):
- `GET /JWT/votings`

Preconditions:
- A prerequisite state or response value produced by `GET /JWT/votings/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /JWT/votings/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API sets an `access_token` cookie and then returns vote data with user-visible fields.
- Invocation:
  Step 1: `GET /JWT/votings` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /JWT/votings/login` `user` must be contained in the string `TomJerrySylvester`, such as `Tom`. The core endpoint `GET /JWT/votings` must send the `access_token` cookie from the precondition endpoint `GET /JWT/votings/login`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Login user is not in the valid-user string.
  - Failure endpoint:
    `GET /JWT/votings/login`
  - Why this fails:
    Login returns unauthorized and sets an empty cookie.
  - Intentionally violated constraints:
    Invalid user.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Vote list is requested without a valid cookie.
  - Failure endpoint:
    `GET /JWT/votings`
  - Why this fails:
    It does not fail HTTP-wise, but returns the guest serialization view.
  - Intentionally violated constraints:
    Missing valid `access_token`.

Endpoint coverage:
- Covers:
  `GET /JWT/votings`
- Distinct meaning:
  Authenticates and lists votes with authenticated fields.

### Function 78: list JWT votes as guest

Function name:
list JWT votes as guest

Core endpoint(s):
- `GET /JWT/votings`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns public vote fields only.
- Invocation:
  Step 1: `GET /JWT/votings` with required path/query/body/form/header values
- Constraints:
  No `access_token` cookie is required. Empty, invalid, `Guest`, or unknown-user tokens also use the guest view.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /JWT/votings`
  - Why this fails:
    Invalid or missing tokens degrade to guest view.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /JWT/votings`
- Distinct meaning:
  Public vote listing without authentication.

### Function 79: cast JWT vote

Function name:
cast JWT vote

Core endpoint(s):
- `POST /JWT/votings/{title}`

Preconditions:
- A prerequisite state or response value produced by `GET /JWT/votings/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /JWT/votings/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API increments the vote count for the selected title and returns accepted.
- Invocation:
  Step 1: `POST /JWT/votings/{title}` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /JWT/votings/login` must set a valid `access_token`. The core endpoint `POST /JWT/votings/{title}` must send that cookie. `{title}` should match a key in the vote map, such as `Admin lost password`, `Vote for your favourite`, `Get it for free`, or `Photo comments`; unknown titles still return accepted but do not increment anything.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie is missing, invalid, or names an invalid user.
  - Failure endpoint:
    `POST /JWT/votings/{title}`
  - Why this fails:
    The endpoint returns HTTP 401.
  - Intentionally violated constraints:
    Missing or invalid `access_token`.

Endpoint coverage:
- Covers:
  `POST /JWT/votings/{title}`
- Distinct meaning:
  Authenticated vote mutation.

### Function 80: reset JWT votes as admin

Function name:
reset JWT votes as admin

Core endpoint(s):
- `POST /JWT/votings`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API resets all vote counts and marks the lesson solved.
- Invocation:
  Step 1: `POST /JWT/votings` with required path/query/body/form/header values
- Constraints:
  The `access_token` cookie must be a valid token signed with the JWT vote secret and contain claim `admin=true`. No documented endpoint creates an admin token; the documented login endpoint creates `admin=false`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie is missing or invalid.
  - Failure endpoint:
    `POST /JWT/votings`
  - Why this fails:
    The endpoint returns invalid-token feedback.
  - Intentionally violated constraints:
    Missing/invalid `access_token`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is valid but `admin` is false.
  - Failure endpoint:
    `POST /JWT/votings`
  - Why this fails:
    The documented login token is non-admin.
  - Intentionally violated constraints:
    Non-admin token.

Endpoint coverage:
- Covers:
  `POST /JWT/votings`
- Distinct meaning:
  Admin-only vote reset.

### Function 81: forge JWT secret-key token

Function name:
forge JWT secret-key token

Core endpoint(s):
- `POST /JWT/secret`

Preconditions:
- A prerequisite state or response value produced by `GET /JWT/secret/gettoken` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `GET /JWT/secret/gettoken` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts a valid token whose username claim is `WebGoat`.
- Invocation:
  Step 1: `POST /JWT/secret` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `GET /JWT/secret/gettoken` returns a signed sample token and reveals the expected claim shape. The core endpoint `POST /JWT/secret` must submit `token` signed with the same secret and containing all expected claims, with `username=WebGoat`. Any documented method on `/JWT/secret/gettoken` returns the sample token.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is invalid or signed with the wrong key.
  - Failure endpoint:
    `POST /JWT/secret`
  - Why this fails:
    JWT parsing throws and returns invalid-token feedback.
  - Intentionally violated constraints:
    Bad signature or malformed token.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is valid but missing expected claims or has another username.
  - Failure endpoint:
    `POST /JWT/secret`
  - Why this fails:
    Claim-set check or username check fails.
  - Intentionally violated constraints:
    Missing claims or `username` not `WebGoat`.

Endpoint coverage:
- Covers:
  `POST /JWT/secret`
- Distinct meaning:
  Retrieves a sample token and validates a forged secret-key token.

### Function 82: log in to JWT refresh flow

Function name:
log in to JWT refresh flow

Core endpoint(s):
- `POST /JWT/refresh/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns an access token and refresh token.
- Invocation:
  Step 1: `POST /JWT/refresh/login` with required path/query/body/form/header values
- Constraints:
  JSON body must contain `user=Jerry` and `password=bm5nhSkxCXZkKRy4`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Body is missing or credentials do not match Jerry’s expected password.
  - Failure endpoint:
    `POST /JWT/refresh/login`
  - Why this fails:
    The endpoint returns HTTP 401.
  - Intentionally violated constraints:
    Missing body or wrong credentials.

Endpoint coverage:
- Covers:
  `POST /JWT/refresh/login`
- Distinct meaning:
  Creates access and refresh tokens.

### Function 83: refresh JWT access token

Function name:
refresh JWT access token

Core endpoint(s):
- `POST /JWT/refresh/newToken`

Preconditions:
- A prerequisite state or response value produced by `POST /JWT/refresh/login` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /JWT/refresh/login` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API consumes a valid refresh token and returns a new access/refresh token pair.
- Invocation:
  Step 1: `POST /JWT/refresh/newToken` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /JWT/refresh/newToken` must send `Authorization: Bearer {access_token}` using the precondition endpoint `POST /JWT/refresh/login`’s access token and a JSON body with `refresh_token` equal to the precondition endpoint `POST /JWT/refresh/login`’s refresh token. The refresh token is single-use and is removed after success.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Authorization header or JSON body is missing.
  - Failure endpoint:
    `POST /JWT/refresh/newToken`
  - Why this fails:
    The endpoint returns HTTP 401 before parsing.
  - Intentionally violated constraints:
    Missing header/body.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Refresh token is missing, wrong, or already used.
  - Failure endpoint:
    `POST /JWT/refresh/newToken`
  - Why this fails:
    The valid refresh token is removed after the first refresh.
  - Intentionally violated constraints:
    Reused or wrong refresh token.

Endpoint coverage:
- Covers:
  `POST /JWT/refresh/newToken`
- Distinct meaning:
  Refreshes a token pair using the login response values.

### Function 84: check out with Tom JWT

Function name:
check out with Tom JWT

Core endpoint(s):
- `POST /JWT/refresh/checkout`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API marks checkout solved when the access token claims user `Tom`.
- Invocation:
  Step 1: `POST /JWT/refresh/checkout` with required path/query/body/form/header values
- Constraints:
  `Authorization` must contain a valid Bearer token signed with the refresh JWT password and body claim `user=Tom`. No documented endpoint creates a Tom token; `/JWT/refresh/login` creates Jerry tokens.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Authorization header is missing.
  - Failure endpoint:
    `POST /JWT/refresh/checkout`
  - Why this fails:
    The endpoint returns HTTP 401.
  - Intentionally violated constraints:
    Missing Bearer token.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is valid but user is not Tom.
  - Failure endpoint:
    `POST /JWT/refresh/checkout`
  - Why this fails:
    The documented login token contains `user=Jerry`, so checkout returns not-Tom feedback.
  - Intentionally violated constraints:
    Wrong token user.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is malformed or signed incorrectly.
  - Failure endpoint:
    `POST /JWT/refresh/checkout`
  - Why this fails:
    JWT parsing fails.
  - Intentionally violated constraints:
    Invalid token.

Endpoint coverage:
- Covers:
  `POST /JWT/refresh/checkout`
- Distinct meaning:
  Validates a forged/modified checkout token.

### Function 85: submit JWT quiz and read results

Function name:
submit JWT quiz and read results

Core endpoint(s):
- `POST /JWT/quiz`
- `GET /JWT/quiz`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API records both JWT quiz answers as correct and returns the correctness array.
- Invocation:
  Step 1: `POST /JWT/quiz` with required path/query/body/form/header values
  Step 2: `GET /JWT/quiz` with required path/query/body/form/header values
- Constraints:
  Answers must contain `Solution 1` and `Solution 2` for questions 0 and 1.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: One or both answers are wrong.
  - Failure endpoint:
    `POST /JWT/quiz`
  - Why this fails:
    The endpoint succeeds only when both answers match.
  - Intentionally violated constraints:
    Wrong quiz answer.

Endpoint coverage:
- Covers:
  `POST /JWT/quiz`
  `GET /JWT/quiz`
- Distinct meaning:
  Submits and reads JWT quiz status.

### Function 86: follow user in JWT KID lesson

Function name:
follow user in JWT KID lesson

Core endpoint(s):
- `POST /JWT/kid/follow/{user}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a follow-status message.
- Invocation:
  Step 1: `POST /JWT/kid/follow/{user}` with required path/query/body/form/header values
- Constraints:
  If `{user}=Jerry`, response says following yourself is redundant; otherwise it says the user is now following Tom.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `POST /JWT/kid/follow/{user}`
  - Why this fails:
    Normal execution always returns a message.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `POST /JWT/kid/follow/{user}`
- Distinct meaning:
  Auxiliary follow action for the KID lesson.

### Function 87: solve JWT KID delete action

Function name:
solve JWT KID delete action

Core endpoint(s):
- `POST /JWT/kid/delete`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts a token that resolves a signing key by `kid` and has `username=Tom`.
- Invocation:
  Step 1: `POST /JWT/kid/delete` with required path/query/body/form/header values
- Constraints:
  `token` must be non-empty, parse as a signed JWT, use a `kid` header that resolves through the vulnerable SQL lookup, and contain claim `username=Tom`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is empty or invalid.
  - Failure endpoint:
    `POST /JWT/kid/delete`
  - Why this fails:
    Empty token returns invalid-token feedback; JWT parse failures are caught.
  - Intentionally violated constraints:
    Missing or invalid token.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token username is `Jerry` or neither Tom nor Jerry.
  - Failure endpoint:
    `POST /JWT/kid/delete`
  - Why this fails:
    The endpoint rejects Jerry and non-Tom usernames.
  - Intentionally violated constraints:
    Wrong username claim.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `kid` SQL lookup fails.
  - Failure endpoint:
    `POST /JWT/kid/delete`
  - Why this fails:
    SQL exception text is returned as failed output.
  - Intentionally violated constraints:
    Bad `kid` lookup.

Endpoint coverage:
- Covers:
  `POST /JWT/kid/delete`
- Distinct meaning:
  Performs the KID header key-resolution challenge.

### Function 88: follow user in JWT JKU lesson

Function name:
follow user in JWT JKU lesson

Core endpoint(s):
- `POST /JWT/jku/follow/{user}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a follow-status message.
- Invocation:
  Step 1: `POST /JWT/jku/follow/{user}` with required path/query/body/form/header values
- Constraints:
  If `{user}=Jerry`, response says following yourself is redundant; otherwise it says the user is now following Tom.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `POST /JWT/jku/follow/{user}`
  - Why this fails:
    Normal execution always returns a message.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `POST /JWT/jku/follow/{user}`
- Distinct meaning:
  Auxiliary follow action for the JKU lesson.

### Function 89: solve JWT JKU delete action

Function name:
solve JWT JKU delete action

Core endpoint(s):
- `POST /JWT/jku/delete`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts a token whose JKU points to a usable JWK and whose username is Tom.
- Invocation:
  Step 1: `POST /JWT/jku/delete` with required path/query/body/form/header values
- Constraints:
  `token` must be non-empty, have a `jku` header URL, a key id resolvable from that JWK provider, a valid RSA signature, and claim `username=Tom`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is empty, malformed, has an invalid JKU, or fails verification.
  - Failure endpoint:
    `POST /JWT/jku/delete`
  - Why this fails:
    The endpoint catches URL, JWK, and JWT verification exceptions.
  - Intentionally violated constraints:
    Bad token/JKU/signature.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Verified username is Jerry or not Tom.
  - Failure endpoint:
    `POST /JWT/jku/delete`
  - Why this fails:
    The endpoint only succeeds for Tom.
  - Intentionally violated constraints:
    Wrong username claim.

Endpoint coverage:
- Covers:
  `POST /JWT/jku/delete`
- Distinct meaning:
  Performs the JKU header key-resolution challenge.

### Function 90: decode JWT user claim answer

Function name:
decode JWT user claim answer

Core endpoint(s):
- `POST /JWT/decode`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the decoded JWT user value.
- Invocation:
  Step 1: `POST /JWT/decode` with required path/query/body/form/header values
- Constraints:
  Query parameter `jwt-encode-user` must equal `user`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `jwt-encode-user` is not `user`.
  - Failure endpoint:
    `POST /JWT/decode`
  - Why this fails:
    The endpoint performs a direct comparison.
  - Intentionally violated constraints:
    Wrong decoded user value.

Endpoint coverage:
- Covers:
  `POST /JWT/decode`
- Distinct meaning:
  Validates a decoded JWT claim answer.

### Function 91: submit insecure login credentials

Function name:
submit insecure login credentials

Core endpoint(s):
- `POST /InsecureLogin/task`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the exposed credentials.
- Invocation:
  Step 1: `POST /InsecureLogin/task` with required path/query/body/form/header values
- Constraints:
  `username=CaptainJack` and `password=BlackPearl`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username or password is wrong.
  - Failure endpoint:
    `POST /InsecureLogin/task`
  - Why this fails:
    The endpoint performs a direct credential comparison.
  - Intentionally violated constraints:
    Wrong credentials.

Endpoint coverage:
- Covers:
  `POST /InsecureLogin/task`
- Distinct meaning:
  Validates credentials exposed by the insecure-login lesson.

### Function 92: deserialize task token

Function name:
deserialize task token

Core endpoint(s):
- `POST /InsecureDeserialization/task`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API deserializes a token to `VulnerableTaskHolder` and accepts a 3-7 second execution delay.
- Invocation:
  Step 1: `POST /InsecureDeserialization/task` with required path/query/body/form/header values
- Constraints:
  `token` is Base64-url-normalized and Java-deserialized. The object must be a `VulnerableTaskHolder`; deserialization read time must be at least 3000 ms and at most 7000 ms.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Token is not valid Base64/serialized data or has an invalid class version.
  - Failure endpoint:
    `POST /InsecureDeserialization/task`
  - Why this fails:
    Decode/deserialization exceptions return failure feedback.
  - Intentionally violated constraints:
    Invalid serialized token.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Deserialized object is not `VulnerableTaskHolder`.
  - Failure endpoint:
    `POST /InsecureDeserialization/task`
  - Why this fails:
    Strings and other objects are rejected.
  - Intentionally violated constraints:
    Wrong serialized object type.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Delay is below 3000 ms or above 7000 ms.
  - Failure endpoint:
    `POST /InsecureDeserialization/task`
  - Why this fails:
    The timing window check fails.
  - Intentionally violated constraints:
    Wrong execution delay.

Endpoint coverage:
- Covers:
  `POST /InsecureDeserialization/task`
- Distinct meaning:
  Evaluates insecure deserialization function.

### Function 93: reverse submitted name

Function name:
reverse submitted name

Core endpoint(s):
- `POST /HttpBasics/attack1`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the submitted `person` value reversed.
- Invocation:
  Step 1: `POST /HttpBasics/attack1` with required path/query/body/form/header values
- Constraints:
  `person` must be non-blank.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `person` is blank.
  - Failure endpoint:
    `POST /HttpBasics/attack1`
  - Why this fails:
    Blank input returns `http-basics.empty`.
  - Intentionally violated constraints:
    Empty person value.

Endpoint coverage:
- Covers:
  `POST /HttpBasics/attack1`
- Distinct meaning:
  Performs the HTTP basics reverse-name exercise.

### Function 94: solve HTTP basics quiz

Function name:
solve HTTP basics quiz

Core endpoint(s):
- `POST /HttpBasics/attack2`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the HTTP method and matching magic values.
- Invocation:
  Step 1: `POST /HttpBasics/attack2` with required path/query/body/form/header values
- Constraints:
  `answer` must equal `POST` case-insensitively, and `magic_answer` must equal `magic_num`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `answer` is not `POST`.
  - Failure endpoint:
    `POST /HttpBasics/attack2`
  - Why this fails:
    The first check returns incorrect feedback.
  - Intentionally violated constraints:
    Wrong method answer.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `magic_answer` and `magic_num` differ.
  - Failure endpoint:
    `POST /HttpBasics/attack2`
  - Why this fails:
    The magic value equality check fails.
  - Intentionally violated constraints:
    Mismatched magic values.

Endpoint coverage:
- Covers:
  `POST /HttpBasics/attack2`
- Distinct meaning:
  Validates basic HTTP method and request parameter manipulation.

### Function 95: submit tampered HTML purchase

Function name:
submit tampered HTML purchase

Core endpoint(s):
- `POST /HtmlTampering/task`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts a manipulated total price.
- Invocation:
  Step 1: `POST /HtmlTampering/task` with required path/query/body/form/header values
- Constraints:
  `Float(QTY) * 2999.99` must be greater than `Float(Total) + 1`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Total is not low enough for the quantity.
  - Failure endpoint:
    `POST /HtmlTampering/task`
  - Why this fails:
    The arithmetic comparison does not indicate tampering.
  - Intentionally violated constraints:
    `Total` is consistent with `QTY`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `QTY` or `Total` is not parseable as a float.
  - Failure endpoint:
    `POST /HtmlTampering/task`
  - Why this fails:
    The implementation parses both values as floats and does not catch parse errors.
  - Intentionally violated constraints:
    Non-numeric input.

Endpoint coverage:
- Covers:
  `POST /HtmlTampering/task`
- Distinct meaning:
  Detects a tampered purchase total.

### Function 96: hijack session by cookie

Function name:
hijack session by cookie

Core endpoint(s):
- `POST /HijackSession/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API authenticates when the submitted `hijack_cookie` is in the provider’s active session queue.
- Invocation:
  Step 1: `POST /HijackSession/login` with required path/query/body/form/header values
  Step 2: `POST /HijackSession/login` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /HijackSession/login` without a cookie issues a new predictable cookie and may also auto-create authenticated sessions internally. The core endpoint `POST /HijackSession/login` must send a `hijack_cookie` value that exists in the provider session queue.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie is absent and generated authentication is not authenticated.
  - Failure endpoint:
    `POST /HijackSession/login`
  - Why this fails:
    Credentials are not actually checked for success; without a valid queued id, authentication remains false.
  - Intentionally violated constraints:
    No valid hijack cookie.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Cookie value is not in the active session queue.
  - Failure endpoint:
    `POST /HijackSession/login`
  - Why this fails:
    The provider authenticates only known queued ids.
  - Intentionally violated constraints:
    Wrong session id.

Endpoint coverage:
- Covers:
  `POST /HijackSession/login`
- Distinct meaning:
  Authenticates by replaying or predicting an active session id.

### Function 97: list and create stored XSS comments

Function name:
list and create stored XSS comments

Core endpoint(s):
- `GET /CrossSiteScriptingStored/stored-xss`

Preconditions:
- A prerequisite state or response value produced by `POST /CrossSiteScriptingStored/stored-xss` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /CrossSiteScriptingStored/stored-xss` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API stores a comment for the current user and marks the stored-XSS comment step solved when it contains the phone-home script.
- Invocation:
  Step 1: `GET /CrossSiteScriptingStored/stored-xss` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /CrossSiteScriptingStored/stored-xss` body must deserialize to `Comment`. `text` must contain `<script>webgoat.customjs.phoneHome()</script>` for assignment success. The core endpoint `GET /CrossSiteScriptingStored/stored-xss` lists default comments plus the user’s new comments.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Comment text does not contain the exact phone-home script.
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss`
  - Why this fails:
    The comment is still stored, but the success check fails.
  - Intentionally violated constraints:
    Missing exact script.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: JSON body cannot be parsed.
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss`
  - Why this fails:
    Parsing falls back to an empty comment, which does not contain the script.
  - Intentionally violated constraints:
    Invalid comment JSON.

Endpoint coverage:
- Covers:
  `GET /CrossSiteScriptingStored/stored-xss`
- Distinct meaning:
  Creates and lists stored XSS comments.

### Function 98: verify stored XSS callback

Function name:
verify stored XSS callback

Core endpoint(s):
- `POST /CrossSiteScriptingStored/stored-xss-follow-up`

Preconditions:
- A prerequisite state or response value produced by `POST /CrossSiteScriptingStored/stored-xss` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /CrossSiteScriptingStored/stored-xss` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.
- A prerequisite state or response value produced by `POST /CrossSiteScripting/phone-home-xss` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /CrossSiteScripting/phone-home-xss` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the callback message generated by the phone-home endpoint.
- Invocation:
  Step 1: `POST /CrossSiteScriptingStored/stored-xss-follow-up` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /CrossSiteScriptingStored/stored-xss` stores a script that calls phone home. The precondition endpoint `POST /CrossSiteScripting/phone-home-xss` must use `param1=42`, `param2=24`, and header `webgoat-requested-by=dom-xss-vuln`, causing `randValue` to be stored. The core endpoint `POST /CrossSiteScriptingStored/stored-xss-follow-up` must send `successMessage` equal to that `randValue`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Phone-home setup is omitted.
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Why this fails:
    No matching `randValue` exists in session.
  - Intentionally violated constraints:
    Omitted `POST /CrossSiteScripting/phone-home-xss`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `successMessage` does not match the stored value.
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Why this fails:
    The endpoint compares directly to session `randValue`.
  - Intentionally violated constraints:
    Wrong callback value.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScriptingStored/stored-xss-follow-up`
- Distinct meaning:
  Confirms the stored XSS callback workflow.

### Function 99: submit XSS quiz and read results

Function name:
submit XSS quiz and read results

Core endpoint(s):
- `POST /CrossSiteScripting/quiz`
- `GET /CrossSiteScripting/quiz`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API records all XSS quiz answers as correct and returns the correctness array.
- Invocation:
  Step 1: `POST /CrossSiteScripting/quiz` with required path/query/body/form/header values
  Step 2: `GET /CrossSiteScripting/quiz` with required path/query/body/form/header values
- Constraints:
  Answers must contain `Solution 4`, `Solution 3`, `Solution 1`, `Solution 2`, and `Solution 4` for questions 0 through 4.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Any answer is wrong.
  - Failure endpoint:
    `POST /CrossSiteScripting/quiz`
  - Why this fails:
    The endpoint succeeds only when all five answers match.
  - Intentionally violated constraints:
    Wrong quiz answer.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/quiz`
  `GET /CrossSiteScripting/quiz`
- Distinct meaning:
  Submits and reads XSS quiz status.

### Function 100: trigger DOM XSS phone home

Function name:
trigger DOM XSS phone home

Core endpoint(s):
- `POST /CrossSiteScripting/phone-home-xss`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API stores a random value in session and returns it in output.
- Invocation:
  Step 1: `POST /CrossSiteScripting/phone-home-xss` with required path/query/body/form/header values
- Constraints:
  `param1=42`, `param2=24`, and request header `webgoat-requested-by=dom-xss-vuln`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Parameters or header do not match.
  - Failure endpoint:
    `POST /CrossSiteScripting/phone-home-xss`
  - Why this fails:
    The endpoint checks all three conditions.
  - Intentionally violated constraints:
    Wrong numeric parameters or missing/wrong header.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/phone-home-xss`
- Distinct meaning:
  Generates the DOM XSS callback value.

### Function 101: verify DOM XSS follow-up

Function name:
verify DOM XSS follow-up

Core endpoint(s):
- `POST /CrossSiteScripting/dom-follow-up`

Preconditions:
- A prerequisite state or response value produced by `POST /CrossSiteScripting/phone-home-xss` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /CrossSiteScripting/phone-home-xss` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts the session random value from the phone-home call.
- Invocation:
  Step 1: `POST /CrossSiteScripting/dom-follow-up` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /CrossSiteScripting/phone-home-xss` produces `randValue`. The core endpoint `POST /CrossSiteScripting/dom-follow-up` must submit `successMessage` equal to `randValue`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Phone-home endpoint is omitted or `successMessage` is wrong.
  - Failure endpoint:
    `POST /CrossSiteScripting/dom-follow-up`
  - Why this fails:
    The endpoint compares to missing or different session `randValue`.
  - Intentionally violated constraints:
    Missing/wrong callback value.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/dom-follow-up`
- Distinct meaning:
  Confirms the DOM XSS callback value.

### Function 102: submit XSS mitigation code answers

Function name:
submit XSS mitigation code answers

Core endpoint(s):
- `POST /CrossSiteScripting/attack3`
- `POST /CrossSiteScripting/attack4`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts mitigation code snippets for reflected/stored XSS lessons.
- Invocation:
  Step 1: `POST /CrossSiteScripting/attack3` with required path/query/body/form/header values
  Step 2: `POST /CrossSiteScripting/attack4` with required path/query/body/form/header values
- Constraints:
  The core endpoint `POST /CrossSiteScripting/attack3` `editor` must include the OWASP Java Encoder taglib and use `${e:forHtml(param.first_name)}` and `${e:forHtml(param.last_name)}` in the parsed table. The core endpoint `POST /CrossSiteScripting/attack4` `editor2` must include AntiSamy scan logic, `CleanResults`, DAO insertion, and `.getCleanHTML()`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Required encoder elements are missing or malformed.
  - Failure endpoint:
    `POST /CrossSiteScripting/attack3`
  - Why this fails:
    Jsoup parsing or required-string checks fail.
  - Intentionally violated constraints:
    Missing encoder taglib or escaped output calls.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Required AntiSamy constructs are missing.
  - Failure endpoint:
    `POST /CrossSiteScripting/attack4`
  - Why this fails:
    The endpoint’s stripped-code string checks do not all pass.
  - Intentionally violated constraints:
    Missing AntiSamy scan/cleaning logic.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/attack3`
  `POST /CrossSiteScripting/attack4`
- Distinct meaning:
  Validates secure XSS mitigation code snippets.

### Function 103: submit reflected XSS payload

Function name:
submit reflected XSS payload

Core endpoint(s):
- `GET /CrossSiteScripting/attack5a`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a cart response and marks the reflected XSS assignment solved.
- Invocation:
  Step 1: `GET /CrossSiteScripting/attack5a` with required path/query/body/form/header values
- Constraints:
  `field1` must match the script pattern `<script>(console.log|alert)(...)</script>`. `field2` must not match that script pattern. Quantities are used to calculate the displayed total.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Script payload is placed in `field2`.
  - Failure endpoint:
    `GET /CrossSiteScripting/attack5a`
  - Why this fails:
    The endpoint has a specific wrong-field failure branch.
  - Intentionally violated constraints:
    XSS payload in `field2`.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `field1` lacks the expected script pattern.
  - Failure endpoint:
    `GET /CrossSiteScripting/attack5a`
  - Why this fails:
    The success regex does not match.
  - Intentionally violated constraints:
    Missing reflected XSS script.

Endpoint coverage:
- Covers:
  `GET /CrossSiteScripting/attack5a`
- Distinct meaning:
  Validates reflected XSS payload placement.

### Function 104: submit DOM XSS route answer

Function name:
submit DOM XSS route answer

Core endpoint(s):
- `POST /CrossSiteScripting/attack6a`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts the vulnerable DOM route.
- Invocation:
  Step 1: `POST /CrossSiteScripting/attack6a` with required path/query/body/form/header values
- Constraints:
  `DOMTestRoute` must match `start.mvc#test` with an optional trailing slash.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Route does not match the required pattern.
  - Failure endpoint:
    `POST /CrossSiteScripting/attack6a`
  - Why this fails:
    The regex match fails.
  - Intentionally violated constraints:
    Wrong DOM route.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/attack6a`
- Distinct meaning:
  Validates the DOM XSS route answer.

### Function 105: complete XSS lesson 1 checkbox

Function name:
complete XSS lesson 1 checkbox

Core endpoint(s):
- `POST /CrossSiteScripting/attack1`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API marks the first XSS lesson step solved.
- Invocation:
  Step 1: `POST /CrossSiteScripting/attack1` with required path/query/body/form/header values
- Constraints:
  Optional parameter `checkboxAttack1` must be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `checkboxAttack1` is omitted.
  - Failure endpoint:
    `POST /CrossSiteScripting/attack1`
  - Why this fails:
    The endpoint succeeds only when the optional value is non-null.
  - Intentionally violated constraints:
    Missing checkbox parameter.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/attack1`
- Distinct meaning:
  Confirms the XSS lesson checkbox action.

### Function 106: solve Chrome DevTools network number task

Function name:
solve Chrome DevTools network number task

Core endpoint(s):
- `POST /ChromeDevTools/network`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts matching network-number parameters.
- Invocation:
  Step 1: `POST /ChromeDevTools/network` with required path/query/body/form/header values
- Constraints:
  Implementation has two mappings on the same path. The solving mapping requires `network_num` and `number`, and they must be equal. Swagger merges this path with the auxiliary `networkNum` mapping, so it incorrectly documents all three query parameters together.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `network_num` and `number` differ.
  - Failure endpoint:
    `POST /ChromeDevTools/network`
  - Why this fails:
    The solving method compares the two values.
  - Intentionally violated constraints:
    Mismatched network numbers.

Endpoint coverage:
- Covers:
  `POST /ChromeDevTools/network`
- Distinct meaning:
  Solves the network-number task when using the `network_num`/`number` mapping; also includes an auxiliary 200-empty mapping for `networkNum`.

### Function 107: verify Chrome DevTools dummy callback

Function name:
verify Chrome DevTools dummy callback

Core endpoint(s):
- `POST /ChromeDevTools/dummy`

Preconditions:
- A prerequisite state or response value produced by `POST /CrossSiteScripting/phone-home-xss` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /CrossSiteScripting/phone-home-xss` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API accepts a message equal to the session random value.
- Invocation:
  Step 1: `POST /ChromeDevTools/dummy` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /CrossSiteScripting/phone-home-xss` is one documented endpoint that stores `randValue` in the lesson session. The core endpoint `POST /ChromeDevTools/dummy` must submit `successMessage` equal to that value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: The random value was not generated or `successMessage` is wrong.
  - Failure endpoint:
    `POST /ChromeDevTools/dummy`
  - Why this fails:
    The endpoint compares against session `randValue`.
  - Intentionally violated constraints:
    Missing/wrong session value.

Endpoint coverage:
- Covers:
  `POST /ChromeDevTools/dummy`
- Distinct meaning:
  Verifies a callback value for the Chrome DevTools lesson.

### Function 108: bypass frontend validation

Function name:
bypass frontend validation

Core endpoint(s):
- `POST /BypassRestrictions/frontendValidation`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts values that violate every frontend validation regex while `error=0`.
- Invocation:
  Step 1: `POST /BypassRestrictions/frontendValidation` with required path/query/body/form/header values
- Constraints:
  `error` must be `0`. Each field must not match its corresponding frontend regex.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `error>0` or any field still matches its frontend-valid regex.
  - Failure endpoint:
    `POST /BypassRestrictions/frontendValidation`
  - Why this fails:
    The endpoint fails on the first still-valid frontend field.
  - Intentionally violated constraints:
    Did not bypass one or more frontend restrictions.

Endpoint coverage:
- Covers:
  `POST /BypassRestrictions/frontendValidation`
- Distinct meaning:
  Validates server-side detection of frontend-validation bypass.

### Function 109: bypass field restrictions

Function name:
bypass field restrictions

Core endpoint(s):
- `POST /BypassRestrictions/FieldRestrictions`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts values outside the restricted form controls.
- Invocation:
  Step 1: `POST /BypassRestrictions/FieldRestrictions` with required path/query/body/form/header values
- Constraints:
  `select` and `radio` must be neither `option1` nor `option2`; `checkbox` must be neither `on` nor `off`; `shortInput` length must be greater than 5; `readOnlyInput` must not equal `change`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Any field remains within its restricted value set.
  - Failure endpoint:
    `POST /BypassRestrictions/FieldRestrictions`
  - Why this fails:
    The endpoint fails each original restriction value.
  - Intentionally violated constraints:
    Did not bypass one or more field restrictions.

Endpoint coverage:
- Covers:
  `POST /BypassRestrictions/FieldRestrictions`
- Distinct meaning:
  Validates tampering with restricted form controls.

### Function 110: retrieve report card

Function name:
retrieve report card

Core endpoint(s):
- `GET /service/reportcard.mvc`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns current-user lesson and assignment progress statistics.
- Invocation:
  Step 1: `GET /service/reportcard.mvc` with required path/query/body/form/header values
- Constraints:
  Uses current username to load `UserProgress`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: No application-level failure branch is visible in the implementation.
  - Failure endpoint:
    `GET /service/reportcard.mvc`
  - Why this fails:
    Normal execution returns computed progress.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/reportcard.mvc`
- Distinct meaning:
  Retrieves progress summary.

### Function 111: retrieve lesson overview

Function name:
retrieve lesson overview

Core endpoint(s):
- `GET /service/lessonoverview.mvc/{lesson}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns assignment solved status for a lesson.
- Invocation:
  Step 1: `GET /service/lessonoverview.mvc/{lesson}` with required path/query/body/form/header values
- Constraints:
  `{lesson}` must resolve to a lesson name in the course.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `{lesson}` does not resolve to a course lesson.
  - Failure endpoint:
    `GET /service/lessonoverview.mvc/{lesson}`
  - Why this fails:
    `Assert.isTrue(lesson != null, "Lesson not found")` triggers an exception.
  - Intentionally violated constraints:
    Invalid lesson name.

Endpoint coverage:
- Covers:
  `GET /service/lessonoverview.mvc/{lesson}`
- Distinct meaning:
  Reads per-assignment progress for one lesson.

### Function 112: retrieve lesson info

Function name:
retrieve lesson info

Core endpoint(s):
- `GET /service/lessoninfo.mvc/{lesson}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns lesson title and source/solution/plan availability flags.
- Invocation:
  Step 1: `GET /service/lessoninfo.mvc/{lesson}` with required path/query/body/form/header values
- Constraints:
  `{lesson}` must resolve to a lesson name in the course.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `{lesson}` is invalid.
  - Failure endpoint:
    `GET /service/lessoninfo.mvc/{lesson}`
  - Why this fails:
    The implementation dereferences the resolved lesson without a null guard.
  - Intentionally violated constraints:
    Invalid lesson name.

Endpoint coverage:
- Covers:
  `GET /service/lessoninfo.mvc/{lesson}`
- Distinct meaning:
  Reads metadata for one lesson.

### Function 113: retrieve labels and hints

Function name:
retrieve labels and hints

Core endpoint(s):
- `GET /service/labels.mvc`
- `GET /service/hint.mvc`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns all labels or all hints.
- Invocation:
  Step 1: `GET /service/labels.mvc` with required path/query/body/form/header values
  Step 2: `GET /service/hint.mvc` with required path/query/body/form/header values
- Constraints:
  The endpoints are independent; the sequence is an endpoint set, not required ordering.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /service/labels.mvc`
  - Why this fails:
    Normal execution returns loaded properties.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/labels.mvc`
  `GET /service/hint.mvc`
- Distinct meaning:
  Supplies UI labels and assignment hints.

### Function 114: retrieve server directory

Function name:
retrieve server directory

Core endpoint(s):
- `GET /server-directory`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the configured WebGoat server directory.
- Invocation:
  Step 1: `GET /server-directory` with required path/query/body/form/header values
- Constraints:
  Returns property `webgoat.server.directory`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Property is absent.
  - Failure endpoint:
    `GET /server-directory`
  - Why this fails:
    The implementation would return `null`, not an explicit application error.
  - Intentionally violated constraints:
    Missing environment property.

Endpoint coverage:
- Covers:
  `GET /server-directory`
- Distinct meaning:
  Exposes server directory configuration.

### Function 115: retrieve scoreboard data

Function name:
retrieve scoreboard data

Core endpoint(s):
- `GET /scoreboard-data`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns rankings of non-CSRF users by solved challenges.
- Invocation:
  Step 1: `GET /scoreboard-data` with required path/query/body/form/header values
- Constraints:
  No setup is required; the endpoint lists all users and filters names starting with `csrf-`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /scoreboard-data`
  - Why this fails:
    Normal execution returns rankings.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /scoreboard-data`
- Distinct meaning:
  Returns CTF scoreboard information.

### Function 116: retrieve lesson menu

Function name:
retrieve lesson menu

Core endpoint(s):
- `GET /service/lessonmenu.mvc`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the left-navigation lesson menu with completion status.
- Invocation:
  Step 1: `GET /service/lessonmenu.mvc` with required path/query/body/form/header values
- Constraints:
  Source uses `@RequestMapping` without a method restriction, so all documented methods return the same menu.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /service/lessonmenu.mvc`
  - Why this fails:
    Normal execution returns menu items.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/lessonmenu.mvc`
- Distinct meaning:
  All documented methods map to the same menu retrieval function.

### Function 117: call disabled security toggle

Function name:
call disabled security toggle

Core endpoint(s):
- `GET /service/enable-security.mvc`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the message for “Not working...” and does not toggle security.
- Invocation:
  Step 1: `GET /service/enable-security.mvc` with required path/query/body/form/header values
- Constraints:
  Source uses `@RequestMapping` without a method restriction, so all documented methods return the same message. The restart/toggle code is commented out.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /service/enable-security.mvc`
  - Why this fails:
    Normal execution returns the static message.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/enable-security.mvc`
- Distinct meaning:
  Documented as a callable service endpoint, but implementation says the feature is disabled.

### Function 118: set label debugging status

Function name:
set label debugging status

Core endpoint(s):
- `GET /service/debug/labels.mvc`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API sets the label-debugging enabled flag and returns `{success:true, enabled:<value>}`.
- Invocation:
  Step 1: `GET /service/debug/labels.mvc` with required path/query/body/form/header values
- Constraints:
  Swagger documents required query parameter `enabled` for all methods. Source also has a no-parameter status-check mapping on the same path, but the OpenAPI file exposes the parameterized setter shape for every documented method.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: `enabled` is missing from the documented operation.
  - Failure endpoint:
    `GET /service/debug/labels.mvc`
  - Why this fails:
    Under the Swagger contract, `enabled` is required; in source, a no-param request would be routed to the status-check method instead.
  - Intentionally violated constraints:
    Omitted documented query parameter.

Endpoint coverage:
- Covers:
  `GET /service/debug/labels.mvc`
- Distinct meaning:
  Sets label debugging; source also supports an undocumented-by-Swagger status-check variant on the same path.

### Function 119: solve challenge 5 login

Function name:
solve challenge 5 login

Core endpoint(s):
- `POST /challenge/5`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API accepts Larry’s login and returns flag 5.
- Invocation:
  Step 1: `POST /challenge/5` with required path/query/body/form/header values
- Constraints:
  `username_login` must be exactly `Larry`. `password_login` must make the vulnerable SQL query return a row for Larry; seeded password is `larryknows`, and injection is also possible because the query concatenates values.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username or password is blank.
  - Failure endpoint:
    `POST /challenge/5`
  - Why this fails:
    The endpoint checks both fields with `hasText`.
  - Intentionally violated constraints:
    Empty credentials.
- Branch 2:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Username is not `Larry`.
  - Failure endpoint:
    `POST /challenge/5`
  - Why this fails:
    The endpoint rejects all non-Larry usernames before querying.
  - Intentionally violated constraints:
    Wrong user.
- Branch 3:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Query returns no row.
  - Failure endpoint:
    `POST /challenge/5`
  - Why this fails:
    The SQL result set is empty.
  - Intentionally violated constraints:
    Wrong password or unsuccessful injection.

Endpoint coverage:
- Covers:
  `POST /challenge/5`
- Distinct meaning:
  Authenticates Larry for challenge 5.

### Function 120: send and use challenge 7 reset link

Function name:
send and use challenge 7 reset link

Core endpoint(s):
- `GET /challenge/7/reset-password/{link}`

Preconditions:
- A prerequisite state or response value produced by `POST /challenge/7` with its required path/query/body/form/header values is available before the core endpoint is invoked. This can be satisfied by calling `POST /challenge/7` and reusing any generated id, token, location, ETag, cursor, cookie, session value, or response value it returns, or by directly seeding the equivalent database, session, or resource state used by the implementation.

Successful execution:
- Result:
  The API sends a password reset link and accepts the admin reset link, returning flag 7.
- Invocation:
  Step 1: `GET /challenge/7/reset-password/{link}` with required path/query/body/form/header values
- Constraints:
  The precondition endpoint `POST /challenge/7` with an admin email local part generates a deterministic admin reset link using key `webgoat`; the core endpoint `GET /challenge/7/reset-password/{link}` must use `{link}=375afe1104f4a487a73823c50a9292a2`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Step 2 uses any other link.
  - Failure endpoint:
    `GET /challenge/7/reset-password/{link}`
  - Why this fails:
    The endpoint returns HTTP 418 and “not the reset link for admin”.
  - Intentionally violated constraints:
    Wrong reset link.

Endpoint coverage:
- Covers:
  `GET /challenge/7/reset-password/{link}`
- Distinct meaning:
  Sends a reset link and retrieves challenge 7’s flag through the admin link.

### Function 121: read challenge 7 git metadata

Function name:
read challenge 7 git metadata

Core endpoint(s):
- `GET /challenge/7/.git`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns a bundled `.git` resource for the challenge.
- Invocation:
  Step 1: `GET /challenge/7/.git` with required path/query/body/form/header values
- Constraints:
  The endpoint is implemented in the same challenge source but was not in the displayed snippets above; OpenAPI documents it as `assignment-7`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: No application-level failure condition is visible from the documented contract.
  - Failure endpoint:
    `GET /challenge/7/.git`
  - Why this fails:
    Failure would depend on missing bundled resource data.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /challenge/7/.git`
- Distinct meaning:
  Auxiliary challenge artifact retrieval.

### Function 122: retrieve challenge 8 votes

Function name:
retrieve challenge 8 votes

Core endpoint(s):
- `GET /challenge/8/votes/`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns current vote counts.
- Invocation:
  Step 1: `GET /challenge/8/votes/` with required path/query/body/form/header values
- Constraints:
  No setup is required because votes are statically initialized.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /challenge/8/votes/`
  - Why this fails:
    Normal execution returns the vote map.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /challenge/8/votes/`
- Distinct meaning:
  Reads challenge 8 vote counts.

### Function 123: retrieve challenge 8 vote average

Function name:
retrieve challenge 8 vote average

Core endpoint(s):
- `GET /challenge/8/votes/average`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns the rounded-up average star rating.
- Invocation:
  Step 1: `GET /challenge/8/votes/average` with required path/query/body/form/header values
- Constraints:
  Uses current in-memory vote counts.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `GET /challenge/8/votes/average`
  - Why this fails:
    The static vote map has values, so division has a nonzero denominator.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /challenge/8/votes/average`
- Distinct meaning:
  Computes challenge 8 vote average.

### Function 124: attempt challenge 8 vote through documented GET

Function name:
attempt challenge 8 vote through documented GET

Core endpoint(s):
- `GET /challenge/8/vote/{stars}`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The documented GET endpoint returns a JSON message saying login is required; it does not cast a vote.
- Invocation:
  Step 1: `GET /challenge/8/vote/{stars}` with required path/query/body/form/header values
- Constraints:
  Source contains a non-GET success branch in the method body, but the method is annotated only with `@GetMapping`; therefore the Swagger-documented function cannot reach the vote-incrementing branch.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: A caller expects a GET request to cast a vote.
  - Failure endpoint:
    `GET /challenge/8/vote/{stars}`
  - Why this fails:
    The implementation explicitly returns an error JSON whenever the request method is `GET`.
  - Intentionally violated constraints:
    Using only the documented method.

Endpoint coverage:
- Covers:
  `GET /challenge/8/vote/{stars}`
- Distinct meaning:
  Documented vote path returns a login-required message; actual vote success branch is unreachable through Swagger.

### Function 125: retrieve lesson/application metadata services

Function name:
retrieve lesson/application metadata services

Core endpoint(s):
- `GET /service/reportcard.mvc`
- `GET /service/lessonoverview.mvc/{lesson}`
- `GET /service/lessoninfo.mvc/{lesson}`
- `GET /service/labels.mvc`
- `GET /service/hint.mvc`
- `GET /server-directory`
- `GET /scoreboard-data`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns service metadata used by the WebGoat UI.
- Invocation:
  Step 1: `GET /service/reportcard.mvc` with required path/query/body/form/header values
  Step 2: `GET /service/lessonoverview.mvc/{lesson}` with required path/query/body/form/header values
  Step 3: `GET /service/lessoninfo.mvc/{lesson}` with required path/query/body/form/header values
  Step 4: `GET /service/labels.mvc` with required path/query/body/form/header values
  Step 5: `GET /service/hint.mvc` with required path/query/body/form/header values
  Step 6: `GET /server-directory` with required path/query/body/form/header values
  Step 7: `GET /scoreboard-data` with required path/query/body/form/header values
- Constraints:
  Steps are independent service reads. `{lesson}` must be a valid lesson name where used.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made with this invalid, missing, duplicated, mismatched, unauthorized, unauthenticated, expired, or cross-scoped state: Invalid `{lesson}` is supplied to a lesson-scoped service.
  - Failure endpoint:
    `GET /service/lessonoverview.mvc/{lesson}`
  - Why this fails:
    The lesson lookup fails.
  - Intentionally violated constraints:
    Invalid lesson path variable.

Endpoint coverage:
- Covers:
  `GET /service/reportcard.mvc`
  `GET /service/lessonoverview.mvc/{lesson}`
  `GET /service/lessoninfo.mvc/{lesson}`
  `GET /service/labels.mvc`
  `GET /service/hint.mvc`
  `GET /server-directory`
  `GET /scoreboard-data`
- Distinct meaning:
  UI-support metadata and progress reads.

### Function 126: solve insecure login support endpoint

Function name:
call insecure login support endpoint

Core endpoint(s):
- `POST /InsecureLogin/login`

Preconditions:
- No separate setup endpoint is required beyond the request values, authentication/session context, seeded data, and runtime state described in the constraints.

Successful execution:
- Result:
  The API returns HTTP 202 Accepted from an otherwise empty login support endpoint.
- Invocation:
  Step 1: `POST /InsecureLogin/login` with required path/query/body/form/header values
- Constraints:
  The implementation exists only so client JavaScript can call an existing endpoint; it performs no authentication logic.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application-level failure condition is implemented for the endpoint under normal runtime state.
  - Failure endpoint:
    `POST /InsecureLogin/login`
  - Why this fails:
    Normal execution always returns accepted.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `POST /InsecureLogin/login`
- Distinct meaning:
  Auxiliary endpoint for the insecure-login lesson.

### Unclear or auxiliary endpoints

- `GET /PathTraversal/zip-slip/profile-image/{username}`:
  The source implementation always returns `ResponseEntity.notFound()`. It is documented in Swagger but does not expose a successful user-visible resource retrieval path.

- `GET /challenge/8/notUsed`:
  The implementation throws `IllegalStateException("Should never be called, challenge specific method")`. It is documented but intentionally not a usable function.

- `POST /ChromeDevTools/network` with only `networkNum`:
  Source has an auxiliary overload that returns HTTP 200 with an empty body, while Swagger merges it with the solving overload and documents `network_num`, `number`, and `networkNum` together. Its user-visible function is only to create a request for inspection.
