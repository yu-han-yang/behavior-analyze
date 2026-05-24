Analyzed only `src` and `webgoat.json`. I did not execute the API.

### Behavior 1: complete intercepted request challenge

Behavior name:
complete intercepted request challenge

Successful execution:
- Result:
  The request-interception lesson is marked solved.
- Endpoint sequence:
  Step 1: `GET /HttpProxies/intercept-request` with header `x-request-intercepted=true` and query `changeMe=Requests are tampered easily`.
- Constraints:
  The method must be `GET`; `POST` is documented but the implementation always fails it. The query value is compared case-insensitively.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The documented `POST` variant is used.
  - Endpoint group:
    Step 1: `POST /HttpProxies/intercept-request`
  - Failure endpoint:
    `POST /HttpProxies/intercept-request`
  - Why this fails:
    The implementation immediately fails all POST requests.
  - Intentionally violated constraints:
    The required method is `GET`.
- Branch 2:
  - Unsatisfied condition:
    The header or query value is missing or incorrect.
  - Endpoint group:
    Step 1: `GET /HttpProxies/intercept-request`
  - Failure endpoint:
    `GET /HttpProxies/intercept-request`
  - Why this fails:
    The implementation requires both the header and the exact tampered query value.
  - Intentionally violated constraints:
    Missing or mismatched `x-request-intercepted` / `changeMe`.

Endpoint coverage:
- Covers:
  `GET /HttpProxies/intercept-request`
  `POST /HttpProxies/intercept-request`
- Distinct meaning:
  `GET` can solve the lesson; `POST` is a documented but deliberately failing branch.

### Behavior 2: retrieve challenge logo

Behavior name:
retrieve challenge logo

Successful execution:
- Result:
  The API returns the challenge logo PNG with a runtime PIN embedded in fixed bytes.
- Endpoint sequence:
  Step 1: `GET /challenge/logo`
- Constraints:
  `POST /challenge/logo` behaves the same as `GET /challenge/logo`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /challenge/logo`
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

### Behavior 3: solve challenge 1 admin login

Behavior name:
solve challenge 1 admin login

Successful execution:
- Result:
  The API accepts the admin credentials and returns flag 1.
- Endpoint sequence:
  Step 1: `GET /challenge/logo`
  Step 2: `POST /challenge/1`
- Constraints:
  Step 1 exposes the runtime PIN embedded in the logo. Step 2 must use `username=admin` and `password=!!webgoat_admin_{PIN}!!`, where `{PIN}` is the four-digit value from Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username is not `admin` or the password does not contain the embedded PIN.
  - Endpoint group:
    Step 1: `GET /challenge/logo`
    Step 2: `POST /challenge/1`
  - Failure endpoint:
    `POST /challenge/1`
  - Why this fails:
    The implementation compares the submitted password with the constant template after replacing `1234` with the runtime PIN.
  - Intentionally violated constraints:
    Wrong username or wrong PIN-derived password.

Endpoint coverage:
- Covers:
  `POST /challenge/1`
  `GET /challenge/logo`
- Distinct meaning:
  Uses the logo endpoint as setup for the challenge login workflow.

### Behavior 4: submit captured challenge flag

Behavior name:
submit captured challenge flag

Successful execution:
- Result:
  The submitted flag is accepted for the requested challenge number.
- Endpoint sequence:
  Step 1: `POST /challenge/1`
  Step 2: `POST /challenge/flag/{flagNumber}`
- Constraints:
  One supported sequence is to solve challenge 1 first, then call Step 2 with `flagNumber=1` and `flag` equal to the flag returned by Step 1. Other challenge-solving endpoints can similarly produce flags for their own numbers.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The submitted `flag` does not equal the server-side flag for `{flagNumber}`.
  - Endpoint group:
    Step 1: `POST /challenge/flag/{flagNumber}`
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

### Behavior 5: register SQL challenge user

Behavior name:
register SQL challenge user

Successful execution:
- Result:
  A new SQL challenge user is inserted.
- Endpoint sequence:
  Step 1: `PUT /SqlInjectionAdvanced/challenge`
- Constraints:
  `username_reg`, `email_reg`, and `password_reg` must be non-empty. `username_reg` must be at most 250 characters; `email_reg` and `password_reg` must be at most 30 characters. The username must not already exist.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A required field is empty or too long.
  - Endpoint group:
    Step 1: `PUT /SqlInjectionAdvanced/challenge`
  - Failure endpoint:
    `PUT /SqlInjectionAdvanced/challenge`
  - Why this fails:
    `checkArguments` rejects empty values and length violations.
  - Intentionally violated constraints:
    Invalid registration field.
- Branch 2:
  - Unsatisfied condition:
    The username already exists and the username does not contain `tom'`.
  - Endpoint group:
    Step 1: `PUT /SqlInjectionAdvanced/challenge`
    Step 2: `PUT /SqlInjectionAdvanced/challenge`
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

### Behavior 6: trigger SQL challenge existing-user success

Behavior name:
trigger SQL challenge existing-user success

Successful execution:
- Result:
  The challenge is marked solved by making the duplicate-user branch return success.
- Endpoint sequence:
  Step 1: `PUT /SqlInjectionAdvanced/challenge`
- Constraints:
  `username_reg` must cause the duplicate-user query to find an existing row and must contain `tom'`. This is a distinct success meaning from normal user creation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The injected username does not find an existing row.
  - Endpoint group:
    Step 1: `PUT /SqlInjectionAdvanced/challenge`
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

### Behavior 7: log in to SQL injection challenge

Behavior name:
log in to SQL injection challenge

Successful execution:
- Result:
  Login succeeds for user `tom`.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionAdvanced/challenge_Login`
- Constraints:
  `username_login=tom` and `password_login=thisisasecretfortomonly`, as seeded in `src/main/resources/lessons/sqlinjection/db/migration/V2019_09_26_5__challenge_assignment.sql`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Credentials match a non-`tom` user.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/challenge_Login`
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/challenge_Login`
  - Why this fails:
    The query finds a row, but the implementation only succeeds when `username_login` is `tom`.
  - Intentionally violated constraints:
    User is not `tom`.
- Branch 2:
  - Unsatisfied condition:
    No row matches the submitted credentials.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/challenge_Login`
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

### Behavior 8: log in for IDOR lesson

Behavior name:
log in for IDOR lesson

Successful execution:
- Result:
  The session is authenticated as `tom` and stores Tom’s user id.
- Endpoint sequence:
  Step 1: `POST /IDOR/login`
- Constraints:
  `username=tom` and `password=cat`. The response state is stored in lesson session keys `idor-authenticated-as` and `idor-authenticated-user-id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username is not `tom` or password is not `cat`.
  - Endpoint group:
    Step 1: `POST /IDOR/login`
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
  Establishes session state required by later IDOR behaviors.

### Behavior 9: retrieve own IDOR profile

Behavior name:
retrieve own IDOR profile

Successful execution:
- Result:
  The API returns Tom’s own profile.
- Endpoint sequence:
  Step 1: `POST /IDOR/login`
  Step 2: `GET /IDOR/own`
- Constraints:
  Step 1 must authenticate as `tom`. `GET /IDOR/profile` is an alternate path for the same implementation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The IDOR login prerequisite is omitted.
  - Endpoint group:
    Step 1: `GET /IDOR/own`
  - Failure endpoint:
    `GET /IDOR/own`
  - Why this fails:
    The session does not contain `idor-authenticated-as=tom`, so the implementation returns no profile details.
  - Intentionally violated constraints:
    Omitted `POST /IDOR/login`.

Endpoint coverage:
- Covers:
  `GET /IDOR/own`
  `GET /IDOR/profile`
- Distinct meaning:
  Reads the authenticated user’s profile from session state.

### Behavior 10: verify alternate own-profile URL

Behavior name:
verify alternate own-profile URL

Successful execution:
- Result:
  The lesson accepts a URL pointing to Tom’s own profile id.
- Endpoint sequence:
  Step 1: `POST /IDOR/login`
  Step 2: `POST /IDOR/profile/alt-path`
- Constraints:
  Step 2 must submit `url=WebGoat/IDOR/profile/{authUserId}`, where `{authUserId}` is the Tom id stored by Step 1, `2342384`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The URL path does not match `WebGoat/IDOR/profile/{authUserId}`.
  - Endpoint group:
    Step 1: `POST /IDOR/login`
    Step 2: `POST /IDOR/profile/alt-path`
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

### Behavior 11: identify IDOR-exposed attributes

Behavior name:
identify IDOR-exposed attributes

Successful execution:
- Result:
  The API accepts `userId` and `role` as the differing attributes.
- Endpoint sequence:
  Step 1: `POST /IDOR/diff-attributes`
- Constraints:
  The comma-separated `attributes` value must contain both `userid` and `role`, in either order, case-insensitively.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Fewer than two attributes are submitted.
  - Endpoint group:
    Step 1: `POST /IDOR/diff-attributes`
  - Failure endpoint:
    `POST /IDOR/diff-attributes`
  - Why this fails:
    The implementation requires at least two comma-separated values.
  - Intentionally violated constraints:
    Missing second attribute.
- Branch 2:
  - Unsatisfied condition:
    The pair is not `userid` and `role`.
  - Endpoint group:
    Step 1: `POST /IDOR/diff-attributes`
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

### Behavior 12: retrieve another IDOR profile

Behavior name:
retrieve another IDOR profile

Successful execution:
- Result:
  The API returns Bill’s profile while authenticated as Tom.
- Endpoint sequence:
  Step 1: `POST /IDOR/login`
  Step 2: `GET /IDOR/profile/{userId}`
- Constraints:
  Step 1 must authenticate as Tom. Step 2 must use `{userId}=2342388`, which is Bill’s seeded profile id and must differ from Tom’s id `2342384`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The login prerequisite is omitted.
  - Endpoint group:
    Step 1: `GET /IDOR/profile/{userId}`
  - Failure endpoint:
    `GET /IDOR/profile/{userId}`
  - Why this fails:
    The session lacks `idor-authenticated-as=tom`.
  - Intentionally violated constraints:
    Omitted `POST /IDOR/login`.
- Branch 2:
  - Unsatisfied condition:
    `{userId}` equals Tom’s own id or is not Bill’s id.
  - Endpoint group:
    Step 1: `POST /IDOR/login`
    Step 2: `GET /IDOR/profile/{userId}`
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

### Behavior 13: edit another IDOR profile

Behavior name:
edit another IDOR profile

Successful execution:
- Result:
  The API updates another profile in session and marks the lesson solved.
- Endpoint sequence:
  Step 1: `POST /IDOR/login`
  Step 2: `PUT /IDOR/profile/{userId}`
- Constraints:
  Step 1 must authenticate as Tom. Step 2 must use a body `userId` that is present and not equal to Tom’s authenticated id. The submitted body must set `color=red` and `role<=1`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The submitted body `userId` equals Tom’s authenticated id.
  - Endpoint group:
    Step 1: `POST /IDOR/login`
    Step 2: `PUT /IDOR/profile/{userId}`
  - Failure endpoint:
    `PUT /IDOR/profile/{userId}`
  - Why this fails:
    The endpoint rejects editing the authenticated user through the “other profile” branch.
  - Intentionally violated constraints:
    Body `userId` matches authenticated id.
- Branch 2:
  - Unsatisfied condition:
    Role is greater than 1 or color is not `red`.
  - Endpoint group:
    Step 1: `POST /IDOR/login`
    Step 2: `PUT /IDOR/profile/{userId}`
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

### Behavior 14: submit simple XXE comment

Behavior name:
submit simple XXE comment

Successful execution:
- Result:
  The API parses an XML comment, stores it for the current user, and marks the lesson solved if the text contains an operating-system directory marker.
- Endpoint sequence:
  Step 1: `POST /xxe/simple`
- Constraints:
  The body is parsed as XML `comment`. The parsed `text` must include one of `usr`, `etc`, or `var` on Unix-like systems, or one of the Windows directory markers on Windows.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The XML cannot be parsed.
  - Endpoint group:
    Step 1: `POST /xxe/simple`
  - Failure endpoint:
    `POST /xxe/simple`
  - Why this fails:
    XML parsing throws and the stack trace is returned in the failed `AttackResult`.
  - Intentionally violated constraints:
    Invalid XML body.
- Branch 2:
  - Unsatisfied condition:
    Parsed comment text does not contain an expected directory marker.
  - Endpoint group:
    Step 1: `POST /xxe/simple`
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

### Behavior 15: retrieve XXE comments

Behavior name:
retrieve XXE comments

Successful execution:
- Result:
  The API returns default comments plus comments stored for the current user.
- Endpoint sequence:
  Step 1: `GET /xxe/comments`
- Constraints:
  No setup endpoint is required because default comments exist.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /xxe/comments`
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

### Behavior 16: retrieve sample DTD template

Behavior name:
retrieve sample DTD template

Successful execution:
- Result:
  The API returns a plain-text DTD template for the blind XXE lesson.
- Endpoint sequence:
  Step 1: `GET /xxe/sampledtd`
- Constraints:
  The source uses `@RequestMapping` without a method restriction, so all documented methods return the same template.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /xxe/sampledtd`
  - Failure endpoint:
    `GET /xxe/sampledtd`
  - Why this fails:
    Normal execution always returns the static template.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /xxe/sampledtd`
  `PUT /xxe/sampledtd`
  `POST /xxe/sampledtd`
  `DELETE /xxe/sampledtd`
  `OPTIONS /xxe/sampledtd`
  `HEAD /xxe/sampledtd`
  `PATCH /xxe/sampledtd`
- Distinct meaning:
  All documented methods map to the same DTD-template retrieval behavior.

