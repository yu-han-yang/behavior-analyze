### Function 1: get application configuration

Function name:
get application configuration

Core endpoint(s):
- `GET /config`

Preconditions:
- None. The endpoint reads the service configuration bean and does not require token contract state.

Successful execution:
- Result:
  Returns the configured Ethereum/Quorum node endpoint and transaction sender address.
- Invocation:
  Step 1: `GET /config`
- Constraints:
  The response contains `nodeEndpoint` and `fromAddress` from `NodeConfiguration`.

Failure or exceptional branches:
- None visible in the implementation. Swagger lists generic `401`, `403`, and `404` responses, but the controller does not implement endpoint-specific authentication, authorization, or missing-resource branches.

Endpoint coverage:
- Covers:
  `GET /config`
- Distinct meaning:
  Reads service configuration rather than token contract state.

### Function 2: deploy ERC-20 token

Function name:
deploy ERC-20 token

Core endpoint(s):
- `POST /deploy`

Preconditions:
- The configured Ethereum/Quorum node is reachable and the configured `fromAddress` is usable as the transaction sender. This is normally satisfied by node configuration rather than another REST endpoint; direct setup means providing an unlocked sender account and reachable node outside this API.

Successful execution:
- Result:
  Deploys a new `HumanStandardToken` contract and returns the hex-encoded contract address. The constructor mints `initialAmount` to the configured transaction sender and stores `tokenName`, `decimalUnits`, and `tokenSymbol`.
- Invocation:
  Step 1: `POST /deploy` with JSON body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`, and optional `privateFor` header containing a comma-separated Quorum private recipient list
- Constraints:
  `initialAmount` must be encodable as Solidity `uint256`. `decimalUnits` must be encodable as Solidity `uint8`. `tokenName` and `tokenSymbol` are passed directly to the generated Web3j deployment wrapper.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The deployment request body is missing a required constructor value or contains a value that cannot be ABI-encoded, such as a negative `uint256` or `decimalUnits` outside the Solidity `uint8` range.
  - Failure endpoint:
    `POST /deploy`
  - Why this fails:
    The controller passes request fields directly to `HumanStandardToken.deploy(...)`; invalid or missing constructor values prevent a valid deployment transaction from being created or sent.
  - Intentionally violated constraints:
    Required constructor fields or Solidity numeric encoding requirements are violated.

Endpoint coverage:
- Covers:
  `POST /deploy`
- Distinct meaning:
  Creates the deployed token contract state used by contract-scoped endpoints.

### Function 3: retrieve token name

Function name:
retrieve token name

Core endpoint(s):
- `GET /{contractAddress}/name`

Preconditions:
- A deployed `HumanStandardToken` contract exists with a known name value. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `GET /{contractAddress}/name` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Returns the `name` stored on the deployed token contract.
- Invocation:
  Step 1: `GET /{contractAddress}/name` with `{contractAddress}` set to the deployed token contract address
- Constraints:
  The supplied address must load as a token contract exposing `name()`. The returned value equals the deployment `tokenName`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/name`
  - Why this fails:
    The service loads the supplied address and calls `name()`; a missing or non-token contract cannot return the expected token name.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/name`
- Distinct meaning:
  Reads the token's human-readable name.

### Function 4: retrieve token symbol

Function name:
retrieve token symbol

Core endpoint(s):
- `GET /{contractAddress}/symbol`

Preconditions:
- A deployed `HumanStandardToken` contract exists with a known symbol value. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `GET /{contractAddress}/symbol` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Returns the `symbol` stored on the deployed token contract.
- Invocation:
  Step 1: `GET /{contractAddress}/symbol` with `{contractAddress}` set to the deployed token contract address
- Constraints:
  The supplied address must load as a token contract exposing `symbol()`. The returned value equals the deployment `tokenSymbol`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/symbol`
  - Why this fails:
    The service calls `symbol()` on the supplied contract address and expects a token string response.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/symbol`
- Distinct meaning:
  Reads the token's display symbol.

### Function 5: retrieve token decimal precision

Function name:
retrieve token decimal precision

Core endpoint(s):
- `GET /{contractAddress}/decimals`

Preconditions:
- A deployed `HumanStandardToken` contract exists with a known decimal precision. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `GET /{contractAddress}/decimals` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Returns the `decimals` value configured for the deployed token contract.
- Invocation:
  Step 1: `GET /{contractAddress}/decimals` with `{contractAddress}` set to the deployed token contract address
- Constraints:
  The supplied address must load as a token contract exposing `decimals()`. The returned value equals the deployment `decimalUnits`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/decimals`
  - Why this fails:
    The service calls `decimals()` on the supplied address and expects a Solidity `uint8` token value.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/decimals`
- Distinct meaning:
  Reads token display precision.

### Function 6: retrieve token version

Function name:
retrieve token version

Core endpoint(s):
- `GET /{contractAddress}/version`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `GET /{contractAddress}/version` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Returns the token contract's version string.
- Invocation:
  Step 1: `GET /{contractAddress}/version` with `{contractAddress}` set to the deployed token contract address
- Constraints:
  The supplied address must load as a token contract exposing `version()`. The Solidity contract initializes `version` to `H0.1`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/version`
  - Why this fails:
    The service calls `version()` on the supplied address and expects a token version string.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/version`
- Distinct meaning:
  Reads the token contract version.

### Function 7: retrieve total token supply

Function name:
retrieve total token supply

Core endpoint(s):
- `GET /{contractAddress}/totalSupply`

Preconditions:
- A deployed `HumanStandardToken` contract exists with a known total supply. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `GET /{contractAddress}/totalSupply` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Returns the deployed token contract's total supply.
- Invocation:
  Step 1: `GET /{contractAddress}/totalSupply` with `{contractAddress}` set to the deployed token contract address
- Constraints:
  The supplied address must load as a token contract exposing `totalSupply()`. Immediately after deployment, the returned supply equals the deployment `initialAmount`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/totalSupply`
  - Why this fails:
    The service calls `totalSupply()` on the supplied address and expects a token integer response.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/totalSupply`
- Distinct meaning:
  Reads total minted supply.

### Function 8: retrieve address token balance

Function name:
retrieve address token balance

Core endpoint(s):
- `GET /{contractAddress}/balanceOf/{ownerAddress}`

Preconditions:
- The service configuration has a known transaction sender address. This can be satisfied by direct configuration of `NodeConfiguration.fromAddress` or by calling `GET /config` and reading response field `fromAddress`.
- A deployed `HumanStandardToken` contract exists with token balances. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`; deployment assigns `initialAmount` to the configured `fromAddress`.
- The `{contractAddress}` used by `GET /{contractAddress}/balanceOf/{ownerAddress}` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.
- For a nonzero balance read immediately after deployment, `{ownerAddress}` must equal the configured `fromAddress` returned by `GET /config`.

Successful execution:
- Result:
  Returns the token balance of `{ownerAddress}` for the deployed token contract.
- Invocation:
  Step 1: `GET /{contractAddress}/balanceOf/{ownerAddress}` with `{contractAddress}` set to the deployed token contract address and `{ownerAddress}` set to the queried address
- Constraints:
  The supplied contract address must load as a token contract exposing `balanceOf(address)`. Any ABI-valid address can be queried; an address with no tokens returns `0`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/balanceOf/{ownerAddress}`
  - Why this fails:
    The service calls `balanceOf(ownerAddress)` on the supplied contract address and expects a token balance response.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.
- Branch 2:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - `{ownerAddress}` is an address with no token balance. This can be satisfied by directly setting no balance for that address or by querying an address that is not the deployment `fromAddress` and has not received a transfer.
  - Failure endpoint:
    `GET /{contractAddress}/balanceOf/{ownerAddress}`
  - Why this fails:
    The endpoint itself succeeds, but it returns `0` because the queried address has no balance.
  - Intentionally violated constraints:
    A funded owner address was not used.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/balanceOf/{ownerAddress}`
- Distinct meaning:
  Reads one address's token balance.

### Function 9: create spending approval

Function name:
create spending approval

Core endpoint(s):
- `POST /{contractAddress}/approve`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `POST /{contractAddress}/approve` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Grants `{spender}` an allowance of `{value}` tokens from the configured transaction sender and returns a transaction hash with the emitted `Approval` event.
- Invocation:
  Step 1: `POST /{contractAddress}/approve` with `{contractAddress}` set to the deployed token contract address, JSON body fields `spender` and `value`, and optional `privateFor` header
- Constraints:
  `spender` must be an ABI-valid address. `value` must be encodable as Solidity `uint256`. The event response maps the emitted value with `longValueExact()`, so values outside Java `long` range can fail during response mapping.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `POST /{contractAddress}/approve`
  - Why this fails:
    The service tries to submit `approve(spender,value)` to an address that is not the deployed token.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.
- Branch 2:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - The approval request has a missing `spender`, invalid address data, missing or invalid `value`, negative `value`, or a `value` whose emitted event cannot be represented as Java `long`.
  - Failure endpoint:
    `POST /{contractAddress}/approve`
  - Why this fails:
    Invalid request data breaks address encoding, Solidity `uint256` encoding, or the REST event mapping performed by `ApprovalEventResponse`.
  - Intentionally violated constraints:
    Request body and response event numeric constraints are violated.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approve`
- Distinct meaning:
  Creates an allowance for a spender from the configured transaction sender.

### Function 10: replace spending approval

Function name:
replace spending approval

Core endpoint(s):
- `POST /{contractAddress}/approve`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The same `{spender}` already has an allowance from the configured transaction sender on the deployed contract. This can be satisfied by directly setting `allowed[fromAddress][spender]` in equivalent contract state or by calling `POST /{contractAddress}/approve` once with the same `{contractAddress}`, body field `spender={spender}`, and an earlier `value`.
- The `{contractAddress}` used by the replacement approval must identify the same deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`; if the API is used to establish the prior allowance, the same address must be reused.

Successful execution:
- Result:
  Replaces the existing allowance for `{spender}` with a new total allowance.
- Invocation:
  Step 1: `POST /{contractAddress}/approve` with the same `{contractAddress}` and `spender`, a new `value`, and optional `privateFor` header
- Constraints:
  The Solidity `approve` function overwrites `allowed[msg.sender][spender]`; it does not add to the prior allowance. Request encoding and Java `long` event mapping constraints are the same as for approval creation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - No prior allowance exists for the configured transaction sender and `{spender}`. This can be produced by directly leaving `allowed[fromAddress][spender]` unset or zero, or by intentionally not calling `POST /{contractAddress}/approve` before the replacement request.
  - Failure endpoint:
    `POST /{contractAddress}/approve`
  - Why this fails:
    The REST call can still succeed, but the endpoint-level effect is allowance creation rather than replacement because no existing allowance was present.
  - Intentionally violated constraints:
    The prerequisite existing allowance for the same owner and spender was omitted.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approve`
- Distinct meaning:
  Uses the approval endpoint to update an existing allowance by overwriting it.

### Function 11: clear spending approval

Function name:
clear spending approval

Core endpoint(s):
- `POST /{contractAddress}/approve`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The same `{spender}` has a prior nonzero allowance from the configured transaction sender on the deployed contract. This can be satisfied by directly setting `allowed[fromAddress][spender]` to a nonzero value in equivalent contract state or by calling `POST /{contractAddress}/approve` once with the same `{contractAddress}`, body field `spender={spender}`, and nonzero `value`.
- The `{contractAddress}` used by the clearing approval must identify the same deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`; if the API is used to establish the prior allowance, the same address must be reused.

Successful execution:
- Result:
  Clears the spender's allowance by setting it to `0`.
- Invocation:
  Step 1: `POST /{contractAddress}/approve` with the same `{contractAddress}` and `spender`, JSON body field `value=0`, and optional `privateFor` header
- Constraints:
  The endpoint stores zero for `allowed[msg.sender][spender]` and emits an `Approval` event. The spender address must still be ABI-valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - No prior nonzero allowance exists for the configured transaction sender and `{spender}`. This can be produced by directly setting the allowance to zero or leaving it unset, or by intentionally not calling `POST /{contractAddress}/approve` with a nonzero value before the clearing request.
  - Failure endpoint:
    `POST /{contractAddress}/approve`
  - Why this fails:
    The endpoint can still store zero and emit an event, but it does not clear a previously usable allowance.
  - Intentionally violated constraints:
    The prerequisite nonzero allowance for the same owner and spender was omitted.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approve`
- Distinct meaning:
  Uses the approval endpoint as an allowance revocation capability.

### Function 12: retrieve spending allowance

Function name:
retrieve spending allowance

Core endpoint(s):
- `GET /{contractAddress}/allowance`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- An allowance exists for `{ownerAddress}` and `{spenderAddress}` when a nonzero allowance read is required. This can be satisfied by directly setting `allowed[ownerAddress][spenderAddress]` in equivalent contract state or by calling `POST /{contractAddress}/approve` with body fields `spender={spenderAddress}` and `value={value}` from the configured sender, then using the emitted Approval event `owner` as `{ownerAddress}`.
- The `{contractAddress}` used by `GET /{contractAddress}/allowance` must identify the deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.
- If the API is used to establish the allowance, query parameter `ownerAddress` must equal the `owner` from the `POST /{contractAddress}/approve` response event, and query parameter `spenderAddress` must equal the approval request body field `spender`.

Successful execution:
- Result:
  Returns the allowance that `{spenderAddress}` may spend from `{ownerAddress}` for the deployed token.
- Invocation:
  Step 1: `GET /{contractAddress}/allowance` with query parameters `ownerAddress={ownerAddress}` and `spenderAddress={spenderAddress}`
- Constraints:
  The supplied contract address must load as a token contract exposing `allowance(address,address)`. Query values must be ABI-valid addresses.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `GET /{contractAddress}/allowance`
  - Why this fails:
    The service calls `allowance(ownerAddress,spenderAddress)` on an address that is not the deployed token.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.
- Branch 2:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - No allowance exists for the queried `{ownerAddress}` and `{spenderAddress}` pair. This can be produced by directly leaving `allowed[ownerAddress][spenderAddress]` unset or zero, or by intentionally not calling `POST /{contractAddress}/approve` for that owner/spender pair.
  - Failure endpoint:
    `GET /{contractAddress}/allowance`
  - Why this fails:
    The endpoint itself succeeds, but it returns `0` because the approval state was never created for the queried pair.
  - Intentionally violated constraints:
    The required owner/spender allowance state was omitted.

Endpoint coverage:
- Covers:
  `GET /{contractAddress}/allowance`
- Distinct meaning:
  Reads ERC-20 allowance state for one owner/spender pair.

### Function 13: transfer owned tokens

Function name:
transfer owned tokens

Core endpoint(s):
- `POST /{contractAddress}/transfer`

Preconditions:
- A deployed `HumanStandardToken` contract exists with sufficient balance assigned to the configured transaction sender. This can be satisfied by directly preparing equivalent contract state on Ethereum/Quorum or by calling `POST /deploy` with `initialAmount` greater than or equal to the transfer `value`, because deployment mints `initialAmount` to the configured sender.
- The `{contractAddress}` used by `POST /{contractAddress}/transfer` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.

Successful execution:
- Result:
  Transfers tokens from the configured transaction sender to `{to}` and returns a transaction hash with a `Transfer` event.
- Invocation:
  Step 1: `POST /{contractAddress}/transfer` with `{contractAddress}` set to the deployed token contract address, JSON body fields `to` and `value`, and optional `privateFor` header
- Constraints:
  `to` must be an ABI-valid address. `value` must be encodable as Solidity `uint256`, greater than `0`, and no greater than the configured sender's balance. The event response maps the emitted value with `longValueExact()`, so values outside Java `long` range can fail during response mapping.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - The configured transaction sender's token balance is less than requested `value`, or the requested `value` is `0`. This can be produced by deploying with insufficient `initialAmount`, transferring away tokens beforehand, direct contract state setup, or sending `value=0`.
  - Failure endpoint:
    `POST /{contractAddress}/transfer`
  - Why this fails:
    Solidity `transfer` returns `false` when `balances[msg.sender] < value` or `value <= 0`. The REST response can still contain a transaction hash, but no `Transfer` event is present and balances do not change.
  - Intentionally violated constraints:
    The requested transfer value is zero or exceeds the sender balance.
- Branch 2:
  - Preconditions:
    - No deployed `HumanStandardToken` exists at `{contractAddress}`. This can be produced by using an unrelated address, using a malformed address, starting without deployment, or intentionally not calling `POST /deploy`.
  - Failure endpoint:
    `POST /{contractAddress}/transfer`
  - Why this fails:
    The service submits `transfer(to,value)` to an address that is not the deployed token.
  - Intentionally violated constraints:
    The required deployed token contract address was omitted or not reused.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/transfer`
- Distinct meaning:
  Moves tokens owned by the configured service account.

### Function 14: transfer approved tokens from an owner

Function name:
transfer approved tokens from an owner

Core endpoint(s):
- `POST /{contractAddress}/transferFrom`

Preconditions:
- The service configuration has a known transaction sender address. This can be satisfied by direct configuration of `NodeConfiguration.fromAddress` or by calling `GET /config` and reading response field `fromAddress`.
- A deployed `HumanStandardToken` contract exists with sufficient token balance assigned to `{from}`. This can be satisfied by directly preparing equivalent contract state on Ethereum/Quorum or by calling `POST /deploy`; with only documented endpoints, `{from}` should be the configured `fromAddress` because deployment mints `initialAmount` to that address.
- The configured transaction sender has a sufficient allowance from `{from}` on the deployed contract. This can be satisfied by directly setting `allowed[from][fromAddress]` in equivalent contract state or by calling `POST /{contractAddress}/approve` with body fields `spender={fromAddress}` and `value` greater than or equal to the transfer amount.
- The `{contractAddress}` used by `POST /{contractAddress}/transferFrom` must identify the same deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`; if the API is used to establish the allowance, the same address must be reused.
- Swagger omits `TransferFromRequest.value`, but the controller requires body fields `from`, `to`, and `value`.

Successful execution:
- Result:
  Transfers tokens from `{from}` to `{to}` using allowance granted to the configured transaction sender and returns a transaction hash with a `Transfer` event.
- Invocation:
  Step 1: `POST /{contractAddress}/transferFrom` with JSON body fields `from`, `to`, and `value`, plus optional `privateFor` header
- Constraints:
  The configured transaction sender is the Solidity `msg.sender`. `from` and `to` must be ABI-valid addresses. `value` must be encodable as Solidity `uint256`, greater than `0`, no greater than `{from}` balance, and no greater than `allowed[from][msg.sender]`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The service configuration has a known transaction sender address. This can be satisfied by direct configuration or by calling `GET /config`.
    - A deployed token contract exists at `{contractAddress}` with sufficient balance for `{from}`. This can be satisfied by direct contract setup or by calling `POST /deploy` with a sufficient `initialAmount`.
    - An approval exists on the token contract, but it grants allowance to a spender different from the configured transaction sender. This can be produced by direct contract setup or by calling `POST /{contractAddress}/approve` with `spender` not equal to the `fromAddress` returned by `GET /config`.
  - Failure endpoint:
    `POST /{contractAddress}/transferFrom`
  - Why this fails:
    `transferFrom` checks `allowed[_from][msg.sender]`. If the approval was granted to a different spender, the configured transaction sender has insufficient allowance. The contract returns `false`, the REST response has a transaction hash but no `Transfer` event, and balances remain unchanged.
  - Intentionally violated constraints:
    The approved spender does not match the configured transaction sender used by the failing request.
- Branch 2:
  - Preconditions:
    - The service configuration has a known transaction sender address. This can be satisfied by direct configuration of `NodeConfiguration.fromAddress` or by calling `GET /config`.
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - An allowance exists or is intended for the configured transaction sender. This can be satisfied by direct contract setup or by calling `POST /{contractAddress}/approve` with body field `spender` equal to the configured `fromAddress`.
    - The requested transfer value is `0`, exceeds `{from}` balance, or exceeds `allowed[from][configuredSender]`. This can be produced by direct contract setup, insufficient deployment `initialAmount`, an insufficient approval value from `POST /{contractAddress}/approve`, or `value=0`.
  - Failure endpoint:
    `POST /{contractAddress}/transferFrom`
  - Why this fails:
    Solidity requires sufficient owner balance, sufficient allowance for `msg.sender`, and `value > 0`; otherwise it returns `false` and emits no `Transfer` event.
  - Intentionally violated constraints:
    The transfer value is invalid relative to owner balance or allowance.
- Branch 3:
  - Preconditions:
    - The service configuration has a known transaction sender address. This can be satisfied by direct configuration of `NodeConfiguration.fromAddress` or by calling `GET /config`.
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - An allowance exists or is intended for the configured transaction sender. This can be satisfied by direct contract setup or by calling `POST /{contractAddress}/approve` with body field `spender` equal to the configured `fromAddress`.
    - The failing request follows Swagger literally and includes only `from` and `to`, omitting the source-required body field `value`.
  - Failure endpoint:
    `POST /{contractAddress}/transferFrom`
  - Why this fails:
    Swagger defines `TransferFromRequest` with only `from` and `to`, but the controller reads `transferFromRequest.getValue()` and passes it to Web3j.
  - Intentionally violated constraints:
    The implementation-required `value` body field is missing.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/transferFrom`
- Distinct meaning:
  Moves tokens using allowance rather than direct ownership.

### Function 15: create approval and notify spender

Function name:
create approval and notify spender

Core endpoint(s):
- `POST /{contractAddress}/approveAndCall`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The `{contractAddress}` used by `POST /{contractAddress}/approveAndCall` must identify that deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`.
- The `{spender}` address must be able to accept the approval notification call. No documented REST endpoint creates that receiver contract; direct setup must deploy or provide a compatible spender contract/address outside this API.
- Swagger defines `ApproveAndCallRequest` as an empty object, but the controller requires body fields `spender`, `value`, and `extraData`.

Successful execution:
- Result:
  Stores allowance for `{spender}`, emits an `Approval` event, and performs the token contract's spender notification call.
- Invocation:
  Step 1: `POST /{contractAddress}/approveAndCall` with JSON body fields `spender`, `value`, and `extraData`, plus optional `privateFor` header
- Constraints:
  `spender` must be an ABI-valid address. `value` must be encodable as Solidity `uint256`. `extraData` must be non-null because the controller calls `extraData.getBytes()`. The low-level Solidity call to `spender` must succeed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - `{spender}` does not accept or successfully complete the approval notification call. This can be produced by using an externally owned account, an incompatible contract, an address without code, or a receiver that reverts.
  - Failure endpoint:
    `POST /{contractAddress}/approveAndCall`
  - Why this fails:
    Solidity reverts when the low-level call to `spender` is unsuccessful, so the approval-and-notification transaction is not completed.
  - Intentionally violated constraints:
    The spender notification target does not satisfy the receiver-call requirement.
- Branch 2:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - The request body follows Swagger literally as an empty object, or otherwise omits `spender`, `value`, or `extraData`.
  - Failure endpoint:
    `POST /{contractAddress}/approveAndCall`
  - Why this fails:
    The controller reads `spender`, `value`, and `extraData`; missing values break address encoding, `uint256` encoding, or `extraData.getBytes()`.
  - Intentionally violated constraints:
    Source-required body fields are omitted despite the Swagger schema exposing no properties.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approveAndCall`
- Distinct meaning:
  Creates allowance state and attempts spender notification in one transaction.

### Function 16: replace approval and notify spender

Function name:
replace approval and notify spender

Core endpoint(s):
- `POST /{contractAddress}/approveAndCall`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The same `{spender}` already has an allowance from the configured transaction sender on the deployed contract, and the previous notification call succeeded. This can be satisfied by directly setting `allowed[fromAddress][spender]` in equivalent contract state with an appropriate receiver setup or by calling `POST /{contractAddress}/approveAndCall` once with the same `{contractAddress}`, `spender`, earlier `value`, and non-null `extraData`.
- The `{contractAddress}` used by the replacement request must identify the same deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`; if the API is used to establish the prior allowance, the same address must be reused.
- The `{spender}` address must be able to accept the replacement notification call. No documented REST endpoint creates that receiver contract; direct setup must deploy or provide a compatible spender contract/address outside this API.

Successful execution:
- Result:
  Overwrites the existing allowance for `{spender}` and performs the spender notification call.
- Invocation:
  Step 1: `POST /{contractAddress}/approveAndCall` with the same `{contractAddress}` and `spender`, a new `value`, non-null `extraData`, and optional `privateFor` header
- Constraints:
  The Solidity function overwrites `allowed[msg.sender][spender]`; it does not add to the prior allowance. Both address/value encoding and spender notification requirements must be satisfied.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - `{spender}` can accept the notification call, but no prior allowance exists for the configured transaction sender and `{spender}`. This can be produced by directly leaving `allowed[fromAddress][spender]` unset or zero, or by intentionally not calling `POST /{contractAddress}/approveAndCall` before the replacement request.
  - Failure endpoint:
    `POST /{contractAddress}/approveAndCall`
  - Why this fails:
    The endpoint may still create an allowance and notify the spender, but the endpoint-level effect is creation rather than replacement because no existing allowance was present.
  - Intentionally violated constraints:
    The prerequisite existing approval-and-call allowance for the same owner and spender was omitted.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approveAndCall`
- Distinct meaning:
  Uses approve-and-call as an allowance overwrite capability with notification.

### Function 17: clear approval and notify spender

Function name:
clear approval and notify spender

Core endpoint(s):
- `POST /{contractAddress}/approveAndCall`

Preconditions:
- A deployed `HumanStandardToken` contract exists. This can be satisfied by directly preparing equivalent deployed contract state on Ethereum/Quorum or by calling `POST /deploy` with body fields `initialAmount`, `tokenName`, `decimalUnits`, and `tokenSymbol`.
- The same `{spender}` has a prior nonzero allowance from the configured transaction sender on the deployed contract, and the previous notification call succeeded. This can be satisfied by directly setting `allowed[fromAddress][spender]` to a nonzero value in equivalent contract state with an appropriate receiver setup or by calling `POST /{contractAddress}/approveAndCall` once with the same `{contractAddress}`, `spender`, nonzero `value`, and non-null `extraData`.
- The `{contractAddress}` used by the clearing request must identify the same deployed contract. If the API is used to establish the contract, `{contractAddress}` must be the hex address returned by `POST /deploy`; if the API is used to establish the prior allowance, the same address must be reused.
- The `{spender}` address must be able to accept the clearing notification call. No documented REST endpoint creates that receiver contract; direct setup must deploy or provide a compatible spender contract/address outside this API.

Successful execution:
- Result:
  Sets the spender's allowance to `0` and performs the spender notification call.
- Invocation:
  Step 1: `POST /{contractAddress}/approveAndCall` with the same `{contractAddress}` and `spender`, JSON body field `value=0`, non-null `extraData`, and optional `privateFor` header
- Constraints:
  The endpoint stores zero for `allowed[msg.sender][spender]`, emits an `Approval` event, and still requires the spender notification call to succeed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A deployed token contract exists at `{contractAddress}`. This can be satisfied by direct contract setup or by calling `POST /deploy`.
    - `{spender}` can accept the notification call, but no prior nonzero allowance exists for the configured transaction sender and `{spender}`. This can be produced by directly setting the allowance to zero or leaving it unset, or by intentionally not calling `POST /{contractAddress}/approveAndCall` with a nonzero value before the clearing request.
  - Failure endpoint:
    `POST /{contractAddress}/approveAndCall`
  - Why this fails:
    The call may still store zero and notify the spender, but it does not clear an existing usable allowance.
  - Intentionally violated constraints:
    The prerequisite nonzero approval-and-call allowance for the same owner and spender was omitted.

Endpoint coverage:
- Covers:
  `POST /{contractAddress}/approveAndCall`
- Distinct meaning:
  Uses approve-and-call as an allowance-clearing capability with notification.
