### Function 1: register user

Function name:
register user

Core endpoint(s):
- `POST /api/auth/register`

Preconditions:
- No user with `username = {username}` exists in the `User` collection. This can be satisfied by starting from a database without that username, deleting the user beforehand, or ensuring no direct database insert has created the same username.

Successful execution:
- Result:
  Creates a disabled user account, stores a verification token, and sends an activation email.
- Invocation:
  Step 1: `POST /api/auth/register` with JSON body fields `username`, `name`, `email`, `password`, and `confirmPassword`.
- Constraints:
  `username`, `name`, `email`, `password`, and `confirmPassword` must be nonblank. `email` must be email-shaped. `password` must be 8 to 20 characters. `username` must not already exist. The implementation stores `email`, `username`, and an encoded `password`; `name` is not persisted and `confirmPassword` is not compared with `password`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username = {username}` already exists in the `User` collection. This can be satisfied by directly inserting a `User` document with that username or by calling `POST /api/auth/register` once with `username = {username}` before the failing request.
  - Failure endpoint:
    `POST /api/auth/register`
  - Why this fails:
    The controller calls `authService.existsByUserName`, which delegates to `userRepository.existsByUsername`; when the username exists, the endpoint returns HTTP 400 with message `Username already exists`.
  - Intentionally violated constraints:
    The registration request reuses an existing `username`.
- Branch 2:
  - Preconditions:
    - The request body contains invalid registration data, such as blank required fields, an invalid `email`, or a `password` shorter than 8 characters or longer than 20 characters. This invalid state is supplied directly in the failing `POST /api/auth/register` body; no setup endpoint is needed.
  - Failure endpoint:
    `POST /api/auth/register`
  - Why this fails:
    The controller applies `@Valid` to `RegisterRequestDto`, whose fields use `@NotBlank`, `@Email`, and `@Size(min = 8, max = 20)`.
  - Intentionally violated constraints:
    Required registration fields or validation bounds are not satisfied.

Endpoint coverage:
- Covers:
  `POST /api/auth/register`
- Distinct meaning:
  Creates a disabled account and starts the email verification workflow.

### Function 2: verify account

Function name:
verify account

Core endpoint(s):
- `GET /api/auth/accountVerification/{token}`

Preconditions:
- A disabled user exists in the `User` collection. This can be satisfied by directly inserting a `User` document with `enabled = false` or by calling `POST /api/auth/register` with a valid registration body.
- A `VerificationToken` document exists whose `token = {token}` and whose embedded user has the username of the disabled user. This can be satisfied by directly inserting a `VerificationToken` linked to the user or by calling `POST /api/auth/register`, which generates and stores the token.
- The `{token}` path value must be the generated verification token. When the API is used to create the token, it is embedded in the activation email URL and is not returned in the registration response body.

Successful execution:
- Result:
  Enables the user account associated with the verification token.
- Invocation:
  Step 1: `GET /api/auth/accountVerification/{token}` with `{token}` set to a saved verification-token value.
- Constraints:
  `{token}` must resolve to a saved `VerificationToken`. The implementation uses the token's embedded user username to load the `User` document, sets `enabled = true`, and saves the user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `VerificationToken` document exists with `token = {token}`. This can be produced by starting from a database without that token, deleting the token beforehand, using a fabricated token value, or intentionally not calling `POST /api/auth/register` for that token.
  - Failure endpoint:
    `GET /api/auth/accountVerification/{token}`
  - Why this fails:
    `verificationTokenRepository.findByToken` returns empty. The implementation returns an `ApiResponse` with status `400` and message `Invalid Token`.
  - Intentionally violated constraints:
    `{token}` is not a token generated for a registered user.
- Branch 2:
  - Preconditions:
    - A `VerificationToken` document exists with `token = {token}`, but its embedded username does not identify any saved `User`. This can be satisfied by directly inserting a mismatched token document or by deleting the user after creating the token with `POST /api/auth/register`.
  - Failure endpoint:
    `GET /api/auth/accountVerification/{token}`
  - Why this fails:
    The token lookup succeeds, but `userRepository.findByUsername` fails and `fetchUserAndEnable` throws `SpringStoreException("User Not Found with id - ...")`.
  - Intentionally violated constraints:
    The token-to-user relationship does not resolve to a persisted user.

Endpoint coverage:
- Covers:
  `GET /api/auth/accountVerification/{token}`
- Distinct meaning:
  Completes account activation from a verification-token workflow.

### Function 3: log in and obtain token

Function name:
log in and obtain token

Core endpoint(s):
- `POST /api/auth/login`

Preconditions:
- A user with `username = {username}` exists in the `User` collection with a BCrypt-encoded password matching raw `{password}`. This can be satisfied by directly inserting the user with an encoded password or by calling `POST /api/auth/register` with the desired `username` and `password`.
- The user is enabled. This can be satisfied by directly setting `enabled = true` on the `User` document or by calling `GET /api/auth/accountVerification/{token}` with the verification token generated for the registered user.
- If the API is used to establish the user, `{token}` must come from the verification token generated during `POST /api/auth/register`; the login body must reuse the same `username` and raw `password` used during registration.

Successful execution:
- Result:
  Authenticates an enabled user and returns an access token plus the username.
- Invocation:
  Step 1: `POST /api/auth/login` with JSON body fields `username` and `password`.
- Constraints:
  `username` and `password` must be nonblank. The credentials must match an enabled saved user. The returned `accessToken` is used as `Authorization: Bearer {accessToken}` for protected endpoints.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username = {username}` exists and has a password matching raw `{password}`, but `enabled = false`. This can be satisfied by directly inserting a disabled user or by calling `POST /api/auth/register` and intentionally not calling `GET /api/auth/accountVerification/{token}`.
  - Failure endpoint:
    `POST /api/auth/login`
  - Why this fails:
    Registration stores disabled users, and Spring Security authentication rejects disabled accounts.
  - Intentionally violated constraints:
    The required account-enabled state is omitted.