### Behavior 17: submit content-type XXE payload

Behavior name:
submit content-type XXE payload

Successful execution:
- Result:
  The API accepts an XML request body and solves the content-type XXE lesson when parsed text contains expected directory content.
- Endpoint sequence:
  Step 1: `POST /xxe/content-type`
- Constraints:
  Header `Content-Type` must contain `application/xml`; the body must parse as XML `comment`; parsed `text` must contain an OS directory marker.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `Content-Type` is exactly `application/json`.
  - Endpoint group:
    Step 1: `POST /xxe/content-type`
  - Failure endpoint:
    `POST /xxe/content-type`
  - Why this fails:
    The implementation parses the body as JSON, stores the comment globally if valid, and returns JSON-specific failure feedback.
  - Intentionally violated constraints:
    Used JSON instead of XML.
- Branch 2:
  - Unsatisfied condition:
    XML body is invalid or lacks expected directory content.
  - Endpoint group:
    Step 1: `POST /xxe/content-type`
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

### Behavior 18: solve blind XXE by submitting extracted secret

Behavior name:
solve blind XXE by submitting extracted secret

Successful execution:
- Result:
  The API marks the blind XXE lesson solved when the submitted body contains the current user’s generated secret file content.
- Endpoint sequence:
  Step 1: `POST /xxe/blind`
- Constraints:
  The secret file content is created by lesson initialization, not by a documented Swagger endpoint. The request body must contain the exact generated secret string.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The body does not contain the generated secret.
  - Endpoint group:
    Step 1: `POST /xxe/blind`
  - Failure endpoint:
    `POST /xxe/blind`
  - Why this fails:
    The endpoint parses the body as XML comment and stores it, then returns failure.
  - Intentionally violated constraints:
    Missing exact secret string.
- Branch 2:
  - Unsatisfied condition:
    XML parsing fails.
  - Endpoint group:
    Step 1: `POST /xxe/blind`
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

### Behavior 19: solve sample lesson-template attack

Behavior name:
solve sample lesson-template attack

Successful execution:
- Result:
  The sample lesson returns success.
- Endpoint sequence:
  Step 1: `POST /lesson-template/sample-attack`
- Constraints:
  `param1` must equal `secr37Value`. `param2` is required by the OpenAPI contract but is not used by the implementation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `param1` is not `secr37Value`.
  - Endpoint group:
    Step 1: `POST /lesson-template/sample-attack`
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

### Behavior 20: retrieve sample shop basket

Behavior name:
retrieve sample shop basket

Successful execution:
- Result:
  The API returns a fixed sample basket for the path user.
- Endpoint sequence:
  Step 1: `GET /lesson-template/shop/{user}`
- Constraints:
  `{user}` is accepted but not used to customize the returned items.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /lesson-template/shop/{user}`
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

### Behavior 21: list CSRF reviews

Behavior name:
list CSRF reviews

Successful execution:
- Result:
  The API returns the current user’s reviews plus default reviews.
- Endpoint sequence:
  Step 1: `GET /csrf/review`
- Constraints:
  No prerequisite is required because default reviews are always present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /csrf/review`
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

### Behavior 22: create forged CSRF review

Behavior name:
create forged CSRF review

Successful execution:
- Result:
  The API stores a review for the current user and marks the CSRF review lesson solved.
- Endpoint sequence:
  Step 1: `POST /csrf/review`
- Constraints:
  `reviewText`, `stars`, and `validateReq` are submitted. `validateReq` must equal `2aa14227b9a13d0bede0388a7fba9aa9`. The `Referer` header must be missing or from a different host than `Host`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `validateReq` is missing or wrong.
  - Endpoint group:
    Step 1: `POST /csrf/review`
  - Failure endpoint:
    `POST /csrf/review`
  - Why this fails:
    The review is still stored, but the lesson returns missing-token feedback.
  - Intentionally violated constraints:
    Wrong anti-CSRF value.
- Branch 2:
  - Unsatisfied condition:
    `Referer` host equals `Host`.
  - Endpoint group:
    Step 1: `POST /csrf/review`
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

### Behavior 23: verify CSRF login username

Behavior name:
verify CSRF login username

Successful execution:
- Result:
  The lesson succeeds when the authenticated username starts with `csrf`.
- Endpoint sequence:
  Step 1: `POST /csrf/login`
- Constraints:
  The current authenticated WebGoat username must start with `csrf`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Current username does not start with `csrf`.
  - Endpoint group:
    Step 1: `POST /csrf/login`
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

### Behavior 24: get and confirm CSRF basic flag

Behavior name:
get and confirm CSRF basic flag

Successful execution:
- Result:
  The API generates a CSRF flag and then accepts that same flag.
- Endpoint sequence:
  Step 1: `POST /csrf/basic-get-flag`
  Step 2: `POST /csrf/confirm-flag-1`
- Constraints:
  Step 1 succeeds when `Referer` is absent or from a different host. Step 1 stores `csrf-get-success`; Step 2 must send `confirmFlagVal` equal to that stored value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Step 1 uses a same-host `Referer`.
  - Endpoint group:
    Step 1: `POST /csrf/basic-get-flag`
  - Failure endpoint:
    `POST /csrf/basic-get-flag`
  - Why this fails:
    The response has `success=false` and no flag.
  - Intentionally violated constraints:
    Same-host referer.
- Branch 2:
  - Unsatisfied condition:
    Step 2 sends the wrong confirmation value or Step 1 was omitted.
  - Endpoint group:
    Step 1: `POST /csrf/confirm-flag-1`
  - Failure endpoint:
    `POST /csrf/confirm-flag-1`
  - Why this fails:
    The session does not contain the matching `csrf-get-success` value.
  - Intentionally violated constraints:
    Missing or mismatched generated flag.

Endpoint coverage:
- Covers:
  `POST /csrf/basic-get-flag`
  `POST /csrf/confirm-flag-1`
- Distinct meaning:
  Generates and confirms the CSRF GET-style lesson flag.

### Behavior 25: create and confirm CSRF feedback flag

Behavior name:
create and confirm CSRF feedback flag

Successful execution:
- Result:
  The API accepts a forged feedback message, returns a flag, and then confirms it.
- Endpoint sequence:
  Step 1: `POST /csrf/feedback/message`
  Step 2: `POST /csrf/feedback`
- Constraints:
  Step 1 body must be valid JSON text, request must contain `JSESSIONID`, `Content-Type` must contain `text/plain`, and `Referer` must be absent or different from `Host`. Step 2 must send `confirmFlagVal` equal to the flag stored by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Feedback body is invalid JSON.
  - Endpoint group:
    Step 1: `POST /csrf/feedback/message`
  - Failure endpoint:
    `POST /csrf/feedback/message`
  - Why this fails:
    Jackson parsing fails and the stack trace is returned as feedback.
  - Intentionally violated constraints:
    Invalid JSON body.
- Branch 2:
  - Unsatisfied condition:
    Cookie/content-type/referer conditions are not met.
  - Endpoint group:
    Step 1: `POST /csrf/feedback/message`
  - Failure endpoint:
    `POST /csrf/feedback/message`
  - Why this fails:
    `correctCSRF` remains false.
  - Intentionally violated constraints:
    Missing `JSESSIONID`, wrong content type, or same-host referer.
- Branch 3:
  - Unsatisfied condition:
    Confirmation value does not match the generated flag.
  - Endpoint group:
    Step 1: `POST /csrf/feedback/message`
    Step 2: `POST /csrf/feedback`
  - Failure endpoint:
    `POST /csrf/feedback`
  - Why this fails:
    The submitted value is compared to session key `csrf-feedback`.
  - Intentionally violated constraints:
    Wrong `confirmFlagVal`.

Endpoint coverage:
- Covers:
  `POST /csrf/feedback/message`
  `POST /csrf/feedback`
- Distinct meaning:
  Creates and confirms a CSRF feedback flag.

### Behavior 26: retrieve RSA private key and verify signature

Behavior name:
retrieve RSA private key and verify signature

Successful execution:
- Result:
  The API generates or returns the session private key, then accepts a matching modulus and signature.
- Endpoint sequence:
  Step 1: `GET /crypto/signing/getprivate`
  Step 2: `POST /crypto/signing/verify`
- Constraints:
  Step 1 creates session attributes `privateKeyString` and `keyPair` if missing. Step 2 must submit `modulus` equal to the generated public key modulus and `signature` that verifies against that public key. Any documented method on `/crypto/signing/getprivate` returns the same key.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Step 1 is omitted.
  - Endpoint group:
    Step 1: `POST /crypto/signing/verify`
  - Failure endpoint:
    `POST /crypto/signing/verify`
  - Why this fails:
    The implementation expects `keyPair` in the session.
  - Intentionally violated constraints:
    Missing key-generation endpoint.
- Branch 2:
  - Unsatisfied condition:
    Modulus or signature is wrong.
  - Endpoint group:
    Step 1: `GET /crypto/signing/getprivate`
    Step 2: `POST /crypto/signing/verify`
  - Failure endpoint:
    `POST /crypto/signing/verify`
  - Why this fails:
    The modulus check or signature verification fails.
  - Intentionally violated constraints:
    Mismatched cryptographic values.

Endpoint coverage:
- Covers:
  `GET /crypto/signing/getprivate`
  `PUT /crypto/signing/getprivate`
  `POST /crypto/signing/getprivate`
  `DELETE /crypto/signing/getprivate`
  `OPTIONS /crypto/signing/getprivate`
  `HEAD /crypto/signing/getprivate`
  `PATCH /crypto/signing/getprivate`
  `POST /crypto/signing/verify`
- Distinct meaning:
  The all-method key endpoint is setup for signature verification.

### Behavior 27: verify secure defaults secret

Behavior name:
verify secure defaults secret

Successful execution:
- Result:
  The API marks the secure-defaults lesson solved.
- Endpoint sequence:
  Step 1: `POST /crypto/secure/defaults`
- Constraints:
  `secretFileName` must be `default_secret`. `secretText` must hash with SHA-256 to `34de66e5caf2cb69ff2bebdc1f3091ecf6296852446c718e38ebfa60e4aa75d2`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `secretFileName` is not `default_secret`.
  - Endpoint group:
    Step 1: `POST /crypto/secure/defaults`
  - Failure endpoint:
    `POST /crypto/secure/defaults`
  - Why this fails:
    The filename check is the outer success gate.
  - Intentionally violated constraints:
    Wrong secret filename.
- Branch 2:
  - Unsatisfied condition:
    `secretText` hashes to a different value.
  - Endpoint group:
    Step 1: `POST /crypto/secure/defaults`
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

### Behavior 28: crack generated MD5 and SHA-256 hashes

Behavior name:
crack generated MD5 and SHA-256 hashes

Successful execution:
- Result:
  The API returns session hashes and then accepts the recovered plaintexts.
- Endpoint sequence:
  Step 1: `GET /crypto/hashing/md5`
  Step 2: `GET /crypto/hashing/sha256`
  Step 3: `POST /crypto/hashing`
- Constraints:
  Step 1 stores `md5Secret`; Step 2 stores `sha256Secret`. Step 3 must submit `answer_pwd1` equal to `md5Secret` and `answer_pwd2` equal to `sha256Secret`. Any documented method on the two hash endpoints behaves as the same retrieval operation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    One or both retrieval endpoints are omitted.
  - Endpoint group:
    Step 1: `POST /crypto/hashing`
  - Failure endpoint:
    `POST /crypto/hashing`
  - Why this fails:
    The expected session secrets are missing.
  - Intentionally violated constraints:
    Omitted hash-generation endpoints.
- Branch 2:
  - Unsatisfied condition:
    Only one recovered password is correct.
  - Endpoint group:
    Step 1: `GET /crypto/hashing/md5`
    Step 2: `GET /crypto/hashing/sha256`
    Step 3: `POST /crypto/hashing`
  - Failure endpoint:
    `POST /crypto/hashing`
  - Why this fails:
    The implementation returns a specific “one ok” failure when only one answer matches.
  - Intentionally violated constraints:
    One mismatched plaintext.

Endpoint coverage:
- Covers:
  `GET /crypto/hashing/md5`
  `PUT /crypto/hashing/md5`
  `POST /crypto/hashing/md5`
  `DELETE /crypto/hashing/md5`
  `OPTIONS /crypto/hashing/md5`
  `HEAD /crypto/hashing/md5`
  `PATCH /crypto/hashing/md5`
  `GET /crypto/hashing/sha256`
  `PUT /crypto/hashing/sha256`
  `POST /crypto/hashing/sha256`
  `DELETE /crypto/hashing/sha256`
  `OPTIONS /crypto/hashing/sha256`
  `HEAD /crypto/hashing/sha256`
  `PATCH /crypto/hashing/sha256`
  `POST /crypto/hashing`
- Distinct meaning:
  The hash endpoints create session state consumed by the answer endpoint.

### Behavior 29: solve XOR encoding password

Behavior name:
solve XOR encoding password

Successful execution:
- Result:
  The API accepts the decoded XOR password.
- Endpoint sequence:
  Step 1: `POST /crypto/encoding/xor`
- Constraints:
  `answer_pwd1` must equal `databasepassword`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `answer_pwd1` is missing or not `databasepassword`.
  - Endpoint group:
    Step 1: `POST /crypto/encoding/xor`
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

### Behavior 30: decode generated Basic Auth header

Behavior name:
decode generated Basic Auth header

Successful execution:
- Result:
  The API returns a Basic Auth header and then accepts the decoded username/password pair.
- Endpoint sequence:
  Step 1: `GET /crypto/encoding/basic`
  Step 2: `POST /crypto/encoding/basic-auth`
