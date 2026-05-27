# Domain-Level Behavior Analysis

## Domain Summary

This service is a Spring-based e-commerce storefront API. Its main domain concepts are customer accounts, account verification tokens, catalog categories, catalog products, Elasticsearch-backed search/facet data, product ratings, and a currently non-persistent shopping cart acknowledgement endpoint.

The catalog itself is mostly read-only through the API. Categories, products, and Elasticsearch product documents must be created outside the exposed REST API, typically through direct database/index setup or fixtures. Customer registration, activation, login, product rating operations, and authenticated add-to-cart acknowledgement are exposed as API functions.

## Available Function Inventory

### Account and Authentication

- `register user`
  - Endpoint: `POST /api/auth/register`
  - Domain meaning: creates a disabled customer account, creates a verification token, and sends an activation email.

- `verify account`
  - Endpoint: `GET /api/auth/accountVerification/{token}`
  - Domain meaning: enables a registered user account using a saved verification token.

- `log in and obtain token`
  - Endpoint: `POST /api/auth/login`
  - Domain meaning: authenticates an enabled user and returns an access token for protected API calls.

### Catalog Browsing

- `list categories`
  - Endpoint: `GET /api/store/catalog/categories`
  - Domain meaning: lists available catalog category names.

- `list products`
  - Endpoint: `GET /api/store/catalog/products`
  - Domain meaning: lists all catalog products.

- `list featured products`
  - Endpoint: `GET /api/store/catalog/products/featured`
  - Domain meaning: lists products marked as featured.

- `retrieve product by SKU`
  - Endpoint: `GET /api/store/catalog/products/{sku}`
  - Domain meaning: retrieves one catalog product by SKU.

- `list products by category`
  - Endpoint: `GET /api/store/catalog/products/category/{categoryName}`
  - Domain meaning: lists products attached to a named category.

### Search and Facets

- `search catalog globally`
  - Endpoint: `POST /api/store/catalog/search`
  - Domain meaning: searches Elasticsearch products globally and returns product hits, price range, brand facets, and category facets.

- `filter category catalog`
  - Endpoint: `POST /api/store/catalog/{categoryName}/facets/filter`
  - Domain meaning: searches within a named category when `textQuery` is blank and applies attribute filters.

- `search text with category facets`
  - Endpoint: `POST /api/store/catalog/{categoryName}/facets/filter`
  - Domain meaning: searches text globally while using the named category only to choose facet definitions.

### Product Ratings

- `submit product rating`
  - Endpoint: `POST /api/products/ratings/submit`
  - Domain meaning: adds a rating to a product in MongoDB and Elasticsearch.

- `retrieve product ratings`
  - Endpoint: `GET /api/products/ratings/get/{sku}`
  - Domain meaning: retrieves ratings for a product and exposes generated rating IDs.

- `edit product rating`
  - Endpoint: `PUT /api/products/ratings/edit`
  - Domain meaning: updates an existing MongoDB product rating’s stars and review text.

- `delete product ratings`
  - Endpoint: `DELETE /api/products/ratings/delete/{ratingId}`
  - Domain meaning: clears all ratings for the body `sku`; despite the path name, it does not delete only the path `ratingId`.

### Cart

- `acknowledge add to cart`
  - Endpoint: `POST /api/cart/add/{sku}`
  - Domain meaning: returns success for an authenticated add-to-cart request, but does not persist cart state.

## Supported Business Behaviors

### Behavior 1: Register a New Customer Account

Business goal:
Create a customer account and start email-based activation.

Domain context:
Registration is the first step before a customer can authenticate and use protected features such as ratings or cart acknowledgement.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with body `username=alice`, `name=Alice Example`, `email=alice@example.com`, `password=Password123`, `confirmPassword=Password123` to create a disabled user and generate a verification token.

Optional verification workflow:
1. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) only if the generated token is available from email or direct token storage inspection.

Existing-state shortcuts:
- None for the core registration behavior.
- Direct database setup can insert a disabled `User` and matching `VerificationToken`, but that skips the API behavior and does not send the activation email.
- The `username` must not already exist.

Parameter and value bindings:
- `username` is saved on the user and later reused by login.
- `password` is encoded and persisted; the raw value must be reused for login.
- The generated verification `token` is stored in `VerificationToken` and embedded in the activation email URL.
- `name` is validated but not persisted.
- `confirmPassword` is validated as nonblank but is not compared with `password`.

Business result:
A disabled user exists with saved `email`, `username`, and encoded password. A verification token linked to that user exists. An activation email is sent.

Constraints and invariants:
- `username`, `name`, `email`, `password`, and `confirmPassword` must be nonblank.
- `email` must be email-shaped.
- `password` must be 8 to 20 characters.
- `username` uniqueness is enforced.
- Password confirmation matching is implied by the API shape but not enforced.

Failure and exceptional cases:
- Failing function: `register user`
  - Failure condition: `username` already exists.
  - Why it fails: `userRepository.existsByUsername` returns true and the controller returns HTTP 400.
  - Violated prerequisite or constraint: username uniqueness.
- Failing function: `register user`
  - Failure condition: blank required fields, invalid email, or password length outside 8 to 20 characters.
  - Why it fails: DTO validation rejects the request.
  - Violated prerequisite or constraint: registration input validation.

Implementation notes:
The implementation creates only disabled accounts. It stores no display name and does not compare password and confirmation.

### Behavior 2: Activate a Registered Account

Business goal:
Enable a previously registered customer account.

Domain context:
Login requires an enabled user. Registration alone is not sufficient for authentication.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with body `username=alice`, `name=Alice Example`, `email=alice@example.com`, `password=Password123`, `confirmPassword=Password123` to create the disabled account and token.
2. Capture `{token}` from the activation email URL or from the saved `VerificationToken`.
3. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with path `token={token}` to enable the account.