- Branch 2:
  - Preconditions:
    - No enabled saved user matches the submitted `username` and `password`. This can be produced by using an unknown `username`, a wrong `password` for an existing user, or direct database state that does not match the login body.
  - Failure endpoint:
    `POST /api/auth/login`
  - Why this fails:
    `AuthenticationManager.authenticate` rejects credentials that do not match an enabled user.
  - Intentionally violated constraints:
    The login body does not match an enabled saved user.
- Branch 3:
  - Preconditions:
    - The login body has a blank `username` or blank `password`. This invalid state is supplied directly in the failing request body.
  - Failure endpoint:
    `POST /api/auth/login`
  - Why this fails:
    The controller applies `@Valid` to `LoginRequestDto`, whose fields are `@NotBlank`.
  - Intentionally violated constraints:
    Required login fields are blank.

Endpoint coverage:
- Covers:
  `POST /api/auth/login`
- Distinct meaning:
  Produces a JWT-style access token for an enabled user.

### Function 4: list categories

Function name:
list categories

Core endpoint(s):
- `GET /api/store/catalog/categories`

Preconditions:
- No specific category must exist. Categories can be inserted directly into the `Category` collection to make the response nonempty; the OpenAPI file and controllers do not expose a category-creation endpoint.

Successful execution:
- Result:
  Retrieves all product categories as category-name DTOs.
- Invocation:
  Step 1: `GET /api/store/catalog/categories`.
- Constraints:
  The endpoint is public according to security configuration. An empty `Category` repository returns an empty list.

Failure or exceptional branches:
None identified from the implementation beyond repository or infrastructure failures.

Endpoint coverage:
- Covers:
  `GET /api/store/catalog/categories`
- Distinct meaning:
  Lists available catalog categories.

### Function 5: list products

Function name:
list products

Core endpoint(s):
- `GET /api/store/catalog/products`

Preconditions:
- No specific product must exist. Products can be inserted directly into the `Product` collection to make the response nonempty; the OpenAPI file and controllers do not expose a product-creation endpoint.

Successful execution:
- Result:
  Retrieves all products as catalog product DTOs.
- Invocation:
  Step 1: `GET /api/store/catalog/products`.
- Constraints:
  The endpoint is public according to security configuration. Product availability is derived from `quantity`, but the implementation labels both in-stock and out-of-stock products as `Out of Stock`; only the color differs.

Failure or exceptional branches:
None identified from the implementation beyond repository or infrastructure failures.

Endpoint coverage:
- Covers:
  `GET /api/store/catalog/products`
- Distinct meaning:
  Lists all products in the catalog.

### Function 6: list featured products

Function name:
list featured products

Core endpoint(s):
- `GET /api/store/catalog/products/featured`

Preconditions:
- No specific featured product must exist. Featured products can be created by directly inserting `Product` documents with `featured = true`; the OpenAPI file and controllers do not expose a product-creation endpoint.

Successful execution:
- Result:
  Retrieves only products whose mapped DTO has `featured = true`.
- Invocation:
  Step 1: `GET /api/store/catalog/products/featured`.
- Constraints:
  The endpoint is public according to security configuration. It first loads all products through `productService.findAll()`, maps them to DTOs, and filters by `ProductDto.isFeatured()`.