- Constraints:
  Step 1 stores `basicAuth` in the session for the current principal. Step 2 must submit `answer_user` and `answer_pwd` such that Base64 of `answer_user:answer_pwd` equals that stored value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Step 1 is omitted.
  - Endpoint group:
    Step 1: `POST /crypto/encoding/basic-auth`
  - Failure endpoint:
    `POST /crypto/encoding/basic-auth`
  - Why this fails:
    The session has no generated `basicAuth` value.
  - Intentionally violated constraints:
    Omitted `GET /crypto/encoding/basic`.
- Branch 2:
  - Unsatisfied condition:
    Decoded user/password pair does not match.
  - Endpoint group:
    Step 1: `GET /crypto/encoding/basic`
    Step 2: `POST /crypto/encoding/basic-auth`
  - Failure endpoint:
    `POST /crypto/encoding/basic-auth`
  - Why this fails:
    The recomputed Basic Auth token differs from the session token.
  - Intentionally violated constraints:
    Wrong decoded credentials.

Endpoint coverage:
- Covers:
  `GET /crypto/encoding/basic`
  `POST /crypto/encoding/basic-auth`
- Distinct meaning:
  Retrieves and verifies a Basic Auth encoding challenge.

### Behavior 31: retrieve coupon codes

Behavior name:
retrieve coupon codes

Successful execution:
- Result:
  The API returns all checkout coupon codes, including the 100% coupon.
- Endpoint sequence:
  Step 1: `GET /clientSideFiltering/challenge-store/coupons`
- Constraints:
  No prerequisite is required; coupon codes are in memory.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /clientSideFiltering/challenge-store/coupons`
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

### Behavior 32: retrieve one coupon code

Behavior name:
retrieve one coupon code

Successful execution:
- Result:
  The API returns the matching coupon code and discount.
- Endpoint sequence:
  Step 1: `GET /clientSideFiltering/challenge-store/coupons/{code}`
- Constraints:
  `{code}` can be one of the built-in codes such as `webgoat`, `owasp`, `owasp-webgoat`, or `get_it_for_free`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is unknown.
  - Endpoint group:
    Step 1: `GET /clientSideFiltering/challenge-store/coupons/{code}`
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

### Behavior 33: complete free checkout

Behavior name:
complete free checkout

Successful execution:
- Result:
  The API accepts the super coupon and marks the assignment solved.
- Endpoint sequence:
  Step 1: `GET /clientSideFiltering/challenge-store/coupons`
  Step 2: `POST /clientSideFiltering/getItForFree`
- Constraints:
  Step 1 is one documented way to discover `get_it_for_free`. Step 2 must submit `checkoutCode=get_it_for_free`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `checkoutCode` is not `get_it_for_free`.
  - Endpoint group:
    Step 1: `POST /clientSideFiltering/getItForFree`
  - Failure endpoint:
    `POST /clientSideFiltering/getItForFree`
  - Why this fails:
    The endpoint compares directly with the super coupon constant.
  - Intentionally violated constraints:
    Wrong checkout code.

Endpoint coverage:
- Covers:
  `POST /clientSideFiltering/getItForFree`
  `GET /clientSideFiltering/challenge-store/coupons`
- Distinct meaning:
  Uses the discovered coupon to solve the checkout task.

### Behavior 34: retrieve salaries and submit hidden salary

Behavior name:
retrieve salaries and submit hidden salary

Successful execution:
- Result:
  The API exposes employee salary data and accepts the target salary answer.
- Endpoint sequence:
  Step 1: `GET /clientSideFiltering/salaries`
  Step 2: `POST /clientSideFiltering/attack1`
- Constraints:
  Step 2 must submit `answer=450000`. Step 1 is the documented endpoint that exposes salary data.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `answer` is not `450000`.
  - Endpoint group:
    Step 1: `GET /clientSideFiltering/salaries`
    Step 2: `POST /clientSideFiltering/attack1`
  - Failure endpoint:
    `POST /clientSideFiltering/attack1`
  - Why this fails:
    The endpoint performs a direct string comparison.
  - Intentionally violated constraints:
    Wrong salary answer.

Endpoint coverage:
- Covers:
  `GET /clientSideFiltering/salaries`
  `POST /clientSideFiltering/attack1`
- Distinct meaning:
  Reads client-side data and submits the discovered value.

### Behavior 35: submit CIA quiz and read results

Behavior name:
submit CIA quiz and read results

Successful execution:
- Result:
  The API records all CIA quiz answers as correct and returns the correctness array.
- Endpoint sequence:
  Step 1: `POST /cia/quiz`
  Step 2: `GET /cia/quiz`
- Constraints:
  Step 1 must include answers containing `Solution 3`, `Solution 1`, `Solution 4`, and `Solution 2` for questions 0 through 3. Step 2 reads the `guesses` state set by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    One or more submitted answers do not contain the expected solution string.
  - Endpoint group:
    Step 1: `POST /cia/quiz`
    Step 2: `GET /cia/quiz`
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

### Behavior 36: bypass account verification questions

Behavior name:
bypass account verification questions

Successful execution:
- Result:
  The API verifies the account and stores the verified account id in the lesson session.
- Endpoint sequence:
  Step 1: `POST /auth-bypass/verify-account`
- Constraints:
  `userId` is required by Swagger but not actually used by `verifyAccount` for lookup. The request must include exactly two parameters whose names contain `secQuestion`. To avoid the “cheated” branch, use alternate question names rather than both exact `secQuestion0` and `secQuestion1` with exact answers.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Exact known answers are submitted for both exact keys.
  - Endpoint group:
    Step 1: `POST /auth-bypass/verify-account`
  - Failure endpoint:
    `POST /auth-bypass/verify-account`
  - Why this fails:
    `didUserLikelylCheat` detects the direct answers and returns cheated feedback.
  - Intentionally violated constraints:
    Used exact expected keys and values.
- Branch 2:
  - Unsatisfied condition:
    The number of submitted security-question parameters is not exactly two, or one known exact key has a wrong value.
  - Endpoint group:
    Step 1: `POST /auth-bypass/verify-account`
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

### Behavior 37: list access-control users

Behavior name:
list access-control users

Successful execution:
- Result:
  The API returns users with hashes generated using the simple salt.
- Endpoint sequence:
  Step 1: `GET /access-control/users`
- Constraints:
  No setup is required because the users table is seeded.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /access-control/users`
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

### Behavior 38: add access-control user

Behavior name:
add access-control user

Successful execution:
- Result:
  The API inserts a new access-control user and returns that user.
- Endpoint sequence:
  Step 1: `POST /access-control/users`
- Constraints:
  The JSON body maps to `User` with `username`, `password`, and `admin`. The same add method is also exposed at `/access-control/users-admin-fix`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Insert fails, for example because the body cannot be saved.
  - Endpoint group:
    Step 1: `POST /access-control/users`
  - Failure endpoint:
    `POST /access-control/users`
  - Why this fails:
    The repository exception is caught and the endpoint returns `null`.
  - Intentionally violated constraints:
    Invalid or unsavable user body.

Endpoint coverage:
- Covers:
  `POST /access-control/users`
  `POST /access-control/users-admin-fix`
- Distinct meaning:
  Creates an access-control user; there is no additional admin check on either POST path.

### Behavior 39: list users through admin-fixed endpoint

Behavior name:
list users through admin-fixed endpoint

Successful execution:
- Result:
  The API returns users with hashes generated using the admin salt.
- Endpoint sequence:
  Step 1: `POST /access-control/users-admin-fix`
  Step 2: `GET /access-control/users-admin-fix`
- Constraints:
  Step 1 is one documented way to create a row for the current WebGoat username with `admin=true`. Step 2 checks `@CurrentUsername` against the repository and succeeds only if that user is admin.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Current username is missing from the access-control table or is not admin.
  - Endpoint group:
    Step 1: `GET /access-control/users-admin-fix`
  - Failure endpoint:
    `GET /access-control/users-admin-fix`
  - Why this fails:
    The endpoint returns HTTP 403 when `currentUser` is null or not admin.
  - Intentionally violated constraints:
    Omitted or non-admin current-user row.

Endpoint coverage:
- Covers:
  `GET /access-control/users-admin-fix`
  `POST /access-control/users-admin-fix`
- Distinct meaning:
  POST can establish the admin row; GET enforces it.

### Behavior 40: submit simple user hash

Behavior name:
submit simple user hash

Successful execution:
- Result:
  The API accepts Jerry’s hash generated with the simple salt.
- Endpoint sequence:
  Step 1: `GET /access-control/users`
  Step 2: `POST /access-control/user-hash`
- Constraints:
  Step 1 exposes Jerry’s simple-salt `userHash`. Step 2 must submit that exact value as `userHash`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Submitted hash is not Jerry’s simple-salt hash.
  - Endpoint group:
    Step 1: `GET /access-control/users`
    Step 2: `POST /access-control/user-hash`
  - Failure endpoint:
    `POST /access-control/user-hash`
  - Why this fails:
    The endpoint compares against `new DisplayUser(Jerry, PASSWORD_SALT_SIMPLE).userHash`.
  - Intentionally violated constraints:
    Wrong hash or wrong user’s hash.

Endpoint coverage:
- Covers:
  `POST /access-control/user-hash`
  `GET /access-control/users`
- Distinct meaning:
  Uses the exposed simple-salt user list to solve the hash check.

### Behavior 41: submit admin-salt user hash

Behavior name:
submit admin-salt user hash

Successful execution:
- Result:
  The API accepts Jerry’s hash generated with the admin salt.
- Endpoint sequence:
  Step 1: `POST /access-control/users-admin-fix`
  Step 2: `GET /access-control/users-admin-fix`
  Step 3: `POST /access-control/user-hash-fix`
- Constraints:
  Step 1 makes the current WebGoat username an admin in the access-control table. Step 2 exposes Jerry’s admin-salt hash. Step 3 must submit that exact hash as `userHash`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Admin-fixed user list cannot be accessed.
  - Endpoint group:
    Step 1: `GET /access-control/users-admin-fix`
  - Failure endpoint:
    `GET /access-control/users-admin-fix`
  - Why this fails:
    The current user is not an admin access-control user.
  - Intentionally violated constraints:
    Omitted admin setup.
- Branch 2:
  - Unsatisfied condition:
    Submitted hash is not Jerry’s admin-salt hash.
  - Endpoint group:
    Step 1: `POST /access-control/users-admin-fix`
    Step 2: `GET /access-control/users-admin-fix`
    Step 3: `POST /access-control/user-hash-fix`
  - Failure endpoint:
    `POST /access-control/user-hash-fix`
  - Why this fails:
    The endpoint compares against `PASSWORD_SALT_ADMIN`.
  - Intentionally violated constraints:
    Wrong hash.

Endpoint coverage:
- Covers:
  `POST /access-control/user-hash-fix`
  `GET /access-control/users-admin-fix`
- Distinct meaning:
  Uses the admin-protected list to solve the stronger hash check.

### Behavior 42: submit hidden-menu names

Behavior name:
submit hidden-menu names

Successful execution:
- Result:
  The API accepts the hidden menu names.
- Endpoint sequence:
  Step 1: `POST /access-control/hidden-menu`
- Constraints:
  `hiddenMenu1=Users` and `hiddenMenu2=Config`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Values are reversed.
  - Endpoint group:
    Step 1: `POST /access-control/hidden-menu`
  - Failure endpoint:
    `POST /access-control/hidden-menu`
  - Why this fails:
    Reversed values are treated as close but not successful.
  - Intentionally violated constraints:
    `hiddenMenu1=Config`, `hiddenMenu2=Users`.
- Branch 2:
  - Unsatisfied condition:
    Values are not the expected pair.
  - Endpoint group:
    Step 1: `POST /access-control/hidden-menu`
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

### Behavior 43: send and verify WebWolf mail code

Behavior name:
send and verify WebWolf mail code

Successful execution:
- Result:
  The API sends a mail containing the reversed username and then accepts that code.
- Endpoint sequence:
  Step 1: `POST /WebWolf/mail/send`
  Step 2: `POST /WebWolf/mail`
- Constraints:
  Step 1 `email` local part must equal the current WebGoat username. The mail content contains `reverse(username)`. Step 2 must submit `uniqueCode=reverse(username)`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Email local part does not match the current username.
  - Endpoint group:
    Step 1: `POST /WebWolf/mail/send`
  - Failure endpoint:
    `POST /WebWolf/mail/send`
  - Why this fails:
    The endpoint returns mismatch information and does not send the intended mail.
  - Intentionally violated constraints:
    Mismatched email local part.
- Branch 2:
  - Unsatisfied condition:
    Submitted code is not the reversed username.
  - Endpoint group:
    Step 1: `POST /WebWolf/mail/send`
    Step 2: `POST /WebWolf/mail`
  - Failure endpoint:
    `POST /WebWolf/mail`
  - Why this fails:
    The code is compared to `StringUtils.reverse(username)`.
  - Intentionally violated constraints:
    Wrong `uniqueCode`.

Endpoint coverage:
- Covers:
  `POST /WebWolf/mail/send`
  `POST /WebWolf/mail`
- Distinct meaning:
  Sends and validates the WebWolf email code.

### Behavior 44: verify WebWolf landing code

Behavior name:
verify WebWolf landing code

Successful execution:
- Result:
  The API accepts the landing-page code.
- Endpoint sequence:
  Step 1: `POST /WebWolf/landing`
- Constraints:
  `uniqueCode` must equal the current username reversed. The source has a `GET /WebWolf/landing/password-reset` helper that exposes this code, but that helper is not present in the Swagger file.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `uniqueCode` is not the reversed current username.
  - Endpoint group:
    Step 1: `POST /WebWolf/landing`
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