Optional verification workflow:
1. Use function `log in and obtain token` (`POST /api/auth/login`) with body `username=alice`, `password=Password123` to verify the account can authenticate.

Existing-state shortcuts:
- Step 1 can be skipped if an equivalent disabled `User` and matching `VerificationToken` already exist.
- Direct database setup may insert the user and token, or directly set `User.enabled=true`; direct enabling bypasses the activation behavior.
- The token must point to a persisted user whose `username` matches the token’s embedded user.

Parameter and value bindings:
- The verification path `token` must be the exact generated token from registration.
- The token’s embedded user `username` is used to reload and enable the persisted `User`.

Business result:
The user’s `enabled` flag is set to true. The token remains stored; the implementation does not consume or delete it.

Constraints and invariants:
- Token existence is required.
- The token-to-user relationship must resolve to a saved user.
- Tokens appear reusable because verification does not invalidate them.

Failure and exceptional cases:
- Failing function: `verify account`
  - Failure condition: unknown or fabricated `token`.
  - Why it fails: token lookup returns empty and the response body reports status `400`, message `Invalid Token`.
  - Violated prerequisite or constraint: token must be generated and saved.
- Failing function: `verify account`
  - Failure condition: token exists but its embedded username no longer identifies a user.
  - Why it fails: user lookup throws `SpringStoreException`.
  - Violated prerequisite or constraint: token must remain linked to a persisted user.

Implementation notes:
The controller returns an `ApiResponse` object directly, so an invalid token is represented in the body as status `400`; the HTTP status may still be 200 depending on Spring serialization behavior.

### Behavior 3: Authenticate as a Customer

Business goal:
Obtain a bearer token for protected customer actions.

Domain context:
Ratings and cart acknowledgement require authentication by security configuration.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with body `username=alice`, `name=Alice Example`, `email=alice@example.com`, `password=Password123`, `confirmPassword=Password123`.
2. Capture `{token}` from the activation email URL or `VerificationToken`.
3. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with `token={token}`.
4. Use function `log in and obtain token` (`POST /api/auth/login`) with body `username=alice`, `password=Password123` to obtain `accessToken`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an enabled user with a BCrypt password already exists.
- Step 3 can be skipped if the user is already enabled.
- Direct database setup must preserve the same `username` and encoded password matching the raw login password.

Parameter and value bindings:
- Login body `username` must match the registered or pre-existing user.
- Login body `password` must match the raw password corresponding to the encoded stored password.
- The response field `accessToken` is reused as `Authorization: Bearer {accessToken}` for protected functions.

Business result:
The client receives an access token and username for an enabled account.

Constraints and invariants:
- `username` and `password` must be nonblank.
- Disabled users cannot authenticate.
- Tokens are stateless JWT-style credentials generated from Spring Security authentication.

Failure and exceptional cases:
- Failing function: `log in and obtain token`
  - Failure condition: user exists but is disabled.
  - Why it fails: Spring Security rejects disabled accounts.
  - Violated prerequisite or constraint: account must be enabled.
- Failing function: `log in and obtain token`
  - Failure condition: unknown username or wrong password.
  - Why it fails: `AuthenticationManager.authenticate` rejects credentials.
  - Violated prerequisite or constraint: submitted credentials must match an enabled user.
- Failing function: `register user`
  - Failure condition: duplicate username or invalid registration fields.
  - Why it fails: username check or DTO validation.
  - Violated prerequisite or constraint: valid unique account input.

Implementation notes:
Swagger lists auth endpoints but does not define reusable bearer security metadata for protected endpoints.

### Behavior 4: Browse the Public Catalog

Business goal:
Inspect available categories and products without logging in.

Domain context:
The storefront exposes public catalog discovery endpoints.

Starting point:
Pre-existing catalog database state.

Required execution workflow:
1. Use function `list categories` (`GET /api/store/catalog/categories`) to list category names.
2. Use function `list products` (`GET /api/store/catalog/products`) to list all products.
3. Optionally as part of the same browsing workflow, use function `list featured products` (`GET /api/store/catalog/products/featured`) to view promoted products.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={skuFromProductList}` to inspect one listed product.

Existing-state shortcuts:
- No API setup function exists for categories or products.
- Direct MongoDB setup can insert `Category` and `Product` documents.
- The product `category` object should match a saved category if category-based browsing is needed.

Parameter and value bindings:
- `sku` values returned from `list products` can be reused in `retrieve product by SKU`.
- Category names returned from `list categories` can be reused in `list products by category`.
- Product availability is derived from `quantity`.

Business result:
The client receives public catalog category and product DTOs. Featured products are the subset whose DTO has `featured=true`.

Constraints and invariants:
- Catalog endpoints are public.
- Empty repositories return empty lists.
- Product availability uses color to distinguish stock, but both in-stock and out-of-stock labels are `"Out of Stock"`.

Failure and exceptional cases:
- Failing function: `retrieve product by SKU`
  - Failure condition: `sku` does not match a saved product.
  - Why it fails: `productRepository.findBySku` returns empty and `IllegalArgumentException` is thrown.
  - Violated prerequisite or constraint: SKU must exist.
- Failing function: `list products by category`
  - Failure condition: `categoryName` does not match a saved category.
  - Why it fails: category lookup throws `IllegalArgumentException("Category Not Found")`.
  - Violated prerequisite or constraint: category name must exist.

Implementation notes:
There are no API functions to create or update catalog products or categories.

### Behavior 5: View Products in a Named Category

Business goal:
Show products belonging to a selected storefront category.

Domain context:
Category pages are a standard e-commerce navigation flow.

Starting point:
Pre-existing catalog database state.

Required execution workflow:
1. Use function `list categories` (`GET /api/store/catalog/categories`) to obtain `categoryName`.
2. Use function `list products by category` (`GET /api/store/catalog/products/category/{categoryName}`) with `categoryName={categoryNameFromCategoryList}` to retrieve products in that category.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={skuFromCategoryResult}` to inspect a selected product.