Failure or exceptional branches:
None identified from the implementation beyond repository or infrastructure failures.

Endpoint coverage:
- Covers:
  `GET /api/store/catalog/products/featured`
- Distinct meaning:
  Lists the featured subset of catalog products.

### Function 7: retrieve product by SKU

Function name:
retrieve product by SKU

Core endpoint(s):
- `GET /api/store/catalog/products/{sku}`

Preconditions:
- A `Product` document exists with `sku = {sku}`. This can be satisfied by directly inserting the product into the `Product` collection; the OpenAPI file and controllers do not expose a product-creation endpoint.

Successful execution:
- Result:
  Retrieves the product whose SKU equals `{sku}`.
- Invocation:
  Step 1: `GET /api/store/catalog/products/{sku}` with `{sku}` set to an existing product SKU.
- Constraints:
  `{sku}` must match `Product.sku` in Mongo. The endpoint is public according to security configuration.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `Product` document exists with `sku = {sku}`. This can be produced by starting from a database without that SKU, deleting the product beforehand, or intentionally not inserting it directly; no documented product-creation endpoint exists.
  - Failure endpoint:
    `GET /api/store/catalog/products/{sku}`
  - Why this fails:
    `productRepository.findBySku` returns empty and `ProductService.readOneProduct` throws `IllegalArgumentException`.
  - Intentionally violated constraints:
    `{sku}` does not match an existing product.

Endpoint coverage:
- Covers:
  `GET /api/store/catalog/products/{sku}`
- Distinct meaning:
  Reads one product by SKU.

### Function 8: list products by category

Function name:
list products by category

Core endpoint(s):
- `GET /api/store/catalog/products/category/{categoryName}`

Preconditions:
- A `Category` document exists with `name = {categoryName}`. This can be satisfied by directly inserting the category into the `Category` collection; the OpenAPI file and controllers do not expose a category-creation endpoint.
- Any products that should appear in the response exist in the `Product` collection with `category` equal to the resolved `Category` object. This can be satisfied by directly inserting products linked to that category; no documented product-creation endpoint exists.

Successful execution:
- Result:
  Retrieves products whose saved category equals the category named `{categoryName}`.
- Invocation:
  Step 1: `GET /api/store/catalog/products/category/{categoryName}` with `{categoryName}` set to an existing category name.
- Constraints:
  `{categoryName}` must resolve through `CategoryRepository.findByName`. Products are queried by the resolved `Category` object. The endpoint is public according to security configuration.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `Category` document exists with `name = {categoryName}`. This can be produced by starting from a database without that category, deleting it beforehand, or intentionally not inserting it directly.
  - Failure endpoint:
    `GET /api/store/catalog/products/category/{categoryName}`
  - Why this fails:
    `categoryRepository.findByName` returns empty and `ProductService.findByCategoryName` throws `IllegalArgumentException("Category Not Found")`.
  - Intentionally violated constraints:
    `{categoryName}` does not match an existing category.

Endpoint coverage:
- Covers:
  `GET /api/store/catalog/products/category/{categoryName}`
- Distinct meaning:
  Lists products under a named category.

### Function 9: search catalog globally

Function name:
search catalog globally

Core endpoint(s):
- `POST /api/store/catalog/search`

Preconditions:
- Elasticsearch index `product` may contain searchable product documents. This can be satisfied by direct Elasticsearch setup; the OpenAPI file and controllers do not expose an endpoint that indexes products.
- The request body contains `filters` as a non-null list. This state is supplied directly in the core request body and may be an empty list.

Successful execution:
- Result:
  Searches products in Elasticsearch by `textQuery` against product `name` and `description`, and returns products, min/max price, Brand facets, and Category facets.
- Invocation:
  Step 1: `POST /api/store/catalog/search` with JSON body fields `textQuery` and `filters`.
- Constraints:
  `filters` must be present as a list using entries with `key` and `value`, or an empty list. `from` and `to` are defined in the DTO but not used. In this endpoint, filters affect the filtered aggregation and min/max calculation, but the implementation does not apply them as a post-filter to returned product hits. The endpoint is public according to security configuration.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The search request body omits `filters` or sets `filters = null`. This invalid state is supplied directly in the failing `POST /api/store/catalog/search` body.
  - Failure endpoint:
    `POST /api/store/catalog/search`
  - Why this fails:
    `SearchService.createAggregations` iterates over `searchQueryDto.getFilters()` without a null guard.
  - Intentionally violated constraints:
    The body does not provide a filter list, even an empty one.

