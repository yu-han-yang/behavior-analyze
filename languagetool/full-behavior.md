### Function 1: list supported languages

Function name:
list supported languages

Core endpoint(s):
- `GET /v2/languages`

Preconditions:
- No caller-created resource state is required. The language catalog comes from LanguageTool's configured in-process language registry, so direct database setup is not applicable and no setup endpoint is needed.

Successful execution:
- Result:
  The endpoint returns the supported LanguageTool languages as JSON objects with `name`, `code`, and `longCode`.
- Invocation:
  Step 1: `GET /v2/languages` with no required path, query, body, form, or header values
- Constraints:
  The implementation sorts languages by display name before returning them. Swagger documents this function as `GET /v2/languages`, but `ApiV2.handleLanguagesRequest` does not enforce the HTTP method for the `/languages` route.

Failure or exceptional branches:
None identified from the documented endpoint or implementation logic.

Endpoint coverage:
- Covers:
  `GET /v2/languages`
- Distinct meaning:
  Retrieves the global language catalog that clients can use when selecting `language`, `motherTongue`, `altLanguages`, or `preferredVariants` values for `/v2/check`.

### Function 2: check plain text

Function name:
check plain text

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. The checked content and language selection are supplied entirely as form parameters to `POST /v2/check`; direct database setup is not applicable for the basic plain-text check.

Successful execution:
- Result:
  The endpoint checks submitted plain text for grammar, spelling, and style matches and returns JSON containing software metadata, the used or detected language, and match objects.
- Invocation:
  Step 1: `POST /v2/check` with form value `text={plainText}` and form value `language={languageCode}`, such as `language=en-US`, `language=de-DE`, or `language=fr`
- Constraints:
  The request must provide `text` and must not provide `data`. `language` must be present and must resolve through `Languages.getLanguageForShortCode`, unless it is `auto` for the automatic-detection function. Swagger documents this function as `POST /v2/check`, but the implementation dispatches `/v2/check` by path and does not explicitly enforce POST for this route.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request omits both checked-content form parameters: no `text` and no `data`. No setup endpoint or direct database state can satisfy this because the failing endpoint reads the checked content directly from request parameters.
    - The request still supplies a valid `language` value such as `language=en-US`, so the violated condition is the missing checked content rather than language resolution.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `ApiV2.handleCheckRequest` requires exactly one checked-content input and throws `IllegalArgumentException("Missing 'text' or 'data' parameter")` when neither parameter is present.
  - Intentionally violated constraints:
    Both `text` and `data` were omitted.
- Branch 2:
  - Preconditions:
    - The request supplies plain checked content as `text={plainText}` and omits `data`, so content parsing can proceed without structured-data conflicts.
    - The request omits the required `language` form value. No setup endpoint or direct database state can satisfy this because `V2TextChecker.checkParams` validates the request parameter itself.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `V2TextChecker.checkParams` rejects the request with a missing-language error before language lookup and rule execution.
  - Intentionally violated constraints:
    Required form value `language` was omitted.
- Branch 3:
  - Preconditions:
    - The request supplies plain checked content as `text={plainText}` and omits `data`, so content parsing can proceed without structured-data conflicts.
    - The request supplies `language=unknown-code`, which is not a configured LanguageTool language code. No setup endpoint can create this language for the request; it would require changing the server language registry rather than database state.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `V2TextChecker.getLanguage` calls `Languages.getLanguageForShortCode` for explicit language values, and unknown codes are rejected by the language lookup path.
  - Intentionally violated constraints:
    `language` did not match a supported LanguageTool language code.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Performs the main documented text-checking capability with caller-supplied plain text and an explicit checking language.

### Function 3: check structured or markup-aware text

Function name:
check structured text

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. The structured checked content is supplied as the `data` form parameter to `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint checks text supplied through the `data` JSON form value. The JSON may contain a plain `text` field or an `annotation` array where text is checked and markup is ignored or interpreted as whitespace.
- Invocation:
  Step 1: `POST /v2/check` with form value `language={languageCode}` and form value `data={json}` containing either top-level `text` or top-level `annotation`
- Constraints:
  The request must provide `data` and must not provide `text`. `data` must contain either top-level `text` or top-level `annotation`, not both. Each `annotation` item must contain either `text` or `markup`, not both. `interpretAs` is valid only on markup items. Swagger documents the JSON structure but the implementation is the final authority for rejecting mixed or unsupported annotation objects.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request supplies plain checked content in `text={plainText}`.
    - The same request also supplies structured checked content in `data={json}`. No setup endpoint or direct database state is involved because the conflict is entirely within the failing request parameters.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `ApiV2.handleCheckRequest` rejects requests that contain both `text` and `data`.
  - Intentionally violated constraints:
    Mutually exclusive checked-content inputs `text` and `data` were provided together.
- Branch 2:
  - Preconditions:
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request omits top-level form parameter `text` so the structured-data parser is used.
    - The `data` JSON contains both top-level `text` and top-level `annotation`. No setup endpoint or database state is involved because the invalid state is the submitted JSON shape.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `ApiV2.handleCheckRequest` requires `data` JSON to contain either `text` or `annotation`, not both.
  - Intentionally violated constraints:
    Mutually exclusive `data.text` and `data.annotation` keys were used together.
- Branch 3:
  - Preconditions:
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request omits top-level form parameter `text` so the structured-data parser is used.
    - The `data.annotation` array contains an item with both `text` and `markup`. No setup endpoint or database state is involved because the invalid state is the submitted annotation object.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `ApiV2.getAnnotatedTextFromJson` requires each annotation object to represent either checkable text or markup, and rejects objects containing both fields.
  - Intentionally violated constraints:
    A single annotation object mixed `text` and `markup`.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Checks user text while preserving offsets around markup and optionally converting markup to whitespace through `interpretAs`.

### Function 4: check text with automatic language detection

Function name:
check with automatic language detection

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. The text and automatic-detection controls are supplied as form parameters to `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint detects the language of the submitted text, chooses the detected language or a preferred variant, checks the text, and returns both detected and used language information.
- Invocation:
  Step 1: `POST /v2/check` with `text={plainText}` or valid `data={json}`, `language=auto`, and optional `preferredVariants={variantCodes}`
- Constraints:
  `language` must be `auto`. `preferredVariants`, when present, must be comma-separated full variant codes containing a dash, such as `en-GB` or `de-AT`, and each applicable variant must resolve to a known LanguageTool language. The implementation also accepts `preferredVariants` outside `language=auto` only when undocumented multilingual mode is active; that is outside the documented Swagger surface.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request uses an explicit language such as `language=en-US` instead of `language=auto`.
    - The same request supplies `preferredVariants=en-GB`. No setup endpoint or direct database state is involved because the invalid relationship is between form parameters on the failing request.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `V2TextChecker.getPreferredVariants` rejects `preferredVariants` when `language` is not `auto` and undocumented multilingual mode is not enabled.
  - Intentionally violated constraints:
    `preferredVariants` was used while the documented automatic-detection mode was not selected.
- Branch 2:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request sets `language=auto`, so automatic detection is active.
    - The request supplies `preferredVariants=en`, a base language code rather than a full variant code. No setup endpoint or direct database state is involved because the invalid value is a request parameter.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.detectLanguageOfString` requires each `preferredVariants` entry to contain a dash and throws an invalid-format error for values such as `en`.
  - Intentionally violated constraints:
    `preferredVariants` used a base language code instead of a variant code.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Uses LanguageTool's language detector instead of a caller-selected checking language, while allowing variant preference hints for detected base languages.

### Function 5: check text with mother-tongue false-friend rules

Function name:
check false friends

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. The user's native-language hint is supplied as the `motherTongue` form parameter to `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint checks text while using `motherTongue` to activate false-friend rules for supported language pairs.
- Invocation:
  Step 1: `POST /v2/check` with `text={plainText}` or valid `data={json}`, `language={languageCode}`, and `motherTongue={motherTongueCode}`
- Constraints:
  The request must provide valid checked content, a valid checking `language`, and a `motherTongue` value that resolves through `Languages.getLanguageForShortCode`. False-friend matches appear only when relevant rules exist for the selected checking-language and mother-tongue pair.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid checking language such as `language=de-DE`.
    - The request supplies `motherTongue=unknown-code`, which is not a configured LanguageTool language code. No setup endpoint can create this request-level language; it would require changing the server language registry rather than database state.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.checkText` resolves `motherTongue` through `Languages.getLanguageForShortCode`, and unknown codes are rejected by the language lookup path.
  - Intentionally violated constraints:
    `motherTongue` did not match a supported LanguageTool language code.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Adds user-native-language context to the check so the rule pipeline can surface false-friend issues.

### Function 6: check text with alternative-language hinting

Function name:
check with alternative languages

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. Alternative-language hints are supplied as the `altLanguages` form parameter to `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint checks text while treating words from `altLanguages` as accepted alternatives unless they are similar to words in the main language, in which case matches can be reported as hints.
- Invocation:
  Step 1: `POST /v2/check` with `text={plainText}` or valid `data={json}`, `language={languageCode}`, and `altLanguages={commaSeparatedLanguageCodes}`
- Constraints:
  The request must provide valid checked content, a valid main `language`, and comma-separated `altLanguages` values that resolve to known LanguageTool languages. For languages with variants, `altLanguages` must use a concrete variant code such as `en-GB`, not the base code `en`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid main language such as `language=de-DE`.
    - The request supplies `altLanguages=en`, where `en` resolves to a language with variants but is not itself a concrete variant. No setup endpoint or direct database state is involved because the invalid state is the form value.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.checkText` rejects an alternative language when `altLang.hasVariant()` is true and `altLang.isVariant()` is false.
  - Intentionally violated constraints:
    `altLanguages` used a base language code that requires a concrete variant such as `en-US` or `en-GB`.
- Branch 2:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid main language such as `language=en-US`.
    - The request supplies `altLanguages=xy`, which is not a configured LanguageTool language code. No setup endpoint can create this request-level language; it would require changing the server language registry rather than database state.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.checkText` resolves each `altLanguages` entry through `Languages.getLanguageForShortCode`, and unknown codes are rejected by the language lookup path.
  - Intentionally violated constraints:
    `altLanguages` contained an unsupported language code.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Changes spell-check evaluation by passing additional accepted languages into the rule pipeline for a single check request.

### Function 7: check text with enabled or disabled rules and categories

Function name:
filter rules and categories

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. Rule and category filters are supplied as request form parameters to `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint checks text while enabling or disabling specific rule IDs or category IDs for the request.
- Invocation:
  Step 1: `POST /v2/check` with `text={plainText}` or valid `data={json}`, `language={languageCode}`, and any of `enabledRules`, `disabledRules`, `enabledCategories`, or `disabledCategories` as comma-separated IDs
- Constraints:
  Without `enabledOnly=true` or `enabledOnly=yes`, enabled selections do not disable all other rules. The implementation parses `enabledRules` specially in `V2TextChecker` and parses disabled rules and category IDs as comma-separated strings. If a rule is both enabled and disabled, the effective rule configuration is determined by the downstream rule pipeline, with explicit request settings applied for this check.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request uses obsolete form parameter `enabled=RULE_ID` instead of `enabledRules=RULE_ID`. No setup endpoint or direct database state is involved because the invalid state is the request parameter name.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `V2TextChecker.checkParams` rejects the old v1 parameter name `enabled` and requires `enabledRules` for v2.
  - Intentionally violated constraints:
    Obsolete parameter name `enabled` was used.
- Branch 2:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request uses obsolete form parameter `disabled=RULE_ID` instead of `disabledRules=RULE_ID`. No setup endpoint or direct database state is involved because the invalid state is the request parameter name.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `V2TextChecker.checkParams` rejects the old v1 parameter name `disabled` and requires `disabledRules` for v2.
  - Intentionally violated constraints:
    Obsolete parameter name `disabled` was used.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Customizes which checks run for one `/v2/check` invocation without restricting execution to only selected checks.

### Function 8: check text using only selected rules or categories

Function name:
check only selected rules

Core endpoint(s):
- `POST /v2/check`

Preconditions:
- No persistent resource state is required. The selected-only mode is controlled entirely by `enabledOnly`, `enabledRules`, and `enabledCategories` form parameters on `POST /v2/check`; direct database setup is not applicable.

Successful execution:
- Result:
  The endpoint checks text with all rules disabled except the rules or categories explicitly enabled in the request.
- Invocation:
  Step 1: `POST /v2/check` with `text={plainText}` or valid `data={json}`, `language={languageCode}`, `enabledOnly=true` or `enabledOnly=yes`, and at least one of `enabledRules={ruleIds}` or `enabledCategories={categoryIds}`
- Constraints:
  `enabledOnly` is active only for values `true` or `yes`. When active, at least one enabled rule or category must be supplied, and the request must not include `disabledRules` or `disabledCategories`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request sets `enabledOnly=true` but omits both `enabledRules` and `enabledCategories`. No setup endpoint or direct database state is involved because the invalid state is the set of request parameters.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.checkText` requires at least one enabled rule or enabled category when `enabledOnly` mode is active.
  - Intentionally violated constraints:
    `enabledOnly=true` was used without any enabled rule or category selection.
- Branch 2:
  - Preconditions:
    - The request supplies checked content as `text={plainText}` or valid `data={json}`.
    - The request supplies a valid `language` value such as `language=en-US`.
    - The request sets `enabledOnly=true` and supplies `enabledRules=RULE_ID`, so selected-only mode has a positive selection.
    - The same request also supplies `disabledRules=OTHER_RULE_ID` or `disabledCategories=CATEGORY_ID`. No setup endpoint or direct database state is involved because the conflict is within the failing request parameters.
  - Failure endpoint:
    `POST /v2/check`
  - Why this fails:
    `TextChecker.checkText` rejects disabled rules or categories when `enabledOnly` mode is active.
  - Intentionally violated constraints:
    Disabled selections were combined with `enabledOnly=true`.

Endpoint coverage:
- Covers:
  `POST /v2/check`
- Distinct meaning:
  Turns `/v2/check` into a targeted rule/category-only check instead of the normal full rule set.