Existing-state shortcuts:
- Step 1 can be skipped if the exact category name is already known.
- Direct MongoDB setup can insert the category and products.
- Products must be saved with their `category` field equal to the resolved `Category` object.

Parameter and value bindings:
- The `categoryName` returned or known from category data must be reused exactly in the path.
- Product `sku` values returned from the category listing can be reused for product detail.

Business result:
The client receives all products whose saved category matches the named category.

Constraints and invariants:
- Category matching is by `Category.name`.
- Product lookup is by resolved `Category` object, not only by category text.
- No category ownership or visibility rules are enforced.

Failure and exceptional cases:
- Failing function: `list products by category`
  - Failure condition: missing category.
  - Why it fails: `CategoryRepository.findByName` returns empty.
  - Violated prerequisite or constraint: category must exist before category browsing.

Implementation notes:
If products contain a category object that does not equal the repository-resolved category object, they may not appear even if the displayed category name is similar.

### Behavior 6: Search the Catalog Globally

Business goal:
Find products by text and inspect global facets.

Domain context:
Customers need keyword search across product names and descriptions.

Starting point:
Pre-existing Elasticsearch index state.

Required execution workflow:
1. Use function `search catalog globally` (`POST /api/store/catalog/search`) with body `textQuery="phone"`, `filters=[]` to search product `name` and `description`.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={skuFromSearchHit}` to inspect a MongoDB-backed product detail, if the same SKU exists in MongoDB.

Existing-state shortcuts:
- No API function indexes products into Elasticsearch.
- Direct Elasticsearch setup can create `product` index documents.
- Direct MongoDB setup is only needed if search hits will be cross-checked through product detail.

Parameter and value bindings:
- The body `filters` field must exist and be a list, even if empty.
- Search hit `sku` can be reused in catalog detail or rating workflows.
- Filters use `key` and `value`, but in global search they affect aggregations and min/max price, not returned hits.

Business result:
The client receives product hits, min and max price, Brand facets, and Category facets from Elasticsearch.

Constraints and invariants:
- `filters=null` is invalid in implementation.
- `from` and `to` fields exist in DTO but are unused.
- Returned hits are limited by the search source size of 16.
- Global filters are not applied as a post-filter to returned hits.

Failure and exceptional cases:
- Failing function: `search catalog globally`
  - Failure condition: body omits `filters` or sets `filters=null`.
  - Why it fails: implementation iterates over `searchQueryDto.getFilters()` without a null guard.
  - Violated prerequisite or constraint: filters must be a non-null list.
- Failing function: `retrieve product by SKU`
  - Failure condition: search hit SKU exists only in Elasticsearch, not MongoDB.
  - Why it fails: Mongo product lookup fails.
  - Violated prerequisite or constraint: SKU must exist in MongoDB for product detail.

Implementation notes:
The implementation prioritizes Elasticsearch state for search. Search and Mongo catalog state can diverge.

### Behavior 7: Filter a Category Page by Facets

Business goal:
Search within a category and narrow results by product attributes.

Domain context:
A category page with filters such as Brand, RAM, or Screen Size is a common storefront workflow.

Starting point:
Pre-existing MongoDB category state and Elasticsearch product index state.

Required execution workflow:
1. Use function `list categories` (`GET /api/store/catalog/categories`) to obtain `categoryName`.
2. Use function `filter category catalog` (`POST /api/store/catalog/{categoryName}/facets/filter`) with path `categoryName={categoryNameFromCategoryList}` and body `textQuery=""`, `filters=[{"key":"Brand","value":"Samsung"}]` to search the named category and apply attribute filters.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={skuFromFilterHit}` to inspect a returned product if MongoDB contains the same SKU.

Existing-state shortcuts:
- Step 1 can be skipped if the exact category name is already known.
- Direct MongoDB setup must provide a `Category` with `name={categoryName}` and relevant `possibleFacets`.
- Direct Elasticsearch setup must provide product documents with matching `category.name` and nested `productAttributeList`.

Parameter and value bindings:
- The path `categoryName` must match an existing MongoDB `Category.name`.
- The category’s `possibleFacets` drives facet aggregation names.
- Each filter’s `key` must match `productAttributeList.attributeName.keyword`; each `value` must match `productAttributeList.attributeValue.keyword`.
- Returned `sku` values can be reused in detail or rating operations.

Business result:
The client receives category-scoped product hits, filtered facets, and min/max prices.

Constraints and invariants:
- Blank or absent `textQuery` with a category path searches by `category.name`.
- `filters` must be non-null.
- Category must exist in MongoDB even though hits come from Elasticsearch.
- Facet definitions are category-driven.

Failure and exceptional cases:
- Failing function: `filter category catalog`
  - Failure condition: unknown `categoryName`.
  - Why it fails: `SearchService.searchWithFilters` cannot load category `possibleFacets`.
  - Violated prerequisite or constraint: category must exist.
- Failing function: `filter category catalog`
  - Failure condition: `filters=null`.
  - Why it fails: service iterates over filters without a null guard.
  - Violated prerequisite or constraint: filters must be a list.

Implementation notes:
This endpoint combines MongoDB category metadata with Elasticsearch product search. Missing `possibleFacets` can weaken facet output even when product documents exist.

### Behavior 8: Text Search Using a Category’s Facet Set

Business goal:
Search by free text while displaying facet choices configured for a selected category.

Domain context:
The same endpoint supports a different behavior when `textQuery` is nonblank.

Starting point:
Pre-existing MongoDB category state and Elasticsearch product index state.

Required execution workflow:
1. Use function `list categories` (`GET /api/store/catalog/categories`) to obtain `categoryName`.
2. Use function `search text with category facets` (`POST /api/store/catalog/{categoryName}/facets/filter`) with path `categoryName={categoryNameFromCategoryList}` and body `textQuery="watch"`, `filters=[]`.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={skuFromSearchHit}` if product detail inspection is needed.

Existing-state shortcuts:
- Step 1 can be skipped if category name is known.
- Direct MongoDB setup can create the category metadata.
- Direct Elasticsearch setup can create matching searchable product documents.

Parameter and value bindings:
- The path `categoryName` is reused only to load `possibleFacets`.
- Nonblank `textQuery` is searched against product `name` and `description`.
- `filters` can further post-filter hits by nested attributes.

Business result:
The client receives text-search hits and facets based on the named category’s configured facet names.

Constraints and invariants:
- The path category is not used to constrain hits when `textQuery` is nonblank.
- `filters` must be non-null.
- The category must exist even if the text search is effectively global.

Failure and exceptional cases:
- Failing function: `search text with category facets`
  - Failure condition: unknown category.
  - Why it fails: category lookup occurs before search.
  - Violated prerequisite or constraint: category facet configuration must exist.
- Failing function: `search text with category facets`
  - Failure condition: `filters=null`.
  - Why it fails: null filter iteration.
  - Violated prerequisite or constraint: filters must be a list.

Implementation notes:
The endpoint name suggests category filtering, but nonblank `textQuery` changes the behavior to global text search with category-selected facets.

### Behavior 9: Submit a Product Rating

Business goal:
Allow an authenticated customer to leave a review and star rating for a product.

Domain context:
Ratings enrich product detail and search-facing product data.

Starting point:
Pre-existing product state in both MongoDB and Elasticsearch.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with body `username=alice`, `name=Alice Example`, `email=alice@example.com`, `password=Password123`, `confirmPassword=Password123`.
2. Capture `{token}` from email or `VerificationToken`.
3. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with `token={token}`.
4. Use function `log in and obtain token` (`POST /api/auth/login`) with body `username=alice`, `password=Password123` and capture `accessToken`.
5. Use function `submit product rating` (`POST /api/products/ratings/submit`) with header `Authorization: Bearer {accessToken}` and body `sku={existingSku}`, `ratingStars=5`, `review="Great product"`, `userName="alice"`.

Optional verification workflow:
1. Use function `retrieve product ratings` (`GET /api/products/ratings/get/{sku}`) with header `Authorization: Bearer {accessToken}` and path `sku={existingSku}` to inspect the new rating and generated `ratingId`.

Existing-state shortcuts:
- Steps 1 to 4 can be skipped if a valid bearer token for an enabled user already exists.
- Product setup cannot be done through the API; direct MongoDB and Elasticsearch setup must create matching product documents with the same `sku`.
- The core submit step cannot be skipped.

Parameter and value bindings:
- `accessToken` from login is reused in the Authorization header.
- Body `sku` must match both MongoDB `Product.sku` and Elasticsearch `ElasticSearchProduct.sku`.
- Body `userName` is persisted as supplied and is not checked against the authenticated user.
- The generated `ratingId` is not returned by submit; it must be obtained later through `retrieve product ratings`.

Business result:
A new rating with generated ID, product ID, Elasticsearch product ID, stars, review, and user name is appended to the MongoDB product and copied to the Elasticsearch product.

Constraints and invariants:
- Authentication is required by security configuration.
- `ratingStars` must be between 1 and 5 when supplied.
- Product must exist in both stores.
- No one-rating-per-user rule is enforced.
- No ownership check binds body `userName` to the JWT principal.

Failure and exceptional cases:
- Failing function: `submit product rating`
  - Failure condition: missing or invalid bearer token.
  - Why it fails: `/api/products/ratings/**` requires authentication.
  - Violated prerequisite or constraint: protected endpoint requires `Authorization`.
- Failing function: `submit product rating`
  - Failure condition: `sku` missing from MongoDB or Elasticsearch.
  - Why it fails: both repositories are queried and either missing document raises `SpringStoreException`.
  - Violated prerequisite or constraint: product must exist in both product stores.
- Failing function: `submit product rating`
  - Failure condition: `ratingStars` below 1 or above 5.
  - Why it fails: DTO validation rejects the request.
  - Violated prerequisite or constraint: rating value range.

Implementation notes:
Swagger does not define the bearer security requirement, but implementation enforces it.

### Behavior 10: Retrieve Product Ratings

Business goal:
Inspect review history for a product and discover generated rating IDs.

Domain context:
Generated rating IDs are required for edit workflows.

Starting point:
Pre-existing MongoDB product with non-null rating list, or a rating submitted through the API.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with valid registration body.
2. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with the generated `token`.
3. Use function `log in and obtain token` (`POST /api/auth/login`) with matching credentials and capture `accessToken`.
4. Use function `retrieve product ratings` (`GET /api/products/ratings/get/{sku}`) with header `Authorization: Bearer {accessToken}` and `sku={existingSku}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Auth setup can be skipped with an existing valid bearer token.
- Rating creation can be done by `submit product rating` before retrieval, or by direct database insertion.
- The product’s `productRating` list must be non-null; an empty list works, but null fails.

Parameter and value bindings:
- The same `sku` identifies the MongoDB product whose ratings are read.
- `ratingId` values returned here can be reused by `edit product rating`.
- The same `accessToken` from login is reused in the Authorization header.

Business result:
The client receives the product’s rating DTOs, including generated `ratingId` values.

Constraints and invariants:
- Authentication is required.
- The MongoDB product must exist.
- The rating list must not be null.

Failure and exceptional cases:
- Failing function: `retrieve product ratings`
  - Failure condition: missing or invalid bearer token.
  - Why it fails: security configuration requires authentication.
  - Violated prerequisite or constraint: protected endpoint authentication.
- Failing function: `retrieve product ratings`
  - Failure condition: unknown `sku`.
  - Why it fails: MongoDB product lookup fails.
  - Violated prerequisite or constraint: product must exist.
- Failing function: `retrieve product ratings`
  - Failure condition: product `productRating=null`.
  - Why it fails: implementation calls `.stream()` on null.
  - Violated prerequisite or constraint: rating list must be initialized.

Implementation notes:
After `delete product ratings`, this read can fail because delete sets `productRating` to null rather than an empty list.

### Behavior 11: Edit an Existing Product Rating

Business goal:
Change the star value and review text of a previously created rating.

Domain context:
Customers or moderators may need to revise reviews, although ownership is not enforced.

Starting point:
Pre-existing product state in both MongoDB and Elasticsearch, plus a rating on the MongoDB product.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with valid body.
2. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with generated `token`.
3. Use function `log in and obtain token` (`POST /api/auth/login`) and capture `accessToken`.
4. Use function `submit product rating` (`POST /api/products/ratings/submit`) with header `Authorization: Bearer {accessToken}` and body `sku={existingSku}`, `ratingStars=4`, `review="Initial review"`, `userName="alice"`.
5. Use function `retrieve product ratings` (`GET /api/products/ratings/get/{sku}`) with header `Authorization: Bearer {accessToken}` and `sku={existingSku}` to capture `ratingId`.
6. Use function `edit product rating` (`PUT /api/products/ratings/edit`) with header `Authorization: Bearer {accessToken}` and body `sku={existingSku}`, `ratingId={capturedRatingId}`, `ratingStars=5`, `review="Updated review"`.

Optional verification workflow:
1. Use function `retrieve product ratings` (`GET /api/products/ratings/get/{sku}`) with the same `sku` and token to verify the changed stars and review.

Existing-state shortcuts:
- Auth setup can be skipped with a valid bearer token.
- Steps 4 and 5 can be skipped if a known `ratingId` already exists on the MongoDB product’s rating list.
- Direct database setup can insert a product rating, but the `ratingId` must match the edit body.
- Matching Elasticsearch product by the same `sku` is still required even though edit does not update its rating list.

Parameter and value bindings:
- `sku` used for submit, retrieve, and edit must be the same product SKU.
- `ratingId` captured from retrieve must be reused in edit body.
- `accessToken` is reused across protected operations.
- Replacement `ratingStars` and `review` overwrite the MongoDB rating fields.

Business result:
The MongoDB product rating identified by `ratingId` has updated stars and review text. Elasticsearch product is fetched and saved, but its rating list is not modified by edit.

Constraints and invariants:
- Authentication is required.
- Product must exist in both MongoDB and Elasticsearch.
- Rating ID must exist on the MongoDB product identified by `sku`.
- `ratingStars` must be between 1 and 5.
- Rating ownership is not checked.

Failure and exceptional cases:
- Failing function: `edit product rating`
  - Failure condition: `ratingId` does not exist on the body `sku`.
  - Why it fails: service filters the product rating list and throws `SpringStoreException`.
  - Violated prerequisite or constraint: rating ID must belong to the product.
- Failing function: `edit product rating`
  - Failure condition: missing MongoDB or Elasticsearch product for `sku`.
  - Why it fails: both stores are looked up before edit completes.
  - Violated prerequisite or constraint: product must exist in both stores.
- Failing function: `retrieve product ratings`
  - Failure condition: product rating list is null after prior deletion.
  - Why it fails: null stream access.
  - Violated prerequisite or constraint: rating list must be initialized.

Implementation notes:
The edit operation can make MongoDB and Elasticsearch rating data inconsistent because it does not mutate the Elasticsearch rating list before saving.

### Behavior 12: Clear Product Ratings

Business goal:
Remove all ratings from a product.

Domain context:
This is exposed as delete-by-rating-ID, but the implemented business behavior is product-level rating clearing.

Starting point:
Pre-existing product state in both MongoDB and Elasticsearch.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with valid body.
2. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with generated `token`.
3. Use function `log in and obtain token` (`POST /api/auth/login`) and capture `accessToken`.
4. Use function `delete product ratings` (`DELETE /api/products/ratings/delete/{ratingId}`) with header `Authorization: Bearer {accessToken}`, path `ratingId=anyValueOrKnownRatingId`, and body `sku={existingSku}`.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={existingSku}` to inspect product DTO ratings as an empty list.
2. Do not use `retrieve product ratings` immediately after deletion as the only verification because it may fail when `productRating=null`.

Existing-state shortcuts:
- Auth setup can be skipped with a valid bearer token.
- Direct MongoDB and Elasticsearch setup must provide matching products by `sku`.
- A real `ratingId` is not required by implementation, although the path requires a value.

Parameter and value bindings:
- Body `sku` controls which product is cleared.
- Path `ratingId` is ignored by implementation.
- `accessToken` is reused in the Authorization header.

Business result:
The MongoDB product and Elasticsearch product identified by body `sku` have `productRating=null`. All ratings are removed for that SKU.

Constraints and invariants:
- Authentication is required.
- Product must exist in both stores.
- The implementation does not validate that path `ratingId` exists or belongs to the body `sku`.

Failure and exceptional cases:
- Failing function: `delete product ratings`
  - Failure condition: missing or invalid bearer token.
  - Why it fails: protected endpoint requires authentication.
  - Violated prerequisite or constraint: valid Authorization header.
- Failing function: `delete product ratings`
  - Failure condition: body `sku` missing from MongoDB or Elasticsearch.
  - Why it fails: repository lookup throws `SpringStoreException`.
  - Violated prerequisite or constraint: body SKU must identify a product in both stores.
- Failing function: `delete product ratings`
  - Failure condition: path `ratingId` belongs to product A but body `sku` names product B.
  - Why it fails: it does not fail as expected; the implementation ignores `ratingId` and clears product B.
  - Violated prerequisite or constraint: API contract implies rating-specific deletion, but implementation does not enforce it.

Implementation notes:
This is a major OpenAPI/implementation discrepancy. The path says delete one rating by ID; source code clears all ratings for body `sku`.

### Behavior 13: Acknowledge Add-to-Cart Request

Business goal:
Allow an authenticated customer to send an add-to-cart action.

Domain context:
Cart interaction is expected in an e-commerce service, but this implementation only acknowledges the request.

Starting point:
No prior product state is required by the active implementation.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with valid body.
2. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with generated `token`.
3. Use function `log in and obtain token` (`POST /api/auth/login`) and capture `accessToken`.
4. Use function `acknowledge add to cart` (`POST /api/cart/add/{sku}`) with header `Authorization: Bearer {accessToken}` and path `sku={anySkuValue}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Auth setup can be skipped with a valid bearer token.
- Direct product setup is not required because the active `CartService.addToCart` does not check product existence.
- Direct cart database inspection will not show a new cart item because no mutation is performed.

Parameter and value bindings:
- `accessToken` from login is reused in the Authorization header.
- Path `sku` is accepted but not consumed by active business logic.

Business result:
The API returns HTTP 200 for an authenticated request. No cart, cart item, product relationship, quantity, or customer cart state is persisted.

Constraints and invariants:
- Authentication is required.
- Product existence is not enforced.
- No ownership, quantity, inventory, or duplicate-line handling exists.

Failure and exceptional cases:
- Failing function: `acknowledge add to cart`
  - Failure condition: missing or invalid bearer token.
  - Why it fails: `/api/cart/**` is not public.
  - Violated prerequisite or constraint: protected endpoint authentication.

Implementation notes:
The intended cart logic is commented out in `CartService`. The endpoint should not be treated as a functional cart mutation.

### Behavior 14: End-to-End Customer Review Lifecycle

Business goal:
Register, activate, authenticate, create a product review, retrieve its generated ID, edit it, and then clear product ratings.

Domain context:
This is the closest complete customer-generated-content workflow supported by the API.

Starting point:
Pre-existing product state in both MongoDB and Elasticsearch.

Required execution workflow:
1. Use function `register user` (`POST /api/auth/register`) with body `username=alice`, `name=Alice Example`, `email=alice@example.com`, `password=Password123`, `confirmPassword=Password123`.
2. Capture `{token}` from email or `VerificationToken`.
3. Use function `verify account` (`GET /api/auth/accountVerification/{token}`) with `token={token}`.
4. Use function `log in and obtain token` (`POST /api/auth/login`) with body `username=alice`, `password=Password123`; capture `accessToken`.
5. Use function `submit product rating` (`POST /api/products/ratings/submit`) with header `Authorization: Bearer {accessToken}` and body `sku={existingSku}`, `ratingStars=4`, `review="Initial review"`, `userName="alice"`.
6. Use function `retrieve product ratings` (`GET /api/products/ratings/get/{sku}`) with header `Authorization: Bearer {accessToken}` and `sku={existingSku}`; capture `ratingId`.
7. Use function `edit product rating` (`PUT /api/products/ratings/edit`) with header `Authorization: Bearer {accessToken}` and body `sku={existingSku}`, `ratingId={capturedRatingId}`, `ratingStars=5`, `review="Updated review"`.
8. Use function `delete product ratings` (`DELETE /api/products/ratings/delete/{ratingId}`) with header `Authorization: Bearer {accessToken}`, path `ratingId={capturedRatingId}`, and body `sku={existingSku}` to clear all ratings for the product.

Optional verification workflow:
1. Use function `retrieve product by SKU` (`GET /api/store/catalog/products/{sku}`) with `sku={existingSku}` to inspect the product DTO after clearing.

Existing-state shortcuts:
- Account setup can be skipped if a valid bearer token exists.
- Rating submission/retrieval can be skipped before edit only if a valid `ratingId` already exists on the product.
- Product and Elasticsearch setup must be done directly because no API creates them.

Parameter and value bindings:
- The same `username` and raw `password` bind registration to login.
- The verification `token` binds registration to activation.
- `accessToken` binds login to protected rating operations.
- The same `sku` scopes submit, retrieve, edit, and delete.
- `ratingId` generated during submit and discovered by retrieve is reused for edit and path delete, although delete ignores it.

Business result:
A user account is created and enabled. A rating is created, then the MongoDB rating is edited, then all ratings for the body SKU are cleared in both MongoDB and Elasticsearch.

Constraints and invariants:
- Product must exist in both MongoDB and Elasticsearch.
- Authentication is required for all rating functions.
- Rating ownership is not enforced.
- Delete clears all product ratings, not one rating.
- Edit does not update Elasticsearch rating content.

Failure and exceptional cases:
- Failing function: `log in and obtain token`
  - Failure condition: activation skipped.
  - Why it fails: disabled accounts cannot authenticate.
  - Violated prerequisite or constraint: user must be enabled.
- Failing function: `submit product rating`
  - Failure condition: product exists in MongoDB but not Elasticsearch, or vice versa.
  - Why it fails: both repository lookups are required.
  - Violated prerequisite or constraint: SKU must be present in both stores.
- Failing function: `edit product rating`
  - Failure condition: `ratingId` was not captured from the same product.
  - Why it fails: rating ID lookup is scoped to the body `sku`.
  - Violated prerequisite or constraint: rating ID must belong to that product.
- Failing function: `delete product ratings`
  - Failure condition: path `ratingId` and body `sku` disagree.
  - Why it fails: it does not fail; body SKU wins.
  - Violated prerequisite or constraint: expected rating-specific deletion is not enforced.

Implementation notes:
This workflow exposes the service’s strongest supported customer write path, but also highlights weak ownership, incomplete Elasticsearch synchronization, and delete semantics that differ from the URL.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Manage Catalog Products Through the API

Priority:
Critical domain gap

Expected business goal:
Administrators should be able to create, update, delete, activate, deactivate, or inventory-adjust catalog products.

Why it is unsupported:
No exposed controller function persists products. `ProductService.save` exists but is incomplete and unused by controllers.

Existing functions considered:
- `list products`: reads products only.
- `retrieve product by SKU`: reads one product only.
- `list featured products`: reads featured products only.
- `list products by category`: reads by category only.
- `submit product rating`: mutates only product ratings, not product catalog data.

Missing capability:
Product create/update/delete endpoints, SKU uniqueness validation, inventory updates, featured flag management, and MongoDB/Elasticsearch synchronization.

Proof that function composition is insufficient:
Chaining read, search, and rating functions cannot create a new product, change product price, change inventory, or remove a catalog item. Direct database insertion is possible but is not an API behavior and does not guarantee Elasticsearch synchronization.

Evidence from existing functions/source:
Catalog controller exposes only read/search endpoints. `AdminController` is empty.

Business impact:
The storefront cannot be administered through its own REST API.

### Missing Behavior 2: Manage Categories Through the API

Priority:
Critical domain gap

Expected business goal:
Administrators should be able to create, update, rename, delete, and configure category facet definitions.

Why it is unsupported:
There is only `list categories`; no category mutation endpoint exists.

Existing functions considered:
- `list categories`: lists category names.
- `list products by category`: requires an existing category.
- `filter category catalog`: requires existing category `possibleFacets`.
- `search text with category facets`: requires existing category `possibleFacets`.

Missing capability:
Category CRUD, category rename, facet configuration management, and safe category deletion/reassignment rules.

Proof that function composition is insufficient:
Existing functions can consume category state but cannot create or modify it. Search facet behavior depends on `possibleFacets`, which cannot be changed through the API.

Evidence from existing functions/source:
`CategoryRepository` supports persistence, but `CatalogController` only reads categories.

Business impact:
Category navigation and faceted search depend on out-of-band database maintenance.

### Missing Behavior 3: Functional Shopping Cart Management

Priority:
Critical domain gap

Expected business goal:
Customers should be able to add products to a cart, view cart contents, update quantities, remove items, and retain cart state.

Why it is unsupported:
`acknowledge add to cart` returns success but active service logic is empty.

Existing functions considered:
- `acknowledge add to cart`: authenticates and returns OK, but does not persist cart items.
- `retrieve product by SKU`: can inspect product existence but is not used by cart logic.
- `log in and obtain token`: supplies authentication only.

Missing capability:
Cart item persistence, product existence validation, quantity handling, cart retrieval, item removal, and customer-cart ownership.

Proof that function composition is insufficient:
No function writes cart state. Repeating `acknowledge add to cart` produces no persisted cart item and no API can list cart contents.

Evidence from existing functions/source:
`CartService.addToCart` contains only commented-out logic.

Business impact:
A core e-commerce shopping workflow is nonfunctional.

### Missing Behavior 4: Checkout and Order Creation

Priority:
Critical domain gap

Expected business goal:
Customers should be able to convert a cart into an order, reserve or decrement inventory, and receive order confirmation.

Why it is unsupported:
There are no order, payment, checkout, shipping, or inventory mutation endpoints.

Existing functions considered:
- `acknowledge add to cart`: does not persist cart state.
- `list products`: exposes products but does not reserve inventory.
- `log in and obtain token`: authenticates only.

Missing capability:
Order model, checkout endpoint, payment flow, shipping data, inventory decrement, transactional order creation.

Proof that function composition is insufficient:
Without persisted cart state or order endpoints, no sequence of existing functions can create an order or alter inventory.

Evidence from existing functions/source:
No order controller, service, model, or repository is present in the exposed API.

Business impact:
The service behaves as a catalog and review API, not a complete commerce transaction system.

### Missing Behavior 5: Delete One Specific Rating Safely

Priority:
Important robustness gap

Expected business goal:
Delete exactly one rating by `ratingId` for a product.

Why it is unsupported:
`delete product ratings` ignores path `ratingId` and clears all ratings for body `sku`.

Existing functions considered:
- `retrieve product ratings`: can discover rating IDs.
- `delete product ratings`: accepts `ratingId` path but does not use it.
- `edit product rating`: validates rating ID but only edits.

Missing capability:
Single-rating deletion that checks the rating belongs to the product and removes only that rating.

Proof that function composition is insufficient:
Retrieving a rating ID before delete does not matter because delete ignores it. Delete-and-recreate is not equivalent because generated IDs, ordering, timestamps if later added, and other users’ ratings cannot be preserved safely.

Evidence from existing functions/source:
`ProductRatingController.deleteRating` receives path `ratingId`, but `ProductService.deleteProductRating` only uses body `sku` and sets `productRating=null`.

Business impact:
A user trying to delete one review can accidentally erase all reviews for a product.

### Missing Behavior 6: Enforce Rating Ownership

Priority:
Important robustness gap

Expected business goal:
Only the authenticated user who authored a rating, or an authorized moderator, should edit or delete it.

Why it is unsupported:
Rating body `userName` is trusted and not compared with the authenticated principal.

Existing functions considered:
- `submit product rating`: saves body `userName`.
- `edit product rating`: edits by `sku` and `ratingId`, no user check.
- `delete product ratings`: clears by body `sku`, no user check.
- `log in and obtain token`: authenticates user but protected services do not bind principal to rating owner.

Missing capability:
Ownership checks against JWT principal, moderator roles, and rating-user relationship enforcement.

Proof that function composition is insufficient:
Authentication provides identity, but no rating function consumes that identity for authorization decisions.

Evidence from existing functions/source:
`ProductService` methods accept only DTO values and do not use `AuthService.getCurrentUser`.

Business impact:
Any authenticated user can submit ratings under another username, edit another user’s rating, or clear all product ratings.

### Missing Behavior 7: Keep MongoDB and Elasticsearch Product Ratings Consistent

Priority:
Important robustness gap

Expected business goal:
Rating creation, edit, and deletion should leave MongoDB and Elasticsearch with equivalent rating state.

Why it is unsupported:
Submit copies ratings to both stores, delete clears both stores, but edit mutates only the MongoDB rating object and saves the Elasticsearch product without changing its rating list.

Existing functions considered:
- `submit product rating`: writes both stores.
- `edit product rating`: updates MongoDB rating but not Elasticsearch rating list.
- `delete product ratings`: clears both stores.
- `search catalog globally`: reads from Elasticsearch and can expose stale data.

Missing capability:
Consistent update logic or a synchronization/reindex endpoint.

Proof that function composition is insufficient:
After edit, there is no function that updates the Elasticsearch rating to match the MongoDB edit except clearing all ratings or direct index repair.

Evidence from existing functions/source:
`editProductRating` changes `productRating` from MongoDB list, then calls `productSearchRepository.save(elasticSearchProduct)` without modifying `elasticSearchProduct.productRating`.

Business impact:
Search results and product detail can disagree about ratings.

### Missing Behavior 8: Account Recovery, Token Resend, and Token Expiration

Priority:
Important robustness gap

Expected business goal:
Users should be able to resend activation emails, recover passwords, and rely on expiring one-time verification tokens.

Why it is unsupported:
Only registration, verification, and login exist.

Existing functions considered:
- `register user`: generates initial token.
- `verify account`: enables user but does not consume or expire token.
- `log in and obtain token`: requires enabled account.

Missing capability:
Resend verification, password reset request/confirm, token expiry, token consumption, and duplicate-token invalidation.

Proof that function composition is insufficient:
A disabled user with lost email cannot request a new token through the API. A used token remains valid because verification does not delete or mark it consumed.

Evidence from existing functions/source:
`AuthService.verifyAccount` enables the user but does not update or delete the `VerificationToken`.

Business impact:
Account lifecycle is fragile and security controls around tokens are weak.

### Missing Behavior 9: Accurate Product Availability and Inventory Rules

Priority:
Important robustness gap

Expected business goal:
Customers should see correct in-stock/out-of-stock status and cart/checkout should enforce inventory.

Why it is unsupported:
Availability label is incorrect for in-stock products, and no cart or checkout inventory enforcement exists.

Existing functions considered:
- `list products`: derives availability from `quantity`.
- `retrieve product by SKU`: returns availability.
- `acknowledge add to cart`: does not check product or quantity.

Missing capability:
Correct availability label, inventory reservation/decrement, and out-of-stock blocking.

Proof that function composition is insufficient:
Reading quantity-derived DTOs cannot enforce stock rules. Add-to-cart does not inspect SKU or quantity.

Evidence from existing functions/source:
`inStock()` returns `new ProductAvailability("Out of Stock", "forestgreen")`.

Business impact:
Customers may see misleading availability and can receive successful cart acknowledgements for nonexistent or unavailable SKUs.

### Missing Behavior 10: Search Index Creation and Reindexing

Priority:
API ergonomics gap

Expected business goal:
Administrators should be able to index products, rebuild search data, or sync MongoDB products into Elasticsearch.

Why it is unsupported:
Search requires Elasticsearch documents, but no endpoint creates or refreshes them.

Existing functions considered:
- `search catalog globally`: reads Elasticsearch.
- `filter category catalog`: reads Elasticsearch plus MongoDB category metadata.
- `submit product rating`: updates existing Elasticsearch product only if found.

Missing capability:
Index product, reindex all products, update indexed product fields, and reconcile missing Elasticsearch documents.

Proof that function composition is insufficient:
If a MongoDB product has no Elasticsearch counterpart, search cannot find it and rating submit/edit/delete fail for that SKU. No existing API can create the missing Elasticsearch document.

Evidence from existing functions/source:
`ProductSearchRepository.findBySku` is required for rating writes; no controller exposes save/index operations.

Business impact:
Catalog and search can drift, and API clients cannot repair the drift.

## Cross-Behavior Observations

- The API is read-heavy for catalog data and lacks administrative catalog mutation endpoints.
- Authentication is implemented through Spring Security, but Swagger does not clearly model bearer-token requirements for ratings and cart endpoints.
- Account verification tokens are not returned by registration, not expired, and not consumed.
- Rating operations have weak ownership controls; authenticated identity is not bound to `userName`.
- Rating deletion is product-wide despite a rating-specific path.
- MongoDB and Elasticsearch can diverge, especially after rating edits.
- Global search filters influence aggregations but not returned hits.
- Category-facet search changes meaning depending on whether `textQuery` is blank.
- Product availability has an implementation bug: in-stock products still carry the text `"Out of Stock"`.
- Add-to-cart is currently an acknowledgement endpoint with no domain mutation.

## Coverage Summary

Supported domain areas:
Customer registration, account activation, login, public catalog reads, category/product browsing, Elasticsearch search/facets, authenticated rating submission/retrieval/editing/clearing, and authenticated cart request acknowledgement.

Partially supported domain areas:
Product reviews are partially supported but lack ownership, single-rating deletion, and full search-index consistency. Search is partially supported but depends on out-of-band indexing and has filter semantics gaps. Cart is only superficially supported.

Unsupported domain areas:
Catalog administration, category management, real cart persistence, checkout/orders, inventory enforcement, product indexing/reindexing, account recovery, token lifecycle hardening, and safe moderation workflows.