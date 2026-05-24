### Function 1: register customer

Function name:
register customer

Core endpoint(s):
- `POST /register`

Preconditions:
- No account with `email = {email}` exists in `user_account`. This can be satisfied by starting from a database without that email or by deleting that account directly before the request.

Successful execution:
- Result:
  Creates an active customer account, stores a BCrypt-hashed password, creates the linked `Contacts` row from `phone` and `address`, creates a linked empty `Cart`, authenticates the new user in the current security context, and returns the created customer with `password` replaced by `hidden`.
- Invocation:
  Step 1: `POST /register` with JSON body fields `email`, `password`, `name`, `phone`, and `address`.
- Constraints:
  `email`, `name`, `phone`, and `address` are required and must satisfy their size and pattern rules. `password` must be 6 to 50 alphanumeric characters. The source requires non-empty fields for `email`, `name`, `phone`, and `address`; the OpenAPI schema shows `minLength: 0` for some of these fields, so the source validation is stricter.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No account with `email = {email}` exists, or duplicate email is not the intended failure cause.
    - The submitted JSON body contains an invalid or missing `email`, `password`, `name`, `phone`, or `address`.
  - Failure endpoint:
    `POST /register`
  - Why this fails:
    `@Valid UserDTO` rejects the request and the REST exception handler returns a validation error.
  - Intentionally violated constraints:
    The request violates DTO validation for one or more registration fields.
- Branch 2:
  - Preconditions:
    - An account with `email = {email}` already exists in `user_account`. This can be satisfied by directly inserting a `UserAccount` row or by calling `POST /register` once with that same `email` and valid registration fields.
  - Failure endpoint:
    `POST /register`
  - Why this fails:
    `UserAccountService.create` calls `findByEmail` and throws `EmailExistsException` when the email is already present.
  - Intentionally violated constraints:
    The customer email uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `POST /register`
- Distinct meaning:
  Direct customer account creation.

### Function 2: retrieve current customer profile

Function name:
retrieve current customer profile

Core endpoint(s):
- `GET /customer`

Preconditions:
- An active customer account exists with `email = {email}`, a BCrypt-compatible password for `{password}`, and linked contacts containing `phone` and `address`. This can be satisfied by directly inserting the `user_account` and `contacts` rows, or by calling `POST /register` with valid `email`, `password`, `name`, `phone`, and `address`.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`; the returned DTO hides the stored password.

Successful execution:
- Result:
  Returns the authenticated customer's `email`, `name`, `phone`, and `address`, with `password` hidden.
- Invocation:
  Step 1: `GET /customer` authenticated as `{email}`.
- Constraints:
  The source uses `Principal.getName()` to select the customer. The OpenAPI file documents an optional `name` query parameter, but the controller ignores that parameter.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` or `ROLE_ADMIN` principal is supplied for an active account.
  - Failure endpoint:
    `GET /customer`
  - Why this fails:
    The controller method is secured with `@Secured({"ROLE_USER", "ROLE_ADMIN"})`.
  - Intentionally violated constraints:
    Required authenticated customer or admin identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `GET /customer`
- Distinct meaning:
  Current principal profile lookup.

### Function 3: list products

Function name:
list products

Core endpoint(s):
- `GET /products`

Preconditions:
- None.

Successful execution:
- Result:
  Returns all catalog products as `ProductDTO` objects sorted by product id in the REST controller, including product id, name, price, availability, and distillery data.
- Invocation:
  Step 1: `GET /products`.
- Constraints:
  No customer authentication is required. Products are catalog data; the documented REST API has no product creation endpoint.

Failure or exceptional branches:
- None identified from the documented contract and implementation logic.

Endpoint coverage:
- Covers:
  `GET /products`
- Distinct meaning:
  Public product catalog listing.

### Function 4: retrieve product details

Function name:
retrieve product details

Core endpoint(s):
- `GET /products/{productId}`

Preconditions:
- A product row exists with `id = {productId}`. This can be satisfied by directly inserting a `product` row with a valid `distillery_id`, or by using an id discovered from `GET /products` because the REST API does not expose product creation.

Successful execution:
- Result:
  Returns the catalog product identified by `{productId}`.
- Invocation:
  Step 1: `GET /products/{productId}` with path value `{productId}`.
- Constraints:
  `{productId}` must identify an existing product.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product row exists with `id = {productId}`. This can be produced by choosing an id not returned by `GET /products` or by directly deleting that product row.
  - Failure endpoint:
    `GET /products/{productId}`
  - Why this fails:
    `ProductService.findById` returns empty and the controller throws `UnknownEntityException`.
  - Intentionally violated constraints:
    The path product id does not identify a stored product.

Endpoint coverage:
- Covers:
  `GET /products/{productId}`
- Distinct meaning:
  Public single-product lookup.

### Function 5: retrieve customer contacts

Function name:
retrieve customer contacts

Core endpoint(s):
- `GET /customer/contacts`

Preconditions:
- An active customer account exists with `email = {email}`, a BCrypt-compatible password for `{password}`, and a linked `Contacts` row containing `phone` and `address`. This can be satisfied by directly inserting the `user_account` and `contacts` rows, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Returns the authenticated customer's stored `phone` and `address`.
- Invocation:
  Step 1: `GET /customer/contacts` authenticated as `{email}`.
- Constraints:
  The authenticated principal selects the customer. The OpenAPI `name` query parameter is ignored by the source.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `GET /customer/contacts`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.
- Branch 2:
  - Preconditions:
    - An active account with `email = {email}` exists and can authenticate, but no linked `Contacts` row exists. This can be produced by direct database setup that inserts only the `user_account` row and intentionally does not call `POST /register`.
  - Failure endpoint:
    `GET /customer/contacts`
  - Why this fails:
    `ContactsService.getContacts` returns `null`, and `ContactsDtoAssembler.toModel` dereferences the missing contacts object.
  - Intentionally violated constraints:
    The required customer contacts state is omitted.

Endpoint coverage:
- Covers:
  `GET /customer/contacts`
- Distinct meaning:
  Current customer's contact lookup.

### Function 6: update customer contacts

Function name:
update customer contacts

Core endpoint(s):
- `PUT /customer/contacts`

Preconditions:
- An active customer account exists with `email = {email}`, a BCrypt-compatible password for `{password}`, and a linked `Contacts` row. This can be satisfied by directly inserting the `user_account` and `contacts` rows, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Replaces the authenticated customer's stored `phone` and `address`.
- Invocation:
  Step 1: `PUT /customer/contacts` authenticated as `{email}` with JSON body fields `phone` and `address`.
- Constraints:
  `phone` and `address` must satisfy `ContactsDTO` validation. The source ignores the OpenAPI `name` query parameter and uses the authenticated principal.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists with linked contacts. This can be satisfied by direct database insertion or by calling `POST /register` with valid customer fields.
    - The request body contains an invalid or missing `phone` or `address`.
  - Failure endpoint:
    `PUT /customer/contacts`
  - Why this fails:
    `@Valid ContactsDTO` rejects the body and the REST exception handler returns a validation error.
  - Intentionally violated constraints:
    The contact phone or address validation rule is violated.
- Branch 2:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `PUT /customer/contacts`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.
- Branch 3:
  - Preconditions:
    - An active account with `email = {email}` exists and can authenticate, but no linked `Contacts` row exists. This can be produced by direct database setup that inserts only the `user_account` row and intentionally does not call `POST /register`.
  - Failure endpoint:
    `PUT /customer/contacts`
  - Why this fails:
    `ContactsService.updateUserContacts` cannot find existing contacts and throws `CustomNotValidException`.
  - Intentionally violated constraints:
    The contacts row required for replacement is omitted.

Endpoint coverage:
- Covers:
  `PUT /customer/contacts`
- Distinct meaning:
  Contact replacement for the current customer.

### Function 7: view or initialize cart

Function name:
view or initialize cart

Core endpoint(s):
- `GET /customer/cart`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row, optionally with an existing linked `cart` row, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Returns the authenticated customer's cart, including items, product cost, delivery flag, delivery cost, and total cost. If no cart row exists for the account, the implementation creates one.
- Invocation:
  Step 1: `GET /customer/cart` authenticated as `{email}`.
- Constraints:
  The authenticated principal selects the cart. The OpenAPI `name` query parameter is ignored by the source.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `GET /customer/cart`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `GET /customer/cart`
- Distinct meaning:
  Cart retrieval with implementation-backed cart initialization when missing.

### Function 8: add product to cart

Function name:
add product to cart

Core endpoint(s):
- `PUT /customer/cart`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with or without a cart, or by calling `POST /register` with valid customer fields.
- An available product exists with `id = {productId}` and `available = true`. This can be satisfied by directly inserting or updating a `product` row with a valid `distillery_id`, or by calling `GET /products` and selecting a returned product whose `available` field is `true`.
- The authenticated customer's cart does not already contain a `cart_item` for `{productId}`. This can be satisfied by direct database setup or by starting from the empty cart created by `POST /register`.
- The request is authenticated as `{email}`, and the body `productId` reuses the id of the available product described above.

Successful execution:
- Result:
  Adds a new cart item for `{productId}` with the submitted positive `quantity` and returns the updated cart.
- Invocation:
  Step 1: `PUT /customer/cart` authenticated as `{email}` with JSON body `productId = {productId}` and `quantity = {quantity}`.
- Constraints:
  `productId` and `quantity` must be positive according to source validation, although the OpenAPI schema does not show minimum values. The product must exist and be available.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - No product row exists with `id = {productId}`. This can be produced by choosing an id not returned by `GET /products` or by directly deleting that product row.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    `ProductService.getProduct` throws `UnknownEntityException`.
  - Intentionally violated constraints:
    The submitted `productId` does not identify a stored product.
- Branch 2:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - A product row exists with `id = {productId}` and `available = false`. This can be satisfied by direct database setup or by calling `GET /products` and selecting a returned unavailable product.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    The request does not produce an error, but `CartService.addToCart` returns the existing cart unchanged when `product.isAvailable()` is false.
  - Intentionally violated constraints:
    The submitted product is unavailable.
- Branch 3:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - The request body contains a non-positive `productId` or non-positive `quantity`.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    `@Valid CartItemDTO` rejects the body because both numeric fields are annotated with `@Positive`.
  - Intentionally violated constraints:
    Positive numeric field validation is violated.
- Branch 4:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `PUT /customer/cart`
- Distinct meaning:
  Adds a new cart item when the product is absent from the customer's cart.

### Function 9: replace cart item quantity

Function name:
replace cart item quantity

Core endpoint(s):
- `PUT /customer/cart`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with a cart, or by calling `POST /register` with valid customer fields.
- An available product exists with `id = {productId}` and `available = true`. This can be satisfied by direct database setup, or by calling `GET /products` and selecting a returned product whose `available` field is `true`.
- The authenticated customer's cart already contains a `cart_item` for `{productId}` with an existing positive quantity. This can be satisfied by directly inserting the `cart_item` row linked to the customer's cart, or by calling `PUT /customer/cart` once with the same `productId` and an initial positive `quantity`.
- The request is authenticated as `{email}`, and the body `productId` must reuse the product id already present in the cart.

Successful execution:
- Result:
  Replaces the existing cart item's quantity with the submitted positive `quantity`; it does not increment the previous quantity.
- Invocation:
  Step 1: `PUT /customer/cart` authenticated as `{email}` with JSON body `productId = {productId}` and `quantity = {newQuantity}`.
- Constraints:
  `productId` and `newQuantity` must be positive, the product must exist, and the product must remain available. Because REST validation requires a positive quantity, the domain branch that removes an item for non-positive quantities is not reachable through this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - An available product exists with `id = {productId}`. This can be satisfied by direct database setup or by selecting it from `GET /products`.
    - The authenticated customer's cart does not contain a `cart_item` for `{productId}` because the prior add state was omitted.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    It does not perform a replacement; `Cart.update` treats the request as an add and creates a new cart item.
  - Intentionally violated constraints:
    The existing cart-item prerequisite for replacement is omitted.
- Branch 2:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - A cart item for `{productId}` already exists. This can be satisfied by direct database insertion or by an earlier `PUT /customer/cart`.
    - The replacement request body uses the same `productId` but a non-positive `quantity`.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    REST validation rejects non-positive quantities before the domain method can update or remove the item.
  - Intentionally violated constraints:
    The replacement quantity is not positive.
- Branch 3:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `PUT /customer/cart`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `PUT /customer/cart`
- Distinct meaning:
  Replaces quantity for an already-present cart item.

### Function 10: clear cart

Function name:
clear cart

Core endpoint(s):
- `DELETE /customer/cart`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with or without cart items, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Removes all items from the authenticated customer's cart and returns the cleared cart. An already empty cart also succeeds.
- Invocation:
  Step 1: `DELETE /customer/cart` authenticated as `{email}`.
- Constraints:
  The authenticated principal selects the cart. The implementation creates a missing cart before clearing it.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `DELETE /customer/cart`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `DELETE /customer/cart`
- Distinct meaning:
  Empty the current customer's cart.

### Function 11: include delivery in cart

Function name:
include delivery in cart

Core endpoint(s):
- `PUT /customer/cart/delivery`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with or without a cart, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Sets the authenticated customer's cart `deliveryIncluded` flag to `true` and returns the updated cart.
- Invocation:
  Step 1: `PUT /customer/cart/delivery?included=true` authenticated as `{email}`.
- Constraints:
  The required `included` query parameter must be parseable as boolean `true`. The source uses a query parameter and no request body, although OpenAPI marks the operation as consuming JSON.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - The request omits `included` or supplies a value that Spring cannot bind to a boolean.
  - Failure endpoint:
    `PUT /customer/cart/delivery`
  - Why this fails:
    Spring cannot bind the required `included` request parameter; the generic REST exception handler may report this as a server error rather than the explicit OpenAPI 4xx responses.
  - Intentionally violated constraints:
    The required boolean query parameter is missing or invalid.
- Branch 2:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `PUT /customer/cart/delivery`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `PUT /customer/cart/delivery`
- Distinct meaning:
  Set `deliveryIncluded` to `true`.

### Function 12: exclude delivery from cart

Function name:
exclude delivery from cart

Core endpoint(s):
- `PUT /customer/cart/delivery`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with or without a cart, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Sets the authenticated customer's cart `deliveryIncluded` flag to `false` and returns the updated cart.
- Invocation:
  Step 1: `PUT /customer/cart/delivery?included=false` authenticated as `{email}`.
- Constraints:
  The required `included` query parameter must be parseable as boolean `false`. The source uses a query parameter and no request body, although OpenAPI marks the operation as consuming JSON.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `PUT /customer/cart/delivery`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.
- Branch 2:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - The request omits `included` or supplies a value that Spring cannot bind to a boolean.
  - Failure endpoint:
    `PUT /customer/cart/delivery`
  - Why this fails:
    Spring cannot bind the required `included` request parameter; the generic REST exception handler may report this as a server error rather than the explicit OpenAPI 4xx responses.
  - Intentionally violated constraints:
    The required boolean query parameter is missing or invalid.

Endpoint coverage:
- Covers:
  `PUT /customer/cart/delivery`
- Distinct meaning:
  Set `deliveryIncluded` to `false`.

### Function 13: pay cart and create order

Function name:
pay cart and create order

Core endpoint(s):
- `POST /customer/cart/pay`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row with a cart, or by calling `POST /register` with valid customer fields.
- An available product exists with `id = {productId}` and `available = true`. This can be satisfied by direct database setup, or by calling `GET /products` and selecting a returned product whose `available` field is `true`.
- The authenticated customer's cart contains at least one item, such as a `cart_item` for `{productId}` with a positive quantity. This can be satisfied by directly inserting the cart and cart-item rows, or by calling `PUT /customer/cart` with JSON body `productId = {productId}` and a positive `quantity`.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Converts the non-empty cart into a paid order, creates a bill using `ccNumber`, copies cart items into ordered products, clears the cart, and returns the created order DTO.
- Invocation:
  Step 1: `POST /customer/cart/pay` authenticated as `{email}` with JSON body `ccNumber = {ccNumber}`.
- Constraints:
  The cart must be non-empty. `ccNumber` must satisfy both `@CreditCardNumber` and the 13-to-16 digit pattern. The response body contains generated `id`, which can be reused as `{orderId}` for `GET /customer/orders/{orderId}`. The source tries to set a `Location` header from a `self` link, but `OrderDtoAssembler` does not add that link, so the documented created response may not include a location.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists with a cart. This can be satisfied by direct database insertion or by calling `POST /register`.
    - The customer's cart is empty because no `cart_item` rows exist and no prior `PUT /customer/cart` added an item.
  - Failure endpoint:
    `POST /customer/cart/pay`
  - Why this fails:
    `OrderService.createUserOrder` calls `cart.isEmpty()` and throws `EmptyCartException`.
  - Intentionally violated constraints:
    The non-empty cart prerequisite is omitted.
- Branch 2:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - The customer's cart contains at least one item. This can be satisfied by direct database setup or by calling `PUT /customer/cart` with an available product and positive quantity.
    - The request body contains an invalid, empty, or missing `ccNumber`.
  - Failure endpoint:
    `POST /customer/cart/pay`
  - Why this fails:
    `@Valid CreditCardDTO` rejects the card number before order creation.
  - Intentionally violated constraints:
    The credit-card number validation requirement is violated.
- Branch 3:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `POST /customer/cart/pay`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `POST /customer/cart/pay`
- Distinct meaning:
  Checkout and paid order creation from the current cart.

### Function 14: list customer orders

Function name:
list customer orders

Core endpoint(s):
- `GET /customer/orders`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row, or by calling `POST /register` with valid customer fields.
- The request is authenticated as `{email}`. If the API is used to establish the account, reuse the email and plaintext password submitted to `POST /register`.

Successful execution:
- Result:
  Returns the authenticated customer's order history sorted by creation date descending. It can succeed with an empty list.
- Invocation:
  Step 1: `GET /customer/orders` authenticated as `{email}`.
- Constraints:
  The authenticated principal selects the user account. The OpenAPI `name` query parameter is ignored by the source.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `GET /customer/orders`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `GET /customer/orders`
- Distinct meaning:
  Current customer's order history listing.

### Function 15: retrieve order by id

Function name:
retrieve order by id

Core endpoint(s):
- `GET /customer/orders/{orderId}`

Preconditions:
- An active customer account exists with `email = {email}` and a BCrypt-compatible password for `{password}`. This can be satisfied by directly inserting the `user_account` row, or by calling `POST /register` with valid customer fields.
- An available product exists with `id = {productId}` and `available = true`. This can be satisfied by direct database setup, or by calling `GET /products` and selecting a returned product whose `available` field is `true`.
- A cart item for the authenticated customer has existed before order creation, such as a `cart_item` for `{productId}` with positive quantity. This can be satisfied by direct database setup before inserting the order, or by calling `PUT /customer/cart` with that product and quantity.
- An order exists with `id = {orderId}`. This can be satisfied by directly inserting linked `customer_order`, `bill`, and `ordered_product` rows, or by calling `POST /customer/cart/pay` with valid `ccNumber` after the non-empty cart is established.
- The `{orderId}` path value must identify the order described above. If the API is used to create the order, `{orderId}` is the `id` returned in the `OrderDTO` response from `POST /customer/cart/pay`.
- The request is authenticated as an active user. The OpenAPI/controller description implies customer order access, but the source does not check that the authenticated user owns `{orderId}`.

Successful execution:
- Result:
  Returns the order identified by `{orderId}`.
- Invocation:
  Step 1: `GET /customer/orders/{orderId}` authenticated as an active user with path value `{orderId}`.
- Constraints:
  `{orderId}` must exist. The implementation performs a global order id lookup with `orderDAO.findById(orderId)` and does not enforce owner scoping, despite the customer-oriented route.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An active authenticated customer exists. This can be satisfied by direct database insertion or by calling `POST /register`.
    - No order exists with `id = {orderId}`. This can be produced by choosing an id not returned by `POST /customer/cart/pay` or by directly deleting the order rows.
  - Failure endpoint:
    `GET /customer/orders/{orderId}`
  - Why this fails:
    `OrderService.getUserOrder` returns empty and the controller throws `UnknownEntityException`.
  - Intentionally violated constraints:
    The path order id does not identify a stored order.
- Branch 2:
  - Preconditions:
    - Customer A exists and can authenticate. This can be satisfied by direct database insertion or by calling `POST /register` with customer A's fields.
    - An available product exists and customer A's cart contains that product. This can be satisfied by direct database setup, or by using `GET /products` to select the product and `PUT /customer/cart` as customer A.
    - An order exists for customer A with `id = {orderId}`. This can be satisfied by direct database insertion or by calling `POST /customer/cart/pay` as customer A and capturing the returned `id`.
    - Customer B exists and can authenticate. This can be satisfied by direct database insertion or by calling `POST /register` with customer B's fields.
    - The request is authenticated as customer B while the `{orderId}` value belongs to customer A.
  - Failure endpoint:
    `GET /customer/orders/{orderId}`
  - Why this fails:
    It does not fail in the implementation. This is an implementation discrepancy: `OrderService.getUserOrder` has a TODO for user checking and calls `orderDAO.findById(orderId)` directly, so customer B can retrieve customer A's order by id.
  - Intentionally violated constraints:
    Expected owner scoping between authenticated customer and order id is violated, but not enforced by source code.
- Branch 3:
  - Preconditions:
    - No valid authenticated `ROLE_USER` principal is supplied for an active account.
  - Failure endpoint:
    `GET /customer/orders/{orderId}`
  - Why this fails:
    The controller class is secured with `@Secured({"ROLE_USER"})`.
  - Intentionally violated constraints:
    Required authenticated customer identity is omitted or invalid.

Endpoint coverage:
- Covers:
  `GET /customer/orders/{orderId}`
- Distinct meaning:
  Order lookup by id, implemented as global id lookup rather than owner-scoped customer order lookup.

Unclear or auxiliary endpoints:
None. Every OpenAPI endpoint in the current project is covered above.