Endpoint coverage:
- Covers:
  `POST /api/store/catalog/search`
- Distinct meaning:
  Performs global full-text product search with response facets.

### Function 10: filter category catalog with blank text query

Function name:
filter category catalog

Core endpoint(s):
- `POST /api/store/catalog/{categoryName}/facets/filter`

Preconditions:
- A `Category` document exists with `name = {categoryName}` and a `possibleFacets` list. This can be satisfied by directly inserting the category into the `Category` collection; the OpenAPI file and controllers do not expose a category-creation endpoint.
- Elasticsearch index `product` may contain product documents whose `category.name` or nested attributes can match the request. This can be satisfied by direct Elasticsearch setup; no documented endpoint indexes products.
- The request body contains `filters` as a non-null list. This state is supplied directly in the core request body and may be an empty list.

Successful execution:
- Result:
  Searches products in the named category and applies attribute filters to the returned hits.
- Invocation:
  Step 1: `POST /api/store/catalog/{categoryName}/facets/filter` with `{categoryName}`, blank or absent body field `textQuery`, and non-null `filters`.
- Constraints:
  `{categoryName}` must match an existing category. With blank `textQuery`, the implementation searches `category.name` using `{categoryName}`. Each filter uses `key` as product attribute name and `value` as product attribute value. The endpoint is public according to security configuration.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `Category` document exists with `name = {categoryName}`. This can be produced by starting from a database without that category, deleting it beforehand, or intentionally not inserting it directly.
  - Failure endpoint:
    `POST /api/store/catalog/{categoryName}/facets/filter`
  - Why this fails:
    `SearchService.searchWithFilters` must load the category to obtain `possibleFacets`; a missing category throws `SpringStoreException("Category - ... Not Found")`.
  - Intentionally violated constraints:
    `{categoryName}` does not match an existing category.
- Branch 2:
  - Preconditions:
    - A `Category` document exists with `name = {categoryName}`. This can be satisfied by direct insertion into the `Category` collection.
    - The request body omits `filters` or sets `filters = null`. This invalid state is supplied directly in the failing request body.
  - Failure endpoint:
    `POST /api/store/catalog/{categoryName}/facets/filter`
  - Why this fails:
    The service iterates over `searchQueryDto.getFilters()` without a null guard while building aggregations or post filters.
  - Intentionally violated constraints:
    The body does not provide a filter list.

Endpoint coverage:
- Covers:
  `POST /api/store/catalog/{categoryName}/facets/filter`
- Distinct meaning:
  Uses the path category as the search category when `textQuery` is blank.

### Function 11: search text using category facet set

Function name:
search text with category facets

Core endpoint(s):
- `POST /api/store/catalog/{categoryName}/facets/filter`

Preconditions:
- A `Category` document exists with `name = {categoryName}` and a `possibleFacets` list. This can be satisfied by directly inserting the category into the `Category` collection; the OpenAPI file and controllers do not expose a category-creation endpoint.
- Elasticsearch index `product` may contain product documents matching `textQuery` in `name` or `description`. This can be satisfied by direct Elasticsearch setup; no documented endpoint indexes products.
- The request body contains a nonblank `textQuery` and a non-null `filters` list. This state is supplied directly in the core request body and `filters` may be empty.

Successful execution:
- Result:
  Searches product `name` and `description` using a nonblank `textQuery`, while using `{categoryName}` only to choose which facet names should be returned.
- Invocation:
  Step 1: `POST /api/store/catalog/{categoryName}/facets/filter` with existing `{categoryName}`, nonblank body field `textQuery`, and non-null `filters`.
- Constraints:
  `{categoryName}` must exist because its `possibleFacets` are loaded. With nonblank `textQuery`, implementation does not constrain product hits to `{categoryName}`; it searches product `name` and `description` instead. `filters` are applied as post-filters to hits. The endpoint is public according to security configuration.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `Category` document exists with `name = {categoryName}`. This can be produced by starting from a database without that category, deleting it beforehand, or intentionally not inserting it directly.
    - The request body contains a nonblank `textQuery` and a non-null `filters` list.
  - Failure endpoint:
    `POST /api/store/catalog/{categoryName}/facets/filter`
  - Why this fails:
    The category lookup fails before search execution, so `SearchService.searchWithFilters` throws `SpringStoreException("Category - ... Not Found")`.
  - Intentionally violated constraints:
    `{categoryName}` does not match an existing category.
- Branch 2:
  - Preconditions:
    - A `Category` document exists with `name = {categoryName}`. This can be satisfied by direct insertion into the `Category` collection.
    - The request body contains a nonblank `textQuery` but omits `filters` or sets `filters = null`.
  - Failure endpoint:
    `POST /api/store/catalog/{categoryName}/facets/filter`
  - Why this fails:
    The service iterates over `searchQueryDto.getFilters()` without a null guard.
  - Intentionally violated constraints:
    The body does not provide a filter list.

Endpoint coverage:
- Covers:
  `POST /api/store/catalog/{categoryName}/facets/filter`
- Distinct meaning:
  Nonblank `textQuery` changes the endpoint from category-name search to product text search while retaining the category's facet configuration.

### Function 12: submit product rating

Function name:
submit product rating

Core endpoint(s):
- `POST /api/products/ratings/submit`

Preconditions:
- An enabled user exists and can authenticate. This can be satisfied by directly inserting a `User` with `enabled = true` and an encoded password or by calling `POST /api/auth/register`, then `GET /api/auth/accountVerification/{token}` with the generated token.
- A valid bearer token exists for the enabled user. This can be satisfied by using the configured JWT generator directly in test setup or by calling `POST /api/auth/login` with that user's `username` and raw `password` and reusing the returned `accessToken`.
- A Mongo `Product` document exists with `sku = {sku}`. This can be satisfied by directly inserting the product; no documented product-creation endpoint exists.
- An Elasticsearch `ElasticSearchProduct` document exists with `sku = {sku}`. This can be satisfied by direct Elasticsearch setup; no documented endpoint indexes products.

Successful execution:
- Result:
  Adds a new rating to the Mongo product and matching Elasticsearch product identified by `sku`.
- Invocation:
  Step 1: `POST /api/products/ratings/submit` with header `Authorization: Bearer {accessToken}` and JSON body fields `sku`, `ratingStars`, `review`, and `userName`.
- Constraints:
  The endpoint is protected even though Swagger has no security definition. `sku` must exist in both Mongo `Product` and Elasticsearch `ElasticSearchProduct`. `ratingStars`, when supplied, must be between 1 and 5. The service generates a new rating UUID, stores product and Elasticsearch product IDs on the rating object, and does not return the generated `ratingId`. `userName` is saved from the body and is not checked against the authenticated user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid `Authorization: Bearer {accessToken}` header is supplied. This can be produced by omitting authentication entirely, using an invalid token, or intentionally not calling `POST /api/auth/login` to obtain a token.
  - Failure endpoint:
    `POST /api/products/ratings/submit`
  - Why this fails:
    Security configuration permits only `/api/auth/**`, `/api/store/catalog/**`, and `/v2/api-docs/**`; all other requests require authentication.
  - Intentionally violated constraints:
    The bearer token requirement is omitted or invalid.
- Branch 2:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using `POST /api/auth/register`, `GET /api/auth/accountVerification/{token}`, and `POST /api/auth/login`.
    - No Mongo `Product` or no Elasticsearch `ElasticSearchProduct` exists with `sku = {sku}`. This can be produced by deleting one store's document, using an unknown SKU, or intentionally not inserting matching product documents directly.
  - Failure endpoint:
    `POST /api/products/ratings/submit`
  - Why this fails:
    The service looks up the SKU in both repositories and throws `SpringStoreException("No Product exists with sku - ...")` if either lookup fails.
  - Intentionally violated constraints:
    `sku` does not identify an existing product in both product stores.
- Branch 3:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - Matching Mongo and Elasticsearch product documents exist with `sku = {sku}`. This can be satisfied by direct database and Elasticsearch setup.
    - The request body sets `ratingStars` below 1 or above 5.
  - Failure endpoint:
    `POST /api/products/ratings/submit`
  - Why this fails:
    The controller applies `@Valid` to `ProductRatingDto`, whose `ratingStars` field has `@Min(1)` and `@Max(5)`.
  - Intentionally violated constraints:
    The rating value is outside the accepted range.

Endpoint coverage:
- Covers:
  `POST /api/products/ratings/submit`
- Distinct meaning:
  Creates a product rating entry.

### Function 13: retrieve product ratings

Function name:
retrieve product ratings

Core endpoint(s):
- `GET /api/products/ratings/get/{sku}`

Preconditions:
- An enabled user exists and can authenticate. This can be satisfied by directly inserting a `User` with `enabled = true` and an encoded password or by calling `POST /api/auth/register`, then `GET /api/auth/accountVerification/{token}` with the generated token.
- A valid bearer token exists for the enabled user. This can be satisfied by using the configured JWT generator directly in test setup or by calling `POST /api/auth/login` with that user's `username` and raw `password` and reusing the returned `accessToken`.
- A Mongo `Product` document exists with `sku = {sku}` and a non-null `productRating` list. This can be satisfied by directly inserting the product with ratings or by calling `POST /api/products/ratings/submit` with the same `sku` after setting up matching Mongo and Elasticsearch product documents.
- If the API is used to create ratings, the generated `ratingId` values are available by invoking this endpoint after submission; the submit endpoint itself does not return them.

Successful execution:
- Result:
  Retrieves the rating list for a product SKU, including generated `ratingId` values.
- Invocation:
  Step 1: `GET /api/products/ratings/get/{sku}` with header `Authorization: Bearer {accessToken}` and `{sku}` set to the product SKU.
- Constraints:
  `{sku}` must match a Mongo `Product`. The product's `productRating` list must be non-null because the implementation calls `.stream()` on it. The endpoint is protected even though Swagger has no security definition.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid `Authorization: Bearer {accessToken}` header is supplied. This can be produced by omitting authentication entirely, using an invalid token, or intentionally not calling `POST /api/auth/login` to obtain a token.
  - Failure endpoint:
    `GET /api/products/ratings/get/{sku}`
  - Why this fails:
    The endpoint is protected by security configuration because `/api/products/ratings/**` is not in the permit list.
  - Intentionally violated constraints:
    The bearer token requirement is omitted or invalid.
- Branch 2:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - No Mongo `Product` document exists with `sku = {sku}`. This can be produced by deleting the product, using an unknown SKU, or intentionally not inserting it directly.
  - Failure endpoint:
    `GET /api/products/ratings/get/{sku}`
  - Why this fails:
    `productRepository.findBySku` returns empty and the service throws `SpringStoreException("No Product exists with sku - ...")`.
  - Intentionally violated constraints:
    `{sku}` does not match an existing Mongo product.
- Branch 3:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - A Mongo `Product` document exists with `sku = {sku}` but `productRating = null`. This can be satisfied by directly setting the field to null or by calling `DELETE /api/products/ratings/delete/{ratingId}` with body `sku = {sku}`, which clears the rating list.
  - Failure endpoint:
    `GET /api/products/ratings/get/{sku}`
  - Why this fails:
    `ProductService.getProductRating` calls `product.getProductRating().stream()` without checking for null.
  - Intentionally violated constraints:
    The request reads ratings after the rating list has been omitted or cleared.

Endpoint coverage:
- Covers:
  `GET /api/products/ratings/get/{sku}`
- Distinct meaning:
  Reads ratings for a product and exposes generated rating IDs.

### Function 14: edit product rating

Function name:
edit product rating

Core endpoint(s):
- `PUT /api/products/ratings/edit`

Preconditions:
- An enabled user exists and can authenticate. This can be satisfied by directly inserting a `User` with `enabled = true` and an encoded password or by calling `POST /api/auth/register`, then `GET /api/auth/accountVerification/{token}` with the generated token.
- A valid bearer token exists for the enabled user. This can be satisfied by using the configured JWT generator directly in test setup or by calling `POST /api/auth/login` with that user's `username` and raw `password` and reusing the returned `accessToken`.
- A Mongo `Product` document exists with `sku = {sku}` and a `productRating` list containing a rating with `id = {ratingId}`. This can be satisfied by directly inserting the product and rating or by calling `POST /api/products/ratings/submit` with the same `sku`.
- An Elasticsearch `ElasticSearchProduct` document exists with `sku = {sku}`. This can be satisfied by direct Elasticsearch setup; no documented endpoint indexes products.
- The `{ratingId}` in the edit body must identify a rating in the Mongo product's `productRating` list. If the API is used to create the rating, `{ratingId}` must be obtained from `GET /api/products/ratings/get/{sku}` after submission.

Successful execution:
- Result:
  Updates the stars and review text of an existing Mongo product rating.
- Invocation:
  Step 1: `PUT /api/products/ratings/edit` with header `Authorization: Bearer {accessToken}` and JSON body containing `sku`, `ratingId`, replacement `ratingStars`, and replacement `review`.
- Constraints:
  `sku` must exist in both Mongo and Elasticsearch. `ratingId` must exist on the Mongo product's rating list. `ratingStars`, when supplied, must be between 1 and 5. The implementation updates the Mongo product rating object; it fetches and saves the Elasticsearch product but does not modify its rating list before saving. The endpoint is protected even though Swagger has no security definition.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - Matching Mongo and Elasticsearch product documents exist with `sku = {sku}`. This can be satisfied by direct database and Elasticsearch setup or, for product state only, by existing fixtures.
    - The Mongo product's rating list does not contain `ratingId = {ratingId}`. This can be produced by using a fabricated rating ID, reusing a rating ID from a different product, or intentionally not retrieving the generated ID with `GET /api/products/ratings/get/{sku}`.
  - Failure endpoint:
    `PUT /api/products/ratings/edit`
  - Why this fails:
    The service searches the product rating list for the requested ID and throws `SpringStoreException("No Rating found with id - ...")` when it is absent.
  - Intentionally violated constraints:
    `ratingId` does not identify a rating on the product identified by `sku`.
- Branch 2:
  - Preconditions:
    - No valid `Authorization: Bearer {accessToken}` header is supplied. This can be produced by omitting authentication entirely, using an invalid token, or intentionally not calling `POST /api/auth/login` to obtain a token.
  - Failure endpoint:
    `PUT /api/products/ratings/edit`
  - Why this fails:
    The endpoint is protected by security configuration because `/api/products/ratings/**` is not in the permit list.
  - Intentionally violated constraints:
    The bearer token requirement is omitted or invalid.
- Branch 3:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - Matching Mongo and Elasticsearch product documents exist with `sku = {sku}`, and the Mongo product contains `ratingId = {ratingId}`.
    - The request body sets `ratingStars` below 1 or above 5.
  - Failure endpoint:
    `PUT /api/products/ratings/edit`
  - Why this fails:
    The controller applies `@Valid` to `ProductRatingDto`, whose `ratingStars` field has `@Min(1)` and `@Max(5)`.
  - Intentionally violated constraints:
    The replacement rating value is outside the accepted range.
- Branch 4:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - No Mongo `Product` or no Elasticsearch `ElasticSearchProduct` exists with `sku = {sku}`. This can be produced by deleting one store's document, using an unknown SKU, or intentionally not inserting matching product documents directly.
  - Failure endpoint:
    `PUT /api/products/ratings/edit`
  - Why this fails:
    The service looks up the SKU in both repositories and throws `SpringStoreException("No Product exists with sku - ...")` if either lookup fails.
  - Intentionally violated constraints:
    `sku` does not identify an existing product in both product stores.

Endpoint coverage:
- Covers:
  `PUT /api/products/ratings/edit`
- Distinct meaning:
  Updates an existing rating's stars and review text.

### Function 15: delete product ratings

Function name:
delete product ratings

Core endpoint(s):
- `DELETE /api/products/ratings/delete/{ratingId}`

Preconditions:
- An enabled user exists and can authenticate. This can be satisfied by directly inserting a `User` with `enabled = true` and an encoded password or by calling `POST /api/auth/register`, then `GET /api/auth/accountVerification/{token}` with the generated token.
- A valid bearer token exists for the enabled user. This can be satisfied by using the configured JWT generator directly in test setup or by calling `POST /api/auth/login` with that user's `username` and raw `password` and reusing the returned `accessToken`.
- A Mongo `Product` document exists with `sku = {sku}`. This can be satisfied by directly inserting the product or by relying on existing fixtures; no documented product-creation endpoint exists.
- An Elasticsearch `ElasticSearchProduct` document exists with `sku = {sku}`. This can be satisfied by direct Elasticsearch setup; no documented endpoint indexes products.
- A path `{ratingId}` value is required by the OpenAPI file and controller path, but source code ignores it. If a meaningful rating ID is desired for contract-level consistency, it can be obtained by calling `GET /api/products/ratings/get/{sku}` after creating a rating with `POST /api/products/ratings/submit`.

Successful execution:
- Result:
  Clears all ratings from the Mongo product and matching Elasticsearch product identified by the request body `sku`.
- Invocation:
  Step 1: `DELETE /api/products/ratings/delete/{ratingId}` with header `Authorization: Bearer {accessToken}` and JSON body containing `sku`.
- Constraints:
  OpenAPI and the controller path require `{ratingId}`, but the implementation ignores the path variable and uses only body `sku`. The visible effect is clearing all ratings for the body product, not deleting one rating by ID. The endpoint is protected even though Swagger has no security definition.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid `Authorization: Bearer {accessToken}` header is supplied. This can be produced by omitting authentication entirely, using an invalid token, or intentionally not calling `POST /api/auth/login` to obtain a token.
  - Failure endpoint:
    `DELETE /api/products/ratings/delete/{ratingId}`
  - Why this fails:
    The endpoint is protected by security configuration because `/api/products/ratings/**` is not in the permit list.
  - Intentionally violated constraints:
    The bearer token requirement is omitted or invalid.
- Branch 2:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - No Mongo `Product` or no Elasticsearch `ElasticSearchProduct` exists with body `sku = {sku}`. This can be produced by deleting one store's document, using an unknown SKU, or intentionally not inserting matching product documents directly.
  - Failure endpoint:
    `DELETE /api/products/ratings/delete/{ratingId}`
  - Why this fails:
    The service looks up the product and Elasticsearch product by body `sku` and throws `SpringStoreException("No Product exists with sku - ...")` if either lookup fails.
  - Intentionally violated constraints:
    Body `sku` does not identify an existing product in both stores.
- Branch 3:
  - Preconditions:
    - An enabled authenticated user and valid bearer token exist. This can be satisfied by direct user/token setup or by using the auth endpoints.
    - A rating with `id = {ratingId}` exists on product `sku = A`. This can be satisfied by directly inserting the rating or by calling `POST /api/products/ratings/submit` for product `A` and then `GET /api/products/ratings/get/A` to obtain the generated ID.
    - A different product exists with `sku = B` in both Mongo and Elasticsearch. This can be satisfied by direct database and Elasticsearch setup.
    - The delete request uses path `{ratingId}` from product `A` but body `sku = B`.
  - Failure endpoint:
    `DELETE /api/products/ratings/delete/{ratingId}`
  - Why this fails:
    It does not fail as the API contract implies. The implementation ignores `{ratingId}` and clears ratings for body `sku = B`.
  - Intentionally violated constraints:
    Path `ratingId` and body `sku` refer to different products; source code prioritizes body `sku` and ignores the path ID.

Endpoint coverage:
- Covers:
  `DELETE /api/products/ratings/delete/{ratingId}`
- Distinct meaning:
  Despite the path name, source code clears all ratings for body `sku`.

### Function 16: acknowledge add to cart

Function name:
acknowledge add to cart

Core endpoint(s):
- `POST /api/cart/add/{sku}`

Preconditions:
- An enabled user exists and can authenticate. This can be satisfied by directly inserting a `User` with `enabled = true` and an encoded password or by calling `POST /api/auth/register`, then `GET /api/auth/accountVerification/{token}` with the generated token.
- A valid bearer token exists for the enabled user. This can be satisfied by using the configured JWT generator directly in test setup or by calling `POST /api/auth/login` with that user's `username` and raw `password` and reusing the returned `accessToken`.

Successful execution:
- Result:
  Returns HTTP 200 for an authenticated request to add a SKU to the cart, but the implementation does not currently persist a cart item.
- Invocation:
  Step 1: `POST /api/cart/add/{sku}` with header `Authorization: Bearer {accessToken}` and path value `{sku}`.
- Constraints:
  `{sku}` is required by the path, but `CartService.addToCart` has all product lookup, current-user lookup, cart lookup, and cart item persistence logic commented out. Product existence is not enforced by the active implementation. The endpoint is protected even though Swagger has no security definition.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid `Authorization: Bearer {accessToken}` header is supplied. This can be produced by omitting authentication entirely, using an invalid token, or intentionally not calling `POST /api/auth/login` to obtain a token.
  - Failure endpoint:
    `POST /api/cart/add/{sku}`
  - Why this fails:
    `/api/cart/**` is not permitted by the security allowlist, so it requires authentication.
  - Intentionally violated constraints:
    The bearer token requirement is omitted or invalid.

Endpoint coverage:
- Covers:
  `POST /api/cart/add/{sku}`
- Distinct meaning:
  Acknowledges an add-to-cart request; source code shows no cart mutation.

### Auxiliary endpoints

The following endpoints are documented in Swagger but are framework or operational endpoints rather than project-specific API functions in the application controllers and services.

- `GET /actuator`: Actuator link discovery. Security config requires authentication because only `/api/auth/**`, `/api/store/catalog/**`, and `/v2/api-docs/**` are public.
- `GET /actuator/health`: Actuator health summary endpoint.
- `GET /actuator/health/{component}`: Actuator component health endpoint. `{component}` must name an actuator health component.
- `GET /actuator/health/{component}/{instance}`: Actuator component-instance health endpoint.
- `GET /actuator/info`: Actuator info endpoint.
- `GET /error`: Generated Spring Boot error endpoint.
- `HEAD /error`: Generated Spring Boot error endpoint.
- `POST /error`: Generated Spring Boot error endpoint.
- `PUT /error`: Generated Spring Boot error endpoint.
- `DELETE /error`: Generated Spring Boot error endpoint.
- `OPTIONS /error`: Generated Spring Boot error endpoint; `OPTIONS /**` is ignored by security config.
- `PATCH /error`: Generated Spring Boot error endpoint.