### Behavior 45: exploit vulnerable XML component

Behavior name:
exploit vulnerable XML component

Successful execution:
- Result:
  The API parses the submitted XStream XML and marks the lesson solved when parsing produces a non-`ContactImpl` object or triggers an exception during contact access.
- Endpoint sequence:
  Step 1: `POST /VulnerableComponents/attack1`
- Constraints:
  `payload` must be XML acceptable to XStream and must not simply deserialize to the normal `ContactImpl` object.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    XML parsing fails.
  - Endpoint group:
    Step 1: `POST /VulnerableComponents/attack1`
  - Failure endpoint:
    `POST /VulnerableComponents/attack1`
  - Why this fails:
    XStream throws and the endpoint returns failure feedback.
  - Intentionally violated constraints:
    Invalid XStream XML.
- Branch 2:
  - Unsatisfied condition:
    Payload deserializes to an ordinary `ContactImpl`.
  - Endpoint group:
    Step 1: `POST /VulnerableComponents/attack1`
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
  Exercises the vulnerable XStream parsing behavior.

### Behavior 46: run SQL injection introduction queries

Behavior name:
run SQL injection introduction queries

Successful execution:
- Result:
  The API executes submitted SQL and marks specific introductory SQL injection lessons solved when their database-side checks pass.
- Endpoint sequence:
  Step 1: `POST /SqlInjection/attack2`
  Step 2: `POST /SqlInjection/attack3`
  Step 3: `POST /SqlInjection/attack4`
  Step 4: `POST /SqlInjection/attack5`
  Step 5: `POST /SqlInjection/attack8`
  Step 6: `POST /SqlInjection/attack9`
  Step 7: `POST /SqlInjection/attack10`
- Constraints:
  Each endpoint is independently executable; the sequence shown covers the family, not a required workflow across all seven. Successful inputs must respectively: return a Marketing employee, change Tobi Barnett’s department to Sales, add a `phone` column, grant rights to `UNAUTHORIZED_USER`, return more than one employee row, raise John Smith’s salary above the old max without changing other salaries, and make `access_log` unavailable.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    SQL does not produce the endpoint-specific expected database state.
  - Endpoint group:
    Step 1: `POST /SqlInjection/attack2`
  - Failure endpoint:
    `POST /SqlInjection/attack2`
  - Why this fails:
    The result department is not Marketing.
  - Intentionally violated constraints:
    Wrong query effect.
- Branch 2:
  - Unsatisfied condition:
    SQL syntax or database execution fails.
  - Endpoint group:
    Step 1: `POST /SqlInjection/attack3`
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

### Behavior 47: solve SQL injection assignment 5a

Behavior name:
solve SQL injection assignment 5a

Successful execution:
- Result:
  The API returns multiple `user_data` rows and marks the assignment solved.
- Endpoint sequence:
  Step 1: `POST /SqlInjection/assignment5a`
- Constraints:
  `account`, `operator`, and `injection` are concatenated into the `last_name` predicate. The resulting query must return at least six rows.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The injected predicate returns no rows or fewer than six rows.
  - Endpoint group:
    Step 1: `POST /SqlInjection/assignment5a`
  - Failure endpoint:
    `POST /SqlInjection/assignment5a`
  - Why this fails:
    The endpoint checks `results.getRow() >= 6`.
  - Intentionally violated constraints:
    Insufficient query results.
- Branch 2:
  - Unsatisfied condition:
    SQL execution fails.
  - Endpoint group:
    Step 1: `POST /SqlInjection/assignment5a`
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

### Behavior 48: solve SQL injection assignment 5b

Behavior name:
solve SQL injection assignment 5b

Successful execution:
- Result:
  The API returns multiple `user_data` rows and marks the assignment solved.
- Endpoint sequence:
  Step 1: `POST /SqlInjection/assignment5b`
- Constraints:
  `login_count` must parse as an integer because it is bound as a prepared-statement parameter. `userid` is concatenated into SQL and must cause at least six rows to return.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `login_count` is not numeric.
  - Endpoint group:
    Step 1: `POST /SqlInjection/assignment5b`
  - Failure endpoint:
    `POST /SqlInjection/assignment5b`
  - Why this fails:
    `Integer.parseInt(login_count)` fails before query execution.
  - Intentionally violated constraints:
    Non-numeric `login_count`.
- Branch 2:
  - Unsatisfied condition:
    Query returns no rows or fewer than six rows.
  - Endpoint group:
    Step 1: `POST /SqlInjection/assignment5b`
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

### Behavior 49: perform advanced SQL injection extraction

Behavior name:
perform advanced SQL injection extraction

Successful execution:
- Result:
  The API returns output containing Dave’s password and marks the advanced injection lesson solved.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionAdvanced/attack6a`
- Constraints:
  `userid_6a` is embedded in a `last_name` query and must produce output containing both `dave` and `passW0rD`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Query returns no rows.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/attack6a`
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/attack6a`
  - Why this fails:
    The endpoint checks for a first result row before validating output.
  - Intentionally violated constraints:
    No matching/extracted rows.
- Branch 2:
  - Unsatisfied condition:
    Output does not contain Dave’s username and password.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/attack6a`
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

### Behavior 50: submit extracted Dave password

Behavior name:
submit extracted Dave password

Successful execution:
- Result:
  The API accepts Dave’s password from `user_system_data`.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionAdvanced/attack6a`
  Step 2: `POST /SqlInjectionAdvanced/attack6b`
- Constraints:
  Step 1 is the documented way to extract the password. Step 2 must submit `userid_6b` equal to the database password for user `dave`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `userid_6b` does not equal Dave’s password.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/attack6b`
  - Failure endpoint:
    `POST /SqlInjectionAdvanced/attack6b`
  - Why this fails:
    The endpoint compares the submitted value to `getPassword()`.
  - Intentionally violated constraints:
    Wrong extracted password.

Endpoint coverage:
- Covers:
  `POST /SqlInjectionAdvanced/attack6b`
  `POST /SqlInjectionAdvanced/attack6a`
- Distinct meaning:
  Confirms the credential extracted in the previous advanced SQL behavior.

### Behavior 51: submit SQL advanced quiz and read results

Behavior name:
submit SQL advanced quiz and read results

Successful execution:
- Result:
  The API records all advanced SQL quiz answers as correct and returns the correctness array.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionAdvanced/quiz`
  Step 2: `GET /SqlInjectionAdvanced/quiz`
- Constraints:
  Answers must contain `Solution 4`, `Solution 3`, `Solution 2`, `Solution 3`, and `Solution 4` for questions 0 through 4.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Any answer is wrong.
  - Endpoint group:
    Step 1: `POST /SqlInjectionAdvanced/quiz`
    Step 2: `GET /SqlInjectionAdvanced/quiz`
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

### Behavior 52: solve SQL mitigation fill-in task

Behavior name:
solve SQL mitigation fill-in task

Successful execution:
- Result:
  The API accepts the prepared-statement code fragments.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionMitigations/attack10a`
- Constraints:
  Fields 1 through 7 must contain, in order: `getConnection`, `PreparedStatement`, `prepareStatement`, `?`, `?`, `setString`, and `setString`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Any field lacks the expected fragment at its position.
  - Endpoint group:
    Step 1: `POST /SqlInjectionMitigations/attack10a`
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

### Behavior 53: submit compiling SQL mitigation code

Behavior name:
submit compiling SQL mitigation code

Successful execution:
- Result:
  The API accepts submitted Java code that uses a prepared statement safely and compiles.
- Endpoint sequence:
  Step 1: `POST /SqlInjectionMitigations/attack10b`
- Constraints:
  `editor` must contain connection setup, `PreparedStatement`, a `=?` placeholder, `setString`, and `execute` or `executeUpdate`. The stripped code must compile inside the generated wrapper class.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `editor` is empty.
  - Endpoint group:
    Step 1: `POST /SqlInjectionMitigations/attack10b`
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10b`
  - Why this fails:
    Empty code is rejected before compilation.
  - Intentionally violated constraints:
    Empty code.
- Branch 2:
  - Unsatisfied condition:
    Code contains required fragments but does not compile.
  - Endpoint group:
    Step 1: `POST /SqlInjectionMitigations/attack10b`
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack10b`
  - Why this fails:
    Compiler diagnostics are returned.
  - Intentionally violated constraints:
    Non-compiling Java.
- Branch 3:
  - Unsatisfied condition:
    Code compiles but lacks required prepared-statement patterns.
  - Endpoint group:
    Step 1: `POST /SqlInjectionMitigations/attack10b`
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

### Behavior 54: verify production server IP

Behavior name:
verify production server IP

Successful execution:
- Result:
  The API accepts the IP for `webgoat-prd`.
- Endpoint sequence:
  Step 1: `GET /SqlInjectionMitigations/servers`
  Step 2: `POST /SqlInjectionMitigations/attack12a`
- Constraints:
  Step 1 lists non-out-of-order servers only, so it may not expose `webgoat-prd` because source data marks that server out of order. Step 2 succeeds only when `ip=104.130.219.202` and hostname `webgoat-prd` exists in the database.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Submitted IP does not belong to `webgoat-prd`.
  - Endpoint group:
    Step 1: `POST /SqlInjectionMitigations/attack12a`
  - Failure endpoint:
    `POST /SqlInjectionMitigations/attack12a`
  - Why this fails:
    The prepared statement finds no row for `ip` and `hostname=webgoat-prd`.
  - Intentionally violated constraints:
    Wrong IP.
- Branch 2:
  - Unsatisfied condition:
    `column` in Step 1 is invalid SQL.
  - Endpoint group:
    Step 1: `GET /SqlInjectionMitigations/servers`
  - Failure endpoint:
    `GET /SqlInjectionMitigations/servers`
  - Why this fails:
    The `column` parameter is concatenated into `order by`.
  - Intentionally violated constraints:
    Invalid sort expression.

Endpoint coverage:
- Covers:
  `GET /SqlInjectionMitigations/servers`
  `POST /SqlInjectionMitigations/attack12a`
- Distinct meaning:
  Lists sortable servers and validates the production server IP.

### Behavior 55: bypass space-only input validation

Behavior name:
bypass space-only input validation

Successful execution:
- Result:
  The API forwards the input to the advanced SQL extraction logic and returns that result under the mitigation assignment.
- Endpoint sequence:
  Step 1: `POST /SqlOnlyInputValidation/attack`
- Constraints:
  `userid_sql_only_input_validation` must not contain a literal space. After that check, the value must still make the delegated `SqlInjectionLesson6a.injectableQuery` succeed.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Input contains a literal space.
  - Endpoint group:
    Step 1: `POST /SqlOnlyInputValidation/attack`
  - Failure endpoint:
    `POST /SqlOnlyInputValidation/attack`
  - Why this fails:
    The endpoint rejects any input containing a space before delegation.
  - Intentionally violated constraints:
    Space in input.
- Branch 2:
  - Unsatisfied condition:
    Delegated advanced SQL injection does not extract the expected output.
  - Endpoint group:
    Step 1: `POST /SqlOnlyInputValidation/attack`
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

### Behavior 56: bypass keyword-stripping input validation

Behavior name:
bypass keyword-stripping input validation

Successful execution:
- Result:
  The API strips `FROM` and `SELECT`, then forwards the transformed input to advanced SQL extraction and returns that result.
- Endpoint sequence:
  Step 1: `POST /SqlOnlyInputValidationOnKeywords/attack`
- Constraints:
  The submitted `userid_sql_only_input_validation_on_keywords` is uppercased, has `FROM` and `SELECT` removed, and must not contain a space after that transformation. The transformed value must still solve the delegated advanced SQL injection.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Transformed input contains a space.
  - Endpoint group:
    Step 1: `POST /SqlOnlyInputValidationOnKeywords/attack`
  - Failure endpoint:
    `POST /SqlOnlyInputValidationOnKeywords/attack`
  - Why this fails:
    The endpoint rejects spaces after keyword removal.
  - Intentionally violated constraints:
    Space remains after transformation.
- Branch 2:
  - Unsatisfied condition:
    Delegated advanced SQL extraction fails.
  - Endpoint group:
    Step 1: `POST /SqlOnlyInputValidationOnKeywords/attack`
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

### Behavior 57: log in with spoofable cookie

Behavior name:
log in with spoofable cookie

Successful execution:
- Result:
  The API marks the spoof-cookie lesson solved when the `spoof_auth` cookie decodes to `tom`.
- Endpoint sequence:
  Step 1: `POST /SpoofCookie/login`
  Step 2: `POST /SpoofCookie/login`
- Constraints:
  Step 1 can authenticate a known non-target user, such as `webgoat/webgoat`, and returns a cookie format in the response output. Step 2 must send a forged `spoof_auth` cookie that decodes to `tom`. Direct Tom credential login returns cheating information, not success.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Cookie is absent and credentials are wrong.
  - Endpoint group:
    Step 1: `POST /SpoofCookie/login`
  - Failure endpoint:
    `POST /SpoofCookie/login`
  - Why this fails:
    The credentials flow returns wrong-login information.
  - Intentionally violated constraints:
    Wrong username/password and no cookie.
- Branch 2:
  - Unsatisfied condition:
    Cookie decodes to a known user other than `tom`.
  - Endpoint group:
    Step 1: `POST /SpoofCookie/login`
  - Failure endpoint:
    `POST /SpoofCookie/login`
  - Why this fails:
    Cookie login for non-target users returns failure with cookie details.
  - Intentionally violated constraints:
    Cookie username is not `tom`.
- Branch 3:
  - Unsatisfied condition:
    Cookie cannot be decoded.
  - Endpoint group:
    Step 1: `POST /SpoofCookie/login`
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

### Behavior 58: clear spoof cookie

Behavior name:
clear spoof cookie

Successful execution:
- Result:
  The API expires the `spoof_auth` cookie.
- Endpoint sequence:
  Step 1: `GET /SpoofCookie/cleanup`
- Constraints:
  No prerequisite is required; it always adds an expired cookie.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /SpoofCookie/cleanup`
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

### Behavior 59: evaluate password strength

Behavior name:
evaluate password strength

Successful execution:
- Result:
  The API returns password-strength output and marks the assignment solved when the score is 4.
- Endpoint sequence:
  Step 1: `POST /SecurePasswords/assignment`
- Constraints:
  `password` must score at least 4 according to zxcvbn.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Password score is 0 through 3.
  - Endpoint group:
    Step 1: `POST /SecurePasswords/assignment`
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

### Behavior 60: solve SSRF local image task

Behavior name:
solve SSRF local image task

Successful execution:
- Result:
  The API returns the Jerry image and marks the task solved.
- Endpoint sequence:
  Step 1: `POST /SSRF/task1`
- Constraints:
  `url=images/jerry.png`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `url=images/tom.png`.
  - Endpoint group:
    Step 1: `POST /SSRF/task1`
  - Failure endpoint:
    `POST /SSRF/task1`
  - Why this fails:
    The endpoint returns Tom image feedback as failure.
  - Intentionally violated constraints:
    Wrong image target.
- Branch 2:
  - Unsatisfied condition:
    URL is neither Tom nor Jerry.
  - Endpoint group:
    Step 1: `POST /SSRF/task1`
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

### Behavior 61: solve SSRF external URL task

Behavior name:
solve SSRF external URL task

Successful execution:
- Result:
  The API fetches `http://ifconfig.pro` or uses fallback success text if the external site is unavailable.
- Endpoint sequence:
  Step 1: `POST /SSRF/task2`
- Constraints:
  `url=http://ifconfig.pro`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    URL is not exactly `http://ifconfig.pro`.
  - Endpoint group:
    Step 1: `POST /SSRF/task2`
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

### Behavior 62: upload profile file with path traversal

Behavior name:
upload profile file with path traversal

Successful execution:
- Result:
  The API writes the uploaded file and marks the lesson solved if the path escapes into the `PathTraversal` parent directory.
- Endpoint sequence:
  Step 1: `POST /PathTraversal/profile-upload`
- Constraints:
  Multipart parameter `uploadedFile` must be non-empty. `fullName` must be non-empty and crafted so the resulting canonical parent differs from the user upload directory and ends with `PathTraversal`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Uploaded file is empty or `fullName` is empty.
  - Endpoint group:
    Step 1: `POST /PathTraversal/profile-upload`
  - Failure endpoint:
    `POST /PathTraversal/profile-upload`
  - Why this fails:
    Base upload validation rejects empty file or name.
  - Intentionally violated constraints:
    Missing multipart file or filename.
- Branch 2:
  - Unsatisfied condition:
    Upload stays inside the user directory.
  - Endpoint group:
    Step 1: `POST /PathTraversal/profile-upload`
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

### Behavior 63: retrieve profile picture

Behavior name:
retrieve profile picture

Successful execution:
- Result:
  The API returns the current user’s uploaded image if present, otherwise the default image.
- Endpoint sequence:
  Step 1: `GET /PathTraversal/profile-picture`
- Constraints:
  No prerequisite is required because the default image is returned when no uploaded picture exists.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /PathTraversal/profile-picture`
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

### Behavior 64: upload profile file with traversal removed

Behavior name:
upload profile file with traversal removed

Successful execution:
- Result:
  The API stores the uploaded file after removing `../` from the supplied name.
- Endpoint sequence:
  Step 1: `POST /PathTraversal/profile-upload-fix`
- Constraints:
  Multipart parameter `uploadedFileFix` must be non-empty. `fullNameFix` is sanitized by replacing `../` with an empty string before storage.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Uploaded file or sanitized name is empty.
  - Endpoint group:
    Step 1: `POST /PathTraversal/profile-upload-fix`
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

### Behavior 65: retrieve fixed profile picture

Behavior name:
retrieve fixed profile picture

Successful execution:
- Result:
  The API returns the fixed-upload profile image or default image.
- Endpoint sequence:
  Step 1: `GET /PathTraversal/profile-picture-fix`
- Constraints:
  No prerequisite is required because the default image is returned when no image exists.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /PathTraversal/profile-picture-fix`
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

### Behavior 66: upload profile file using original filename

Behavior name:
upload profile file using original filename

Successful execution:
- Result:
  The API stores the uploaded file using the multipart original filename.
- Endpoint sequence:
  Step 1: `POST /PathTraversal/profile-upload-remove-user-input`
- Constraints:
  Multipart parameter `uploadedFileRemoveUserInput` must be non-empty. The stored name comes from `file.getOriginalFilename()`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Uploaded file or original filename is empty.
  - Endpoint group:
    Step 1: `POST /PathTraversal/profile-upload-remove-user-input`
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

### Behavior 67: retrieve random or targeted cat picture

Behavior name:
retrieve random or targeted cat picture

Successful execution:
- Result:
  The API returns a random cat picture or the requested cat picture.
- Endpoint sequence:
  Step 1: `GET /PathTraversal/random-picture`
- Constraints:
  Query parameter `id` is optional. If present, the raw query string must not contain `..` or `/`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Query string contains `..` or `/`.
  - Endpoint group:
    Step 1: `GET /PathTraversal/random-picture`
  - Failure endpoint:
    `GET /PathTraversal/random-picture`
  - Why this fails:
    The endpoint returns HTTP 400 with illegal-character text.
  - Intentionally violated constraints:
    Path traversal characters in query.
- Branch 2:
  - Unsatisfied condition:
    Requested file does not exist.
  - Endpoint group:
    Step 1: `GET /PathTraversal/random-picture`
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

### Behavior 68: submit path traversal secret hash

Behavior name:
submit path traversal secret hash

Successful execution:
- Result:
  The API accepts the SHA-512 hash of the current username.
- Endpoint sequence:
  Step 1: `GET /PathTraversal/random-picture`
  Step 2: `POST /PathTraversal/random`
- Constraints:
  Step 1 can expose the secret-file hint through the retrieval lesson. Step 2 must submit `secret` equal to `Sha512DigestUtils.shaHex(currentUsername)`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `secret` does not match the current username hash.
  - Endpoint group:
    Step 1: `POST /PathTraversal/random`
  - Failure endpoint:
    `POST /PathTraversal/random`
  - Why this fails:
    The endpoint compares `secret` to the SHA-512 hex of the username.
  - Intentionally violated constraints:
    Wrong or missing hash.

Endpoint coverage:
- Covers:
  `POST /PathTraversal/random`
  `GET /PathTraversal/random-picture`
- Distinct meaning:
  Confirms the secret discovered through the random-picture traversal lesson.

### Behavior 69: exploit zip slip upload

Behavior name:
exploit zip slip upload

Successful execution:
- Result:
  The API extracts the uploaded ZIP and marks the lesson solved if the current profile image changes.
- Endpoint sequence:
  Step 1: `POST /PathTraversal/zip-slip`
- Constraints:
  Multipart parameter `uploadedFileZipSlip` must have an original filename ending with `.zip`. The ZIP entries must write a new profile image so the before/after image bytes differ.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Uploaded file name does not end with `.zip`.
  - Endpoint group:
    Step 1: `POST /PathTraversal/zip-slip`
  - Failure endpoint:
    `POST /PathTraversal/zip-slip`
  - Why this fails:
    The endpoint rejects non-ZIP uploads.
  - Intentionally violated constraints:
    Non-ZIP upload.
- Branch 2:
  - Unsatisfied condition:
    Extraction does not change the profile image.
  - Endpoint group:
    Step 1: `POST /PathTraversal/zip-slip`
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
  Uploads and extracts a ZIP for Zip Slip behavior.

### Behavior 70: retrieve zip-slip profile picture

Behavior name:
retrieve zip-slip profile picture

Successful execution:
- Result:
  The API returns the current profile picture for the zip-slip lesson.
- Endpoint sequence:
  Step 1: `GET /PathTraversal/zip-slip/`
- Constraints:
  No prerequisite is required because a default image is returned when no uploaded picture exists.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /PathTraversal/zip-slip/`
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

### Behavior 71: perform simple password reset by email

Behavior name:
perform simple password reset by email

Successful execution:
- Result:
  The API sends a reset email and accepts the new password.
- Endpoint sequence:
  Step 1: `POST /PasswordReset/simple-mail/reset`
  Step 2: `POST /PasswordReset/simple-mail`
- Constraints:
  Step 1 `emailReset` local part must equal the current username; it sends a password equal to the reversed username. Step 2 must use `email` for the current user and `password=reverse(username)`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Reset email local part does not match current username.
  - Endpoint group:
    Step 1: `POST /PasswordReset/simple-mail/reset`
  - Failure endpoint:
    `POST /PasswordReset/simple-mail/reset`
  - Why this fails:
    The endpoint returns mismatch information instead of sending the intended mail.
  - Intentionally violated constraints:
    Wrong reset email.
- Branch 2:
  - Unsatisfied condition:
    Login password is not the reversed username.
  - Endpoint group:
    Step 1: `POST /PasswordReset/simple-mail/reset`
    Step 2: `POST /PasswordReset/simple-mail`
  - Failure endpoint:
    `POST /PasswordReset/simple-mail`
  - Why this fails:
    The login endpoint compares password to `reverse(username)`.
  - Intentionally violated constraints:
    Wrong reset password.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/simple-mail/reset`
  `POST /PasswordReset/simple-mail`
- Distinct meaning:
  Sends and uses a simple password reset email.

### Behavior 72: create Tom password reset link and log in

Behavior name:
create Tom password reset link and log in

Successful execution:
- Result:
  The API creates a reset link for Tom and later accepts Tom’s changed password.
- Endpoint sequence:
  Step 1: `POST /PasswordReset/ForgotPassword/create-password-reset-link`
  Step 2: `POST /PasswordReset/reset/login`
- Constraints:
  Step 1 must use `email=tom@webgoat-cloud.org` and a `Host` header containing the configured WebWolf host and port to store the generated link under the current username. Source code shows the actual password change is performed by `POST /PasswordReset/reset/change-password`, but that endpoint is not present in `webgoat.json`; therefore the documented Swagger sequence alone cannot set `usersToTomPassword`. Step 2 must submit Tom’s email and the password set through that missing documented step.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Step 1 uses a normal host or non-Tom email.
  - Endpoint group:
    Step 1: `POST /PasswordReset/ForgotPassword/create-password-reset-link`
    Step 2: `POST /PasswordReset/reset/login`
  - Failure endpoint:
    `POST /PasswordReset/reset/login`
  - Why this fails:
    The generated Tom reset link is not associated with the current user, so Tom’s password remains the default impossible value.
  - Intentionally violated constraints:
    Wrong email or missing WebWolf Host header.
- Branch 2:
  - Unsatisfied condition:
    Step 2 uses an email other than Tom’s or a password that was not set.
  - Endpoint group:
    Step 1: `POST /PasswordReset/reset/login`
  - Failure endpoint:
    `POST /PasswordReset/reset/login`
  - Why this fails:
    The login endpoint only accepts `tom@webgoat-cloud.org` and the stored per-user Tom password.
  - Intentionally violated constraints:
    Wrong email or password.

Endpoint coverage:
- Covers:
  `POST /PasswordReset/ForgotPassword/create-password-reset-link`
  `POST /PasswordReset/reset/login`
- Distinct meaning:
  Creates reset-link state and attempts Tom password login. There is an OpenAPI/source discrepancy because a required password-change endpoint exists in source but is absent from Swagger.

### Behavior 73: answer password reset security question

Behavior name:
answer password reset security question

Successful execution:
- Result:
  The API accepts the known color answer for a non-WebGoat user.
- Endpoint sequence:
  Step 1: `POST /PasswordReset/questions`
- Constraints:
  Form data must include `username` and `securityQuestion`. Valid pairs include `admin/green`, `jerry/orange`, `tom/purple`, and `larry/yellow`. `webgoat` is explicitly rejected.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `username=webgoat`.
  - Endpoint group:
    Step 1: `POST /PasswordReset/questions`
  - Failure endpoint:
    `POST /PasswordReset/questions`
  - Why this fails:
    The endpoint has a specific wrong-user branch.
  - Intentionally violated constraints:
    Forbidden username.
- Branch 2:
  - Unsatisfied condition:
    Unknown username or wrong color.
  - Endpoint group:
    Step 1: `POST /PasswordReset/questions`
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

### Behavior 74: classify weak security questions

Behavior name:
classify weak security questions

Successful execution:
- Result:
  The API eventually marks the assignment solved after enough known questions have been tried.
- Endpoint sequence:
  Step 1: `POST /PasswordReset/SecurityQuestions`
- Constraints:
  `question` must be one of the known weak security-question strings. Completion depends on `TriedQuestions.isComplete()`, which tracks repeated/required tried questions in lesson state.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Question is unknown.
  - Endpoint group:
    Step 1: `POST /PasswordReset/SecurityQuestions`
  - Failure endpoint:
    `POST /PasswordReset/SecurityQuestions`
  - Why this fails:
    The endpoint returns informational output saying the question is unknown.
  - Intentionally violated constraints:
    Question not in the static map.
- Branch 2:
  - Unsatisfied condition:
    Known question is submitted before the tried-question state is complete.
  - Endpoint group:
    Step 1: `POST /PasswordReset/SecurityQuestions`
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

### Behavior 75: solve log spoofing

Behavior name:
solve log spoofing

Successful execution:
- Result:
  The API accepts a username containing a newline before `admin`.
- Endpoint sequence:
  Step 1: `POST /LogSpoofing/log-spoofing`
- Constraints:
  `username` must be non-empty, must not contain `<p>` or `<div>`, and after newline replacement must have `<br/>` before `admin`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username is empty or contains blocked tags.
  - Endpoint group:
    Step 1: `POST /LogSpoofing/log-spoofing`
  - Failure endpoint:
    `POST /LogSpoofing/log-spoofing`
  - Why this fails:
    Empty usernames and `<p>`/`<div>` payloads are rejected.
  - Intentionally violated constraints:
    Empty or blocked HTML payload.
- Branch 2:
  - Unsatisfied condition:
    Newline marker is absent or after `admin`.
  - Endpoint group:
    Step 1: `POST /LogSpoofing/log-spoofing`
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

### Behavior 76: solve log bleeding

Behavior name:
solve log bleeding

Successful execution:
- Result:
  The API accepts the runtime admin password.
- Endpoint sequence:
  Step 1: `POST /LogSpoofing/log-bleeding`
- Constraints:
  `username=Admin`. `password` must equal the random UUID generated in the controller constructor and logged Base64-encoded at startup; no documented endpoint produces this value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username or password is empty.
  - Endpoint group:
    Step 1: `POST /LogSpoofing/log-bleeding`
  - Failure endpoint:
    `POST /LogSpoofing/log-bleeding`
  - Why this fails:
    The endpoint requires both fields.
  - Intentionally violated constraints:
    Empty input.
- Branch 2:
  - Unsatisfied condition:
    Username is not `Admin` or password is not the generated UUID.
  - Endpoint group:
    Step 1: `POST /LogSpoofing/log-bleeding`
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

### Behavior 77: log in and list JWT votes as user

Behavior name:
log in and list JWT votes as user

Successful execution:
- Result:
  The API sets an `access_token` cookie and then returns vote data with user-visible fields.
- Endpoint sequence:
  Step 1: `GET /JWT/votings/login`
  Step 2: `GET /JWT/votings`
- Constraints:
  Step 1 `user` must be contained in the string `TomJerrySylvester`, such as `Tom`. Step 2 must send the `access_token` cookie from Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Login user is not in the valid-user string.
  - Endpoint group:
    Step 1: `GET /JWT/votings/login`
    Step 2: `GET /JWT/votings`
  - Failure endpoint:
    `GET /JWT/votings/login`
  - Why this fails:
    Login returns unauthorized and sets an empty cookie.
  - Intentionally violated constraints:
    Invalid user.
- Branch 2:
  - Unsatisfied condition:
    Vote list is requested without a valid cookie.
  - Endpoint group:
    Step 1: `GET /JWT/votings`
  - Failure endpoint:
    `GET /JWT/votings`
  - Why this fails:
    It does not fail HTTP-wise, but returns the guest serialization view.
  - Intentionally violated constraints:
    Missing valid `access_token`.

Endpoint coverage:
- Covers:
  `GET /JWT/votings/login`
  `GET /JWT/votings`
- Distinct meaning:
  Authenticates and lists votes with authenticated fields.

### Behavior 78: list JWT votes as guest

Behavior name:
list JWT votes as guest

Successful execution:
- Result:
  The API returns public vote fields only.
- Endpoint sequence:
  Step 1: `GET /JWT/votings`
- Constraints:
  No `access_token` cookie is required. Empty, invalid, `Guest`, or unknown-user tokens also use the guest view.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /JWT/votings`
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

### Behavior 79: cast JWT vote

Behavior name:
cast JWT vote

Successful execution:
- Result:
  The API increments the vote count for the selected title and returns accepted.
- Endpoint sequence:
  Step 1: `GET /JWT/votings/login`
  Step 2: `POST /JWT/votings/{title}`
- Constraints:
  Step 1 must set a valid `access_token`. Step 2 must send that cookie. `{title}` should match a key in the vote map, such as `Admin lost password`, `Vote for your favourite`, `Get it for free`, or `Photo comments`; unknown titles still return accepted but do not increment anything.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Cookie is missing, invalid, or names an invalid user.
  - Endpoint group:
    Step 1: `POST /JWT/votings/{title}`
  - Failure endpoint:
    `POST /JWT/votings/{title}`
  - Why this fails:
    The endpoint returns HTTP 401.
  - Intentionally violated constraints:
    Missing or invalid `access_token`.

Endpoint coverage:
- Covers:
  `POST /JWT/votings/{title}`
  `GET /JWT/votings/login`
- Distinct meaning:
  Authenticated vote mutation.

### Behavior 80: reset JWT votes as admin

Behavior name:
reset JWT votes as admin

Successful execution:
- Result:
  The API resets all vote counts and marks the lesson solved.
- Endpoint sequence:
  Step 1: `POST /JWT/votings`
- Constraints:
  The `access_token` cookie must be a valid token signed with the JWT vote secret and contain claim `admin=true`. No documented endpoint creates an admin token; the documented login endpoint creates `admin=false`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Cookie is missing or invalid.
  - Endpoint group:
    Step 1: `POST /JWT/votings`
  - Failure endpoint:
    `POST /JWT/votings`
  - Why this fails:
    The endpoint returns invalid-token feedback.
  - Intentionally violated constraints:
    Missing/invalid `access_token`.
- Branch 2:
  - Unsatisfied condition:
    Token is valid but `admin` is false.
  - Endpoint group:
    Step 1: `GET /JWT/votings/login`
    Step 2: `POST /JWT/votings`
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

### Behavior 81: forge JWT secret-key token

Behavior name:
forge JWT secret-key token

Successful execution:
- Result:
  The API accepts a valid token whose username claim is `WebGoat`.
- Endpoint sequence:
  Step 1: `GET /JWT/secret/gettoken`
  Step 2: `POST /JWT/secret`
- Constraints:
  Step 1 returns a signed sample token and reveals the expected claim shape. Step 2 must submit `token` signed with the same secret and containing all expected claims, with `username=WebGoat`. Any documented method on `/JWT/secret/gettoken` returns the sample token.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Token is invalid or signed with the wrong key.
  - Endpoint group:
    Step 1: `GET /JWT/secret/gettoken`
    Step 2: `POST /JWT/secret`
  - Failure endpoint:
    `POST /JWT/secret`
  - Why this fails:
    JWT parsing throws and returns invalid-token feedback.
  - Intentionally violated constraints:
    Bad signature or malformed token.
- Branch 2:
  - Unsatisfied condition:
    Token is valid but missing expected claims or has another username.
  - Endpoint group:
    Step 1: `GET /JWT/secret/gettoken`
    Step 2: `POST /JWT/secret`
  - Failure endpoint:
    `POST /JWT/secret`
  - Why this fails:
    Claim-set check or username check fails.
  - Intentionally violated constraints:
    Missing claims or `username` not `WebGoat`.

Endpoint coverage:
- Covers:
  `GET /JWT/secret/gettoken`
  `PUT /JWT/secret/gettoken`
  `POST /JWT/secret/gettoken`
  `DELETE /JWT/secret/gettoken`
  `OPTIONS /JWT/secret/gettoken`
  `HEAD /JWT/secret/gettoken`
  `PATCH /JWT/secret/gettoken`
  `POST /JWT/secret`
- Distinct meaning:
  Retrieves a sample token and validates a forged secret-key token.

### Behavior 82: log in to JWT refresh flow

Behavior name:
log in to JWT refresh flow

Successful execution:
- Result:
  The API returns an access token and refresh token.
- Endpoint sequence:
  Step 1: `POST /JWT/refresh/login`
- Constraints:
  JSON body must contain `user=Jerry` and `password=bm5nhSkxCXZkKRy4`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Body is missing or credentials do not match Jerry’s expected password.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/login`
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

### Behavior 83: refresh JWT access token

Behavior name:
refresh JWT access token

Successful execution:
- Result:
  The API consumes a valid refresh token and returns a new access/refresh token pair.
- Endpoint sequence:
  Step 1: `POST /JWT/refresh/login`
  Step 2: `POST /JWT/refresh/newToken`
- Constraints:
  Step 2 must send `Authorization: Bearer {access_token}` using Step 1’s access token and a JSON body with `refresh_token` equal to Step 1’s refresh token. The refresh token is single-use and is removed after success.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Authorization header or JSON body is missing.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/newToken`
  - Failure endpoint:
    `POST /JWT/refresh/newToken`
  - Why this fails:
    The endpoint returns HTTP 401 before parsing.
  - Intentionally violated constraints:
    Missing header/body.
- Branch 2:
  - Unsatisfied condition:
    Refresh token is missing, wrong, or already used.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/login`
    Step 2: `POST /JWT/refresh/newToken`
    Step 3: `POST /JWT/refresh/newToken`
  - Failure endpoint:
    `POST /JWT/refresh/newToken`
  - Why this fails:
    The valid refresh token is removed after the first refresh.
  - Intentionally violated constraints:
    Reused or wrong refresh token.

Endpoint coverage:
- Covers:
  `POST /JWT/refresh/newToken`
  `POST /JWT/refresh/login`
- Distinct meaning:
  Refreshes a token pair using the login response values.

### Behavior 84: check out with Tom JWT

Behavior name:
check out with Tom JWT

Successful execution:
- Result:
  The API marks checkout solved when the access token claims user `Tom`.
- Endpoint sequence:
  Step 1: `POST /JWT/refresh/checkout`
- Constraints:
  `Authorization` must contain a valid Bearer token signed with the refresh JWT password and body claim `user=Tom`. No documented endpoint creates a Tom token; `/JWT/refresh/login` creates Jerry tokens.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Authorization header is missing.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/checkout`
  - Failure endpoint:
    `POST /JWT/refresh/checkout`
  - Why this fails:
    The endpoint returns HTTP 401.
  - Intentionally violated constraints:
    Missing Bearer token.
- Branch 2:
  - Unsatisfied condition:
    Token is valid but user is not Tom.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/login`
    Step 2: `POST /JWT/refresh/checkout`
  - Failure endpoint:
    `POST /JWT/refresh/checkout`
  - Why this fails:
    The documented login token contains `user=Jerry`, so checkout returns not-Tom feedback.
  - Intentionally violated constraints:
    Wrong token user.
- Branch 3:
  - Unsatisfied condition:
    Token is malformed or signed incorrectly.
  - Endpoint group:
    Step 1: `POST /JWT/refresh/checkout`
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

### Behavior 85: submit JWT quiz and read results

Behavior name:
submit JWT quiz and read results

Successful execution:
- Result:
  The API records both JWT quiz answers as correct and returns the correctness array.
- Endpoint sequence:
  Step 1: `POST /JWT/quiz`
  Step 2: `GET /JWT/quiz`
- Constraints:
  Answers must contain `Solution 1` and `Solution 2` for questions 0 and 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    One or both answers are wrong.
  - Endpoint group:
    Step 1: `POST /JWT/quiz`
    Step 2: `GET /JWT/quiz`
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

### Behavior 86: follow user in JWT KID lesson

Behavior name:
follow user in JWT KID lesson

Successful execution:
- Result:
  The API returns a follow-status message.
- Endpoint sequence:
  Step 1: `POST /JWT/kid/follow/{user}`
- Constraints:
  If `{user}=Jerry`, response says following yourself is redundant; otherwise it says the user is now following Tom.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `POST /JWT/kid/follow/{user}`
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

### Behavior 87: solve JWT KID delete action

Behavior name:
solve JWT KID delete action

Successful execution:
- Result:
  The API accepts a token that resolves a signing key by `kid` and has `username=Tom`.
- Endpoint sequence:
  Step 1: `POST /JWT/kid/delete`
- Constraints:
  `token` must be non-empty, parse as a signed JWT, use a `kid` header that resolves through the vulnerable SQL lookup, and contain claim `username=Tom`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Token is empty or invalid.
  - Endpoint group:
    Step 1: `POST /JWT/kid/delete`
  - Failure endpoint:
    `POST /JWT/kid/delete`
  - Why this fails:
    Empty token returns invalid-token feedback; JWT parse failures are caught.
  - Intentionally violated constraints:
    Missing or invalid token.
- Branch 2:
  - Unsatisfied condition:
    Token username is `Jerry` or neither Tom nor Jerry.
  - Endpoint group:
    Step 1: `POST /JWT/kid/delete`
  - Failure endpoint:
    `POST /JWT/kid/delete`
  - Why this fails:
    The endpoint rejects Jerry and non-Tom usernames.
  - Intentionally violated constraints:
    Wrong username claim.
- Branch 3:
  - Unsatisfied condition:
    `kid` SQL lookup fails.
  - Endpoint group:
    Step 1: `POST /JWT/kid/delete`
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

### Behavior 88: follow user in JWT JKU lesson

Behavior name:
follow user in JWT JKU lesson

Successful execution:
- Result:
  The API returns a follow-status message.
- Endpoint sequence:
  Step 1: `POST /JWT/jku/follow/{user}`
- Constraints:
  If `{user}=Jerry`, response says following yourself is redundant; otherwise it says the user is now following Tom.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `POST /JWT/jku/follow/{user}`
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

### Behavior 89: solve JWT JKU delete action

Behavior name:
solve JWT JKU delete action

Successful execution:
- Result:
  The API accepts a token whose JKU points to a usable JWK and whose username is Tom.
- Endpoint sequence:
  Step 1: `POST /JWT/jku/delete`
- Constraints:
  `token` must be non-empty, have a `jku` header URL, a key id resolvable from that JWK provider, a valid RSA signature, and claim `username=Tom`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Token is empty, malformed, has an invalid JKU, or fails verification.
  - Endpoint group:
    Step 1: `POST /JWT/jku/delete`
  - Failure endpoint:
    `POST /JWT/jku/delete`
  - Why this fails:
    The endpoint catches URL, JWK, and JWT verification exceptions.
  - Intentionally violated constraints:
    Bad token/JKU/signature.
- Branch 2:
  - Unsatisfied condition:
    Verified username is Jerry or not Tom.
  - Endpoint group:
    Step 1: `POST /JWT/jku/delete`
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

### Behavior 90: decode JWT user claim answer

Behavior name:
decode JWT user claim answer

Successful execution:
- Result:
  The API accepts the decoded JWT user value.
- Endpoint sequence:
  Step 1: `POST /JWT/decode`
- Constraints:
  Query parameter `jwt-encode-user` must equal `user`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `jwt-encode-user` is not `user`.
  - Endpoint group:
    Step 1: `POST /JWT/decode`
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

### Behavior 91: submit insecure login credentials

Behavior name:
submit insecure login credentials

Successful execution:
- Result:
  The API accepts the exposed credentials.
- Endpoint sequence:
  Step 1: `POST /InsecureLogin/task`
- Constraints:
  `username=CaptainJack` and `password=BlackPearl`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username or password is wrong.
  - Endpoint group:
    Step 1: `POST /InsecureLogin/task`
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

### Behavior 92: deserialize task token

Behavior name:
deserialize task token

Successful execution:
- Result:
  The API deserializes a token to `VulnerableTaskHolder` and accepts a 3-7 second execution delay.
- Endpoint sequence:
  Step 1: `POST /InsecureDeserialization/task`
- Constraints:
  `token` is Base64-url-normalized and Java-deserialized. The object must be a `VulnerableTaskHolder`; deserialization read time must be at least 3000 ms and at most 7000 ms.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Token is not valid Base64/serialized data or has an invalid class version.
  - Endpoint group:
    Step 1: `POST /InsecureDeserialization/task`
  - Failure endpoint:
    `POST /InsecureDeserialization/task`
  - Why this fails:
    Decode/deserialization exceptions return failure feedback.
  - Intentionally violated constraints:
    Invalid serialized token.
- Branch 2:
  - Unsatisfied condition:
    Deserialized object is not `VulnerableTaskHolder`.
  - Endpoint group:
    Step 1: `POST /InsecureDeserialization/task`
  - Failure endpoint:
    `POST /InsecureDeserialization/task`
  - Why this fails:
    Strings and other objects are rejected.
  - Intentionally violated constraints:
    Wrong serialized object type.
- Branch 3:
  - Unsatisfied condition:
    Delay is below 3000 ms or above 7000 ms.
  - Endpoint group:
    Step 1: `POST /InsecureDeserialization/task`
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
  Evaluates insecure deserialization behavior.

### Behavior 93: reverse submitted name

Behavior name:
reverse submitted name

Successful execution:
- Result:
  The API returns the submitted `person` value reversed.
- Endpoint sequence:
  Step 1: `POST /HttpBasics/attack1`
- Constraints:
  `person` must be non-blank.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `person` is blank.
  - Endpoint group:
    Step 1: `POST /HttpBasics/attack1`
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

### Behavior 94: solve HTTP basics quiz

Behavior name:
solve HTTP basics quiz

Successful execution:
- Result:
  The API accepts the HTTP method and matching magic values.
- Endpoint sequence:
  Step 1: `POST /HttpBasics/attack2`
- Constraints:
  `answer` must equal `POST` case-insensitively, and `magic_answer` must equal `magic_num`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `answer` is not `POST`.
  - Endpoint group:
    Step 1: `POST /HttpBasics/attack2`
  - Failure endpoint:
    `POST /HttpBasics/attack2`
  - Why this fails:
    The first check returns incorrect feedback.
  - Intentionally violated constraints:
    Wrong method answer.
- Branch 2:
  - Unsatisfied condition:
    `magic_answer` and `magic_num` differ.
  - Endpoint group:
    Step 1: `POST /HttpBasics/attack2`
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

### Behavior 95: submit tampered HTML purchase

Behavior name:
submit tampered HTML purchase

Successful execution:
- Result:
  The API accepts a manipulated total price.
- Endpoint sequence:
  Step 1: `POST /HtmlTampering/task`
- Constraints:
  `Float(QTY) * 2999.99` must be greater than `Float(Total) + 1`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Total is not low enough for the quantity.
  - Endpoint group:
    Step 1: `POST /HtmlTampering/task`
  - Failure endpoint:
    `POST /HtmlTampering/task`
  - Why this fails:
    The arithmetic comparison does not indicate tampering.
  - Intentionally violated constraints:
    `Total` is consistent with `QTY`.
- Branch 2:
  - Unsatisfied condition:
    `QTY` or `Total` is not parseable as a float.
  - Endpoint group:
    Step 1: `POST /HtmlTampering/task`
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

### Behavior 96: hijack session by cookie

Behavior name:
hijack session by cookie

Successful execution:
- Result:
  The API authenticates when the submitted `hijack_cookie` is in the provider’s active session queue.
- Endpoint sequence:
  Step 1: `POST /HijackSession/login`
  Step 2: `POST /HijackSession/login`
- Constraints:
  Step 1 without a cookie issues a new predictable cookie and may also auto-create authenticated sessions internally. Step 2 must send a `hijack_cookie` value that exists in the provider session queue.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Cookie is absent and generated authentication is not authenticated.
  - Endpoint group:
    Step 1: `POST /HijackSession/login`
  - Failure endpoint:
    `POST /HijackSession/login`
  - Why this fails:
    Credentials are not actually checked for success; without a valid queued id, authentication remains false.
  - Intentionally violated constraints:
    No valid hijack cookie.
- Branch 2:
  - Unsatisfied condition:
    Cookie value is not in the active session queue.
  - Endpoint group:
    Step 1: `POST /HijackSession/login`
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

### Behavior 97: list and create stored XSS comments

Behavior name:
list and create stored XSS comments

Successful execution:
- Result:
  The API stores a comment for the current user and marks the stored-XSS comment step solved when it contains the phone-home script.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScriptingStored/stored-xss`
  Step 2: `GET /CrossSiteScriptingStored/stored-xss`
- Constraints:
  Step 1 body must deserialize to `Comment`. `text` must contain `<script>webgoat.customjs.phoneHome()</script>` for assignment success. Step 2 lists default comments plus the user’s new comments.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Comment text does not contain the exact phone-home script.
  - Endpoint group:
    Step 1: `POST /CrossSiteScriptingStored/stored-xss`
    Step 2: `GET /CrossSiteScriptingStored/stored-xss`
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss`
  - Why this fails:
    The comment is still stored, but the success check fails.
  - Intentionally violated constraints:
    Missing exact script.
- Branch 2:
  - Unsatisfied condition:
    JSON body cannot be parsed.
  - Endpoint group:
    Step 1: `POST /CrossSiteScriptingStored/stored-xss`
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss`
  - Why this fails:
    Parsing falls back to an empty comment, which does not contain the script.
  - Intentionally violated constraints:
    Invalid comment JSON.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScriptingStored/stored-xss`
  `GET /CrossSiteScriptingStored/stored-xss`
- Distinct meaning:
  Creates and lists stored XSS comments.

### Behavior 98: verify stored XSS callback

Behavior name:
verify stored XSS callback

Successful execution:
- Result:
  The API accepts the callback message generated by the phone-home endpoint.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScriptingStored/stored-xss`
  Step 2: `POST /CrossSiteScripting/phone-home-xss`
  Step 3: `POST /CrossSiteScriptingStored/stored-xss-follow-up`
- Constraints:
  Step 1 stores a script that calls phone home. Step 2 must use `param1=42`, `param2=24`, and header `webgoat-requested-by=dom-xss-vuln`, causing `randValue` to be stored. Step 3 must send `successMessage` equal to that `randValue`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Phone-home setup is omitted.
  - Endpoint group:
    Step 1: `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Why this fails:
    No matching `randValue` exists in session.
  - Intentionally violated constraints:
    Omitted `POST /CrossSiteScripting/phone-home-xss`.
- Branch 2:
  - Unsatisfied condition:
    `successMessage` does not match the stored value.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/phone-home-xss`
    Step 2: `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Failure endpoint:
    `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  - Why this fails:
    The endpoint compares directly to session `randValue`.
  - Intentionally violated constraints:
    Wrong callback value.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScriptingStored/stored-xss-follow-up`
  `POST /CrossSiteScripting/phone-home-xss`
  `POST /CrossSiteScriptingStored/stored-xss`
- Distinct meaning:
  Confirms the stored XSS callback workflow.

### Behavior 99: submit XSS quiz and read results

Behavior name:
submit XSS quiz and read results

Successful execution:
- Result:
  The API records all XSS quiz answers as correct and returns the correctness array.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/quiz`
  Step 2: `GET /CrossSiteScripting/quiz`
- Constraints:
  Answers must contain `Solution 4`, `Solution 3`, `Solution 1`, `Solution 2`, and `Solution 4` for questions 0 through 4.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Any answer is wrong.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/quiz`
    Step 2: `GET /CrossSiteScripting/quiz`
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

### Behavior 100: trigger DOM XSS phone home

Behavior name:
trigger DOM XSS phone home

Successful execution:
- Result:
  The API stores a random value in session and returns it in output.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/phone-home-xss`
- Constraints:
  `param1=42`, `param2=24`, and request header `webgoat-requested-by=dom-xss-vuln`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Parameters or header do not match.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/phone-home-xss`
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

### Behavior 101: verify DOM XSS follow-up

Behavior name:
verify DOM XSS follow-up

Successful execution:
- Result:
  The API accepts the session random value from the phone-home call.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/phone-home-xss`
  Step 2: `POST /CrossSiteScripting/dom-follow-up`
- Constraints:
  Step 1 produces `randValue`. Step 2 must submit `successMessage` equal to `randValue`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Phone-home endpoint is omitted or `successMessage` is wrong.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/dom-follow-up`
  - Failure endpoint:
    `POST /CrossSiteScripting/dom-follow-up`
  - Why this fails:
    The endpoint compares to missing or different session `randValue`.
  - Intentionally violated constraints:
    Missing/wrong callback value.

Endpoint coverage:
- Covers:
  `POST /CrossSiteScripting/dom-follow-up`
  `POST /CrossSiteScripting/phone-home-xss`
- Distinct meaning:
  Confirms the DOM XSS callback value.

### Behavior 102: submit XSS mitigation code answers

Behavior name:
submit XSS mitigation code answers

Successful execution:
- Result:
  The API accepts mitigation code snippets for reflected/stored XSS lessons.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/attack3`
  Step 2: `POST /CrossSiteScripting/attack4`
- Constraints:
  Step 1 `editor` must include the OWASP Java Encoder taglib and use `${e:forHtml(param.first_name)}` and `${e:forHtml(param.last_name)}` in the parsed table. Step 2 `editor2` must include AntiSamy scan logic, `CleanResults`, DAO insertion, and `.getCleanHTML()`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required encoder elements are missing or malformed.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/attack3`
  - Failure endpoint:
    `POST /CrossSiteScripting/attack3`
  - Why this fails:
    Jsoup parsing or required-string checks fail.
  - Intentionally violated constraints:
    Missing encoder taglib or escaped output calls.
- Branch 2:
  - Unsatisfied condition:
    Required AntiSamy constructs are missing.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/attack4`
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

### Behavior 103: submit reflected XSS payload

Behavior name:
submit reflected XSS payload

Successful execution:
- Result:
  The API returns a cart response and marks the reflected XSS assignment solved.
- Endpoint sequence:
  Step 1: `GET /CrossSiteScripting/attack5a`
- Constraints:
  `field1` must match the script pattern `<script>(console.log|alert)(...)</script>`. `field2` must not match that script pattern. Quantities are used to calculate the displayed total.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Script payload is placed in `field2`.
  - Endpoint group:
    Step 1: `GET /CrossSiteScripting/attack5a`
  - Failure endpoint:
    `GET /CrossSiteScripting/attack5a`
  - Why this fails:
    The endpoint has a specific wrong-field failure branch.
  - Intentionally violated constraints:
    XSS payload in `field2`.
- Branch 2:
  - Unsatisfied condition:
    `field1` lacks the expected script pattern.
  - Endpoint group:
    Step 1: `GET /CrossSiteScripting/attack5a`
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

### Behavior 104: submit DOM XSS route answer

Behavior name:
submit DOM XSS route answer

Successful execution:
- Result:
  The API accepts the vulnerable DOM route.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/attack6a`
- Constraints:
  `DOMTestRoute` must match `start.mvc#test` with an optional trailing slash.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Route does not match the required pattern.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/attack6a`
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

### Behavior 105: complete XSS lesson 1 checkbox

Behavior name:
complete XSS lesson 1 checkbox

Successful execution:
- Result:
  The API marks the first XSS lesson step solved.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/attack1`
- Constraints:
  Optional parameter `checkboxAttack1` must be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `checkboxAttack1` is omitted.
  - Endpoint group:
    Step 1: `POST /CrossSiteScripting/attack1`
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

### Behavior 106: solve Chrome DevTools network number task

Behavior name:
solve Chrome DevTools network number task

Successful execution:
- Result:
  The API accepts matching network-number parameters.
- Endpoint sequence:
  Step 1: `POST /ChromeDevTools/network`
- Constraints:
  Implementation has two mappings on the same path. The solving mapping requires `network_num` and `number`, and they must be equal. Swagger merges this path with the auxiliary `networkNum` mapping, so it incorrectly documents all three query parameters together.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `network_num` and `number` differ.
  - Endpoint group:
    Step 1: `POST /ChromeDevTools/network`
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

### Behavior 107: verify Chrome DevTools dummy callback

Behavior name:
verify Chrome DevTools dummy callback

Successful execution:
- Result:
  The API accepts a message equal to the session random value.
- Endpoint sequence:
  Step 1: `POST /CrossSiteScripting/phone-home-xss`
  Step 2: `POST /ChromeDevTools/dummy`
- Constraints:
  Step 1 is one documented endpoint that stores `randValue` in the lesson session. Step 2 must submit `successMessage` equal to that value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The random value was not generated or `successMessage` is wrong.
  - Endpoint group:
    Step 1: `POST /ChromeDevTools/dummy`
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

### Behavior 108: bypass frontend validation

Behavior name:
bypass frontend validation

Successful execution:
- Result:
  The API accepts values that violate every frontend validation regex while `error=0`.
- Endpoint sequence:
  Step 1: `POST /BypassRestrictions/frontendValidation`
- Constraints:
  `error` must be `0`. Each field must not match its corresponding frontend regex.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `error>0` or any field still matches its frontend-valid regex.
  - Endpoint group:
    Step 1: `POST /BypassRestrictions/frontendValidation`
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

### Behavior 109: bypass field restrictions

Behavior name:
bypass field restrictions

Successful execution:
- Result:
  The API accepts values outside the restricted form controls.
- Endpoint sequence:
  Step 1: `POST /BypassRestrictions/FieldRestrictions`
- Constraints:
  `select` and `radio` must be neither `option1` nor `option2`; `checkbox` must be neither `on` nor `off`; `shortInput` length must be greater than 5; `readOnlyInput` must not equal `change`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Any field remains within its restricted value set.
  - Endpoint group:
    Step 1: `POST /BypassRestrictions/FieldRestrictions`
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

### Behavior 110: retrieve report card

Behavior name:
retrieve report card

Successful execution:
- Result:
  The API returns current-user lesson and assignment progress statistics.
- Endpoint sequence:
  Step 1: `GET /service/reportcard.mvc`
- Constraints:
  Uses current username to load `UserProgress`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is visible in the implementation.
  - Endpoint group:
    Step 1: `GET /service/reportcard.mvc`
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

### Behavior 111: retrieve lesson overview

Behavior name:
retrieve lesson overview

Successful execution:
- Result:
  The API returns assignment solved status for a lesson.
- Endpoint sequence:
  Step 1: `GET /service/lessonoverview.mvc/{lesson}`
- Constraints:
  `{lesson}` must resolve to a lesson name in the course.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{lesson}` does not resolve to a course lesson.
  - Endpoint group:
    Step 1: `GET /service/lessonoverview.mvc/{lesson}`
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

### Behavior 112: retrieve lesson info

Behavior name:
retrieve lesson info

Successful execution:
- Result:
  The API returns lesson title and source/solution/plan availability flags.
- Endpoint sequence:
  Step 1: `GET /service/lessoninfo.mvc/{lesson}`
- Constraints:
  `{lesson}` must resolve to a lesson name in the course.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{lesson}` is invalid.
  - Endpoint group:
    Step 1: `GET /service/lessoninfo.mvc/{lesson}`
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

### Behavior 113: retrieve labels and hints

Behavior name:
retrieve labels and hints

Successful execution:
- Result:
  The API returns all labels or all hints.
- Endpoint sequence:
  Step 1: `GET /service/labels.mvc`
  Step 2: `GET /service/hint.mvc`
- Constraints:
  The endpoints are independent; the sequence is an endpoint set, not required ordering.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /service/labels.mvc`
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

### Behavior 114: retrieve server directory

Behavior name:
retrieve server directory

Successful execution:
- Result:
  The API returns the configured WebGoat server directory.
- Endpoint sequence:
  Step 1: `GET /server-directory`
- Constraints:
  Returns property `webgoat.server.directory`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Property is absent.
  - Endpoint group:
    Step 1: `GET /server-directory`
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

### Behavior 115: retrieve scoreboard data

Behavior name:
retrieve scoreboard data

Successful execution:
- Result:
  The API returns rankings of non-CSRF users by solved challenges.
- Endpoint sequence:
  Step 1: `GET /scoreboard-data`
- Constraints:
  No setup is required; the endpoint lists all users and filters names starting with `csrf-`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /scoreboard-data`
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

### Behavior 116: retrieve lesson menu

Behavior name:
retrieve lesson menu

Successful execution:
- Result:
  The API returns the left-navigation lesson menu with completion status.
- Endpoint sequence:
  Step 1: `GET /service/lessonmenu.mvc`
- Constraints:
  Source uses `@RequestMapping` without a method restriction, so all documented methods return the same menu.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /service/lessonmenu.mvc`
  - Failure endpoint:
    `GET /service/lessonmenu.mvc`
  - Why this fails:
    Normal execution returns menu items.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/lessonmenu.mvc`
  `PUT /service/lessonmenu.mvc`
  `POST /service/lessonmenu.mvc`
  `DELETE /service/lessonmenu.mvc`
  `OPTIONS /service/lessonmenu.mvc`
  `HEAD /service/lessonmenu.mvc`
  `PATCH /service/lessonmenu.mvc`
- Distinct meaning:
  All documented methods map to the same menu retrieval behavior.

### Behavior 117: call disabled security toggle

Behavior name:
call disabled security toggle

Successful execution:
- Result:
  The API returns the message for “Not working...” and does not toggle security.
- Endpoint sequence:
  Step 1: `GET /service/enable-security.mvc`
- Constraints:
  Source uses `@RequestMapping` without a method restriction, so all documented methods return the same message. The restart/toggle code is commented out.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /service/enable-security.mvc`
  - Failure endpoint:
    `GET /service/enable-security.mvc`
  - Why this fails:
    Normal execution returns the static message.
  - Intentionally violated constraints:
    None.

Endpoint coverage:
- Covers:
  `GET /service/enable-security.mvc`
  `PUT /service/enable-security.mvc`
  `POST /service/enable-security.mvc`
  `DELETE /service/enable-security.mvc`
  `OPTIONS /service/enable-security.mvc`
  `HEAD /service/enable-security.mvc`
  `PATCH /service/enable-security.mvc`
- Distinct meaning:
  Documented as a callable service endpoint, but implementation says the feature is disabled.

### Behavior 118: set label debugging status

Behavior name:
set label debugging status

Successful execution:
- Result:
  The API sets the label-debugging enabled flag and returns `{success:true, enabled:<value>}`.
- Endpoint sequence:
  Step 1: `GET /service/debug/labels.mvc`
- Constraints:
  Swagger documents required query parameter `enabled` for all methods. Source also has a no-parameter status-check mapping on the same path, but the OpenAPI file exposes the parameterized setter shape for every documented method.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `enabled` is missing from the documented operation.
  - Endpoint group:
    Step 1: `GET /service/debug/labels.mvc`
  - Failure endpoint:
    `GET /service/debug/labels.mvc`
  - Why this fails:
    Under the Swagger contract, `enabled` is required; in source, a no-param request would be routed to the status-check method instead.
  - Intentionally violated constraints:
    Omitted documented query parameter.

Endpoint coverage:
- Covers:
  `GET /service/debug/labels.mvc`
  `PUT /service/debug/labels.mvc`
  `POST /service/debug/labels.mvc`
  `DELETE /service/debug/labels.mvc`
  `OPTIONS /service/debug/labels.mvc`
  `HEAD /service/debug/labels.mvc`
  `PATCH /service/debug/labels.mvc`
- Distinct meaning:
  Sets label debugging; source also supports an undocumented-by-Swagger status-check variant on the same path.

### Behavior 119: solve challenge 5 login

Behavior name:
solve challenge 5 login

Successful execution:
- Result:
  The API accepts Larry’s login and returns flag 5.
- Endpoint sequence:
  Step 1: `POST /challenge/5`
- Constraints:
  `username_login` must be exactly `Larry`. `password_login` must make the vulnerable SQL query return a row for Larry; seeded password is `larryknows`, and injection is also possible because the query concatenates values.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username or password is blank.
  - Endpoint group:
    Step 1: `POST /challenge/5`
  - Failure endpoint:
    `POST /challenge/5`
  - Why this fails:
    The endpoint checks both fields with `hasText`.
  - Intentionally violated constraints:
    Empty credentials.
- Branch 2:
  - Unsatisfied condition:
    Username is not `Larry`.
  - Endpoint group:
    Step 1: `POST /challenge/5`
  - Failure endpoint:
    `POST /challenge/5`
  - Why this fails:
    The endpoint rejects all non-Larry usernames before querying.
  - Intentionally violated constraints:
    Wrong user.
- Branch 3:
  - Unsatisfied condition:
    Query returns no row.
  - Endpoint group:
    Step 1: `POST /challenge/5`
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

### Behavior 120: send and use challenge 7 reset link

Behavior name:
send and use challenge 7 reset link

Successful execution:
- Result:
  The API sends a password reset link and accepts the admin reset link, returning flag 7.
- Endpoint sequence:
  Step 1: `POST /challenge/7`
  Step 2: `GET /challenge/7/reset-password/{link}`
- Constraints:
  Step 1 with an admin email local part generates a deterministic admin reset link using key `webgoat`; Step 2 must use `{link}=375afe1104f4a487a73823c50a9292a2`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Step 2 uses any other link.
  - Endpoint group:
    Step 1: `POST /challenge/7`
    Step 2: `GET /challenge/7/reset-password/{link}`
  - Failure endpoint:
    `GET /challenge/7/reset-password/{link}`
  - Why this fails:
    The endpoint returns HTTP 418 and “not the reset link for admin”.
  - Intentionally violated constraints:
    Wrong reset link.

Endpoint coverage:
- Covers:
  `POST /challenge/7`
  `GET /challenge/7/reset-password/{link}`
- Distinct meaning:
  Sends a reset link and retrieves challenge 7’s flag through the admin link.

### Behavior 121: read challenge 7 git metadata

Behavior name:
read challenge 7 git metadata

Successful execution:
- Result:
  The API returns a bundled `.git` resource for the challenge.
- Endpoint sequence:
  Step 1: `GET /challenge/7/.git`
- Constraints:
  The endpoint is implemented in the same challenge source but was not in the displayed snippets above; OpenAPI documents it as `assignment-7`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure condition is visible from the documented contract.
  - Endpoint group:
    Step 1: `GET /challenge/7/.git`
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

### Behavior 122: retrieve challenge 8 votes

Behavior name:
retrieve challenge 8 votes

Successful execution:
- Result:
  The API returns current vote counts.
- Endpoint sequence:
  Step 1: `GET /challenge/8/votes/`
- Constraints:
  No setup is required because votes are statically initialized.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `GET /challenge/8/votes/`
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

### Behavior 123: retrieve challenge 8 vote average

Behavior name:
retrieve challenge 8 vote average

Successful execution:
- Result:
  The API returns the rounded-up average star rating.
- Endpoint sequence:
  Step 1: `GET /challenge/8/votes/average`
- Constraints:
  Uses current in-memory vote counts.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented for initialized votes.
  - Endpoint group:
    Step 1: `GET /challenge/8/votes/average`
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

### Behavior 124: attempt challenge 8 vote through documented GET

Behavior name:
attempt challenge 8 vote through documented GET

Successful execution:
- Result:
  The documented GET endpoint returns a JSON message saying login is required; it does not cast a vote.
- Endpoint sequence:
  Step 1: `GET /challenge/8/vote/{stars}`
- Constraints:
  Source contains a non-GET success branch in the method body, but the method is annotated only with `@GetMapping`; therefore the Swagger-documented behavior cannot reach the vote-incrementing branch.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A caller expects a GET request to cast a vote.
  - Endpoint group:
    Step 1: `GET /challenge/8/vote/{stars}`
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

### Behavior 125: retrieve lesson/application metadata services

Behavior name:
retrieve lesson/application metadata services

Successful execution:
- Result:
  The API returns service metadata used by the WebGoat UI.
- Endpoint sequence:
  Step 1: `GET /service/reportcard.mvc`
  Step 2: `GET /service/lessonoverview.mvc/{lesson}`
  Step 3: `GET /service/lessoninfo.mvc/{lesson}`
  Step 4: `GET /service/labels.mvc`
  Step 5: `GET /service/hint.mvc`
  Step 6: `GET /server-directory`
  Step 7: `GET /scoreboard-data`
- Constraints:
  Steps are independent service reads. `{lesson}` must be a valid lesson name where used.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Invalid `{lesson}` is supplied to a lesson-scoped service.
  - Endpoint group:
    Step 1: `GET /service/lessonoverview.mvc/{lesson}`
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

### Behavior 126: solve insecure login support endpoint

Behavior name:
call insecure login support endpoint

Successful execution:
- Result:
  The API returns HTTP 202 Accepted from an otherwise empty login support endpoint.
- Endpoint sequence:
  Step 1: `POST /InsecureLogin/login`
- Constraints:
  The implementation exists only so client JavaScript can call an existing endpoint; it performs no authentication logic.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No application-level failure branch is implemented.
  - Endpoint group:
    Step 1: `POST /InsecureLogin/login`
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
  The implementation throws `IllegalStateException("Should never be called, challenge specific method")`. It is documented but intentionally not a usable behavior.

- `POST /ChromeDevTools/network` with only `networkNum`:
  Source has an auxiliary overload that returns HTTP 200 with an empty body, while Swagger merges it with the solving overload and documents `network_num`, `number`, and `networkNum` together. Its user-visible behavior is only to create a request for inspection.