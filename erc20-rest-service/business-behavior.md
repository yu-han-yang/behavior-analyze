# Domain-Level Behavior Analysis

## Domain Summary

This service exposes a REST wrapper around an ERC-20 `HumanStandardToken` smart contract deployed to Ethereum/Quorum. The main domain resources are:

- service node configuration: Ethereum/Quorum endpoint and configured transaction sender
- deployed token contracts: finite-supply ERC-20 contracts with name, symbol, decimals, version, and total supply
- token balances: balances held by blockchain addresses
- allowances: owner-to-spender spending approvals
- token transfers: direct transfers by the configured sender and delegated `transferFrom` transfers
- Quorum private transactions: optional `privateFor` header on state-changing calls

The implementation is account-centric: all transactions are sent as the configured `fromAddress`. The API does not support per-request wallet identity, private key selection, authentication, or arbitrary caller impersonation.

## Available Function Inventory

### Configuration

- `get application configuration`
  - Endpoint: `GET /config`
  - Domain meaning: returns the configured node endpoint and transaction sender address.

### Token Deployment

- `deploy ERC-20 token`
  - Endpoint: `POST /deploy`
  - Domain meaning: deploys a new finite-supply `HumanStandardToken`; the initial supply is assigned to the configured transaction sender.

### Token Metadata and Supply Reads

- `retrieve token name`
  - Endpoint: `GET /{contractAddress}/name`
  - Domain meaning: reads the deployed token name.

- `retrieve token symbol`
  - Endpoint: `GET /{contractAddress}/symbol`
  - Domain meaning: reads the deployed token symbol.

- `retrieve token decimal precision`
  - Endpoint: `GET /{contractAddress}/decimals`
  - Domain meaning: reads display precision.

- `retrieve token version`
  - Endpoint: `GET /{contractAddress}/version`
  - Domain meaning: reads the token contract version, initialized as `H0.1`.

- `retrieve total token supply`
  - Endpoint: `GET /{contractAddress}/totalSupply`
  - Domain meaning: reads total minted supply.

### Balance and Allowance Reads

- `retrieve address token balance`
  - Endpoint: `GET /{contractAddress}/balanceOf/{ownerAddress}`
  - Domain meaning: reads one address balance.

- `retrieve spending allowance`
  - Endpoint: `GET /{contractAddress}/allowance`
  - Domain meaning: reads the allowance from `ownerAddress` to `spenderAddress`.

### Allowance Management

- `create spending approval`
  - Endpoint: `POST /{contractAddress}/approve`
  - Domain meaning: grants a spender an allowance from the configured transaction sender.

- `replace spending approval`
  - Endpoint: `POST /{contractAddress}/approve`
  - Domain meaning: overwrites an existing allowance for the same owner/spender pair.

- `clear spending approval`
  - Endpoint: `POST /{contractAddress}/approve`
  - Domain meaning: revokes a spender by setting allowance to `0`.

### Token Transfers

- `transfer owned tokens`
  - Endpoint: `POST /{contractAddress}/transfer`
  - Domain meaning: transfers tokens owned by the configured transaction sender.

- `transfer approved tokens from an owner`
  - Endpoint: `POST /{contractAddress}/transferFrom`
  - Domain meaning: transfers from an owner using allowance granted to the configured transaction sender.

### Approval With Notification

- `create approval and notify spender`
  - Endpoint: `POST /{contractAddress}/approveAndCall`
  - Domain meaning: creates allowance and attempts to notify the spender contract.

- `replace approval and notify spender`
  - Endpoint: `POST /{contractAddress}/approveAndCall`
  - Domain meaning: overwrites allowance and attempts spender notification.

- `clear approval and notify spender`
  - Endpoint: `POST /{contractAddress}/approveAndCall`
  - Domain meaning: clears allowance to `0` and attempts spender notification.

## Supported Business Behaviors

### Behavior 1: Inspect Service Transaction Context

Business goal:
Discover which blockchain node and sender account the service will use for contract deployment and write transactions.

Domain context:
Because the API never accepts a caller identity per request, the configured `fromAddress` is the effective owner, deployer, approver, and transfer sender for most workflows.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get application configuration` (`GET /config`) to obtain `nodeEndpoint` and `fromAddress`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If `nodeEndpoint` and `fromAddress` are already known from deployment configuration, this function can be skipped.
- The same configured `fromAddress` must still be the account used by the running service.

Parameter and value bindings:
- The response field `fromAddress` is reused as the initial token holder after `deploy ERC-20 token`.
- The same `fromAddress` is the Solidity `msg.sender` for `create spending approval`, `transfer owned tokens`, `transfer approved tokens from an owner`, and approval-notification functions.

Business result:
The client knows the blockchain node and sender account that scope all state-changing API behavior.

Constraints and invariants:
- The service returns configuration directly from `NodeConfiguration`.
- No authentication, authorization, or endpoint-specific 401/403/404 handling is implemented despite Swagger listing those responses.

Failure and exceptional cases:
- Failing function: `get application configuration`
  - Failure condition: none visible in controller logic.
  - Why it fails: no endpoint-specific failure branch exists.
  - Violated prerequisite or constraint: none.

Implementation notes:
The values come from Spring configuration/system properties, not from blockchain state.

### Behavior 2: Deploy a Finite-Supply ERC-20 Token

Business goal:
Create a new ERC-20 token contract with fixed initial supply and human-readable metadata.

Domain context:
Deployment is the entry point for nearly every other behavior because contract-scoped endpoints require a valid `contractAddress`.

Starting point:
No prior token contract state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"`, and optional header `privateFor="recipientKey1,recipientKey2"` to deploy a token contract and capture the returned `contractAddress`.

Optional verification workflow:
1. Use function `retrieve total token supply` (`GET /{contractAddress}/totalSupply`) with `contractAddress` equal to the deployment response to inspect total supply.
2. Use function `get application configuration` (`GET /config`) to read `fromAddress`.
3. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `ownerAddress=fromAddress` to verify the initial supply was assigned to the service sender.

Existing-state shortcuts:
- If an equivalent `HumanStandardToken` contract already exists, the deploy step can be skipped and the known deployed address can be reused.
- Direct blockchain setup is equivalent only if the contract exposes the same ABI and constructor-derived values.
- The captured `contractAddress` must still identify the deployed token used in later path parameters.

Parameter and value bindings:
- `initialAmount` becomes both `totalSupply` and the initial balance of configured `fromAddress`.
- `tokenName`, `tokenSymbol`, and `decimalUnits` are later consumed by metadata read functions.
- The returned `contractAddress` is reused in all contract-scoped endpoints.

Business result:
A token contract exists on-chain. The configured sender owns the entire initial supply. Name, symbol, decimals, total supply, and version are persisted in contract state.

Constraints and invariants:
- `initialAmount` must fit Solidity `uint256`.
- `decimalUnits` must fit Solidity `uint8`.
- The implementation does not validate token name or symbol uniqueness.
- The API does not maintain a registry of deployed contracts.

Failure and exceptional cases:
- Failing function: `deploy ERC-20 token`
  - Failure condition: missing body fields, negative `initialAmount`, invalid `decimalUnits`, unreachable node, or unusable `fromAddress`.
  - Why it fails: controller passes values directly into `HumanStandardToken.deploy(...)`; ABI encoding or transaction submission fails.
  - Violated prerequisite or constraint: constructor values and node/sender configuration must be valid.

Implementation notes:
The contract constructor assigns all initial tokens to `msg.sender`, which is the configured `fromAddress` via `ClientTransactionManager`.

### Behavior 3: Inspect Token Metadata

Business goal:
Read the human-facing token definition used by wallets and clients.

Domain context:
Name, symbol, decimals, and version define how a token is displayed and interpreted.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header to create the contract and capture `contractAddress`.
2. Use function `retrieve token name` (`GET /{contractAddress}/name`) with `contractAddress` equal to the deployed address to read `"Quorum Token"`.
3. Use function `retrieve token symbol` (`GET /{contractAddress}/symbol`) with the same `contractAddress` to read `"QT"`.
4. Use function `retrieve token decimal precision` (`GET /{contractAddress}/decimals`) with the same `contractAddress` to read `25`.
5. Use function `retrieve token version` (`GET /{contractAddress}/version`) with the same `contractAddress` to read `H0.1`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the deployed token address already exists, skip `deploy ERC-20 token` and use that address.
- Direct blockchain setup is acceptable if the address exposes `name()`, `symbol()`, `decimals()`, and `version()`.

Parameter and value bindings:
- The same `contractAddress` must be reused across all metadata reads.
- The deployment body values `tokenName`, `tokenSymbol`, and `decimalUnits` bind directly to the read results.
- `version` is not supplied by the API; it is fixed by the Solidity contract.

Business result:
The client obtains the token’s display name, symbol, decimal precision, and contract version.

Constraints and invariants:
- Metadata is immutable after deployment through this API.
- The implementation does not verify that the address was deployed by this service.

Failure and exceptional cases:
- Failing function: `retrieve token name`
  - Failure condition: `contractAddress` does not point to a compatible token.
  - Why it fails: service loads the address and calls `name()`.
  - Violated prerequisite or constraint: valid token contract address required.
- Failing function: `retrieve token symbol`
  - Failure condition: same invalid or incompatible address.
  - Why it fails: service calls `symbol()`.
  - Violated prerequisite or constraint: address must expose the expected ABI.
- Failing function: `retrieve token decimal precision`
  - Failure condition: same invalid or incompatible address.
  - Why it fails: service calls `decimals()`.
  - Violated prerequisite or constraint: address must expose the expected ABI.
- Failing function: `retrieve token version`
  - Failure condition: same invalid or incompatible address.
  - Why it fails: service calls `version()`.
  - Violated prerequisite or constraint: address must expose the expected ABI.

Implementation notes:
Swagger and implementation agree on these metadata endpoints.

### Behavior 4: Inspect Supply and Holder Balance

Business goal:
Understand token supply and how many tokens a particular address owns.

Domain context:
Supply and balance inspection is needed before transfer, delegated transfer, reconciliation, or UI display.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header to create a token and capture `contractAddress`.
3. Use function `retrieve total token supply` (`GET /{contractAddress}/totalSupply`) with the captured `contractAddress` to read `1000000`.
4. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `contractAddress` equal to the deployed address and `ownerAddress=fromAddress` to read the initial holder balance.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a token already exists, skip deployment and use its known `contractAddress`.
- If the holder address is already known, `get application configuration` can be skipped.
- Direct blockchain setup is equivalent if total supply and holder balances already exist.

Parameter and value bindings:
- `initialAmount` from deployment binds to `totalSupply`.
- Deployment sender `fromAddress` binds to `ownerAddress` for the initial nonzero balance read.
- `contractAddress` scopes both supply and balance reads.

Business result:
The client knows total supply and the balance of the selected holder.

Constraints and invariants:
- Querying an address with no balance succeeds and returns `0`.
- Total supply is fixed by deployment; there is no mint or burn function through this API.

Failure and exceptional cases:
- Failing function: `retrieve total token supply`
  - Failure condition: invalid or incompatible `contractAddress`.
  - Why it fails: service calls `totalSupply()` on the supplied address.
  - Violated prerequisite or constraint: address must be a compatible token.
- Failing function: `retrieve address token balance`
  - Failure condition: invalid contract address.
  - Why it fails: service calls `balanceOf(ownerAddress)`.
  - Violated prerequisite or constraint: compatible token contract required.
- Failing function: `retrieve address token balance`
  - Failure condition: valid contract but unfunded `ownerAddress`.
  - Why it fails: endpoint succeeds but returns `0`.
  - Violated prerequisite or constraint: nonzero balance requires prior funding.

Implementation notes:
The API returns numeric values as strings from service methods, although tests deserialize them as `BigInteger`.

### Behavior 5: Transfer Service-Owned Tokens

Business goal:
Move tokens from the configured service account to another address.

Domain context:
This is the primary direct token movement workflow supported by the API.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header to create a token funded to the configured sender; capture `contractAddress`.
2. Use function `transfer owned tokens` (`POST /{contractAddress}/transfer`) with the same `contractAddress`, body `to=recipientAddress`, `value=10000`, and optional `privateFor` header to transfer from the configured sender to `recipientAddress`.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `ownerAddress=recipientAddress` to inspect the recipient balance.
3. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `ownerAddress=fromAddress` to inspect the sender balance.

Existing-state shortcuts:
- If the token already exists and the configured sender already has at least `value`, skip deployment.
- Direct blockchain setup may pre-fund the configured sender.
- The transfer action itself cannot be skipped.

Parameter and value bindings:
- The deployment response `contractAddress` is reused in the transfer path.
- Deployment `initialAmount` must be greater than or equal to transfer `value`.
- Transfer body `to` becomes the `to` field in the emitted `Transfer` event and the recipient balance key.
- Configured `fromAddress` is the implicit sender; it is not supplied in the transfer body.

Business result:
On success, `value` tokens are subtracted from the configured sender and added to `recipientAddress`. The response contains a transaction hash and a `Transfer` event.

Constraints and invariants:
- `value` must be greater than `0`.
- `value` must not exceed the configured sender’s balance.
- `to` must be an ABI-valid address.
- Event `value` is mapped with Java `longValueExact()`, so very large values can fail response mapping.

Failure and exceptional cases:
- Failing function: `deploy ERC-20 token`
  - Failure condition: invalid constructor values or unusable node/sender.
  - Why it fails: deployment transaction cannot be encoded or submitted.
  - Violated prerequisite or constraint: valid token deployment required before transfer.
- Failing function: `transfer owned tokens`
  - Failure condition: `value=0` or sender balance less than `value`.
  - Why it fails: Solidity `transfer` returns `false`; no `Transfer` event is emitted.
  - Violated prerequisite or constraint: positive value and sufficient balance required.
- Failing function: `transfer owned tokens`
  - Failure condition: invalid `contractAddress`, invalid `to`, missing or invalid `value`.
  - Why it fails: contract loading, address encoding, or uint encoding fails.
  - Violated prerequisite or constraint: valid contract and body required.

Implementation notes:
A failed Solidity transfer can still produce HTTP 200 with a transaction hash and no event. The service does not surface the contract boolean return value directly.

### Behavior 6: Create a Spending Approval

Business goal:
Allow a spender address to spend up to a specified token amount from the configured service account.

Domain context:
Allowance is the ERC-20 mechanism for delegated spending.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header to create the token and capture `contractAddress`.
2. Use function `create spending approval` (`POST /{contractAddress}/approve`) with the same `contractAddress`, body `spender=spenderAddress`, `value=10000`, and optional `privateFor` header to approve spending.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with query `ownerAddress=fromAddress` and `spenderAddress=spenderAddress` to inspect the allowance.

Existing-state shortcuts:
- If the token already exists, skip deployment.
- If equivalent allowance already exists, this behavior is already established; calling approval again with the same value still writes and emits an event.
- Direct blockchain setup may set `allowed[fromAddress][spenderAddress]`.

Parameter and value bindings:
- `contractAddress` from deployment scopes the approval.
- Body `spender` is reused as `spenderAddress` in allowance verification.
- The implicit owner is configured `fromAddress`; it appears as `event.owner`.

Business result:
Allowance `allowed[fromAddress][spenderAddress]` equals `10000`. The response includes transaction hash and `Approval` event.

Constraints and invariants:
- Approval overwrites any existing allowance; it is not additive.
- `spender` must be an ABI-valid address.
- `value` must fit Solidity `uint256` and Java response `long` for event mapping.

Failure and exceptional cases:
- Failing function: `create spending approval`
  - Failure condition: invalid contract address.
  - Why it fails: service submits `approve` to an incompatible address.
  - Violated prerequisite or constraint: valid token contract required.
- Failing function: `create spending approval`
  - Failure condition: missing or invalid `spender`, negative or missing `value`, or value outside Java `long` during event mapping.
  - Why it fails: encoding or response mapping fails.
  - Violated prerequisite or constraint: valid request body and event value range required.

Implementation notes:
The API does not support approving from an arbitrary owner; approvals always originate from configured `fromAddress`.

### Behavior 7: Replace a Spending Approval

Business goal:
Change an existing spender allowance to a new total value.

Domain context:
ERC-20 `approve` stores the new allowance amount and overwrites the old one.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
2. Use function `create spending approval` (`POST /{contractAddress}/approve`) with `contractAddress`, body `spender=spenderAddress`, `value=10000`, and optional `privateFor` header to establish the initial allowance.
3. Use function `replace spending approval` (`POST /{contractAddress}/approve`) with the same `contractAddress`, same `spender=spenderAddress`, body `value=25000`, and optional `privateFor` header to replace the allowance.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=spenderAddress` to verify the value is `25000`.

Existing-state shortcuts:
- If the token and prior allowance already exist, skip deployment and initial approval.
- Direct blockchain setup may pre-create `allowed[fromAddress][spenderAddress]`.
- The replacement call itself is required to perform this behavior.

Parameter and value bindings:
- The same `contractAddress` and `spenderAddress` must be reused.
- Earlier `value=10000` and later `value=25000` intentionally differ; the later value replaces the former total allowance.
- Owner remains implicit `fromAddress`.

Business result:
The previous allowance no longer controls spending. The current allowance for `fromAddress` to `spenderAddress` is exactly `25000`.

Constraints and invariants:
- The implementation does not require the previous allowance to be zero before setting a new nonzero value.
- If no prior allowance exists, the same endpoint succeeds as creation rather than replacement.

Failure and exceptional cases:
- Failing function: `replace spending approval`
  - Failure condition: no prior allowance for the same owner/spender pair.
  - Why it fails: the API call may succeed, but the domain effect is creation, not replacement.
  - Violated prerequisite or constraint: existing allowance required for replacement semantics.
- Failing function: `create spending approval`
  - Failure condition: invalid spender/value/contract.
  - Why it fails: setup allowance cannot be created.
  - Violated prerequisite or constraint: valid initial allowance required.

Implementation notes:
This service exposes the known ERC-20 allowance overwrite pattern and does not mitigate allowance race risks.

### Behavior 8: Clear a Spending Approval

Business goal:
Revoke a spender’s allowance from the configured service account.

Domain context:
Setting allowance to zero is the supported revocation mechanism.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
2. Use function `create spending approval` (`POST /{contractAddress}/approve`) with `spender=spenderAddress`, `value=10000`, and optional `privateFor` header to create a nonzero allowance.
3. Use function `clear spending approval` (`POST /{contractAddress}/approve`) with the same `contractAddress`, same `spender=spenderAddress`, body `value=0`, and optional `privateFor` header to clear it.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=spenderAddress` to verify `0`.

Existing-state shortcuts:
- If a nonzero allowance already exists for the same owner/spender, skip deployment and initial approval.
- Direct blockchain setup may establish `allowed[fromAddress][spenderAddress] > 0`.
- Clearing still requires the zero-value approval call.

Parameter and value bindings:
- Same `contractAddress` and `spenderAddress` bind the nonzero setup approval to the zero-value clearing approval.
- `value=0` intentionally differs from the setup value and means revocation.

Business result:
The spender’s allowance from the configured sender is `0`.

Constraints and invariants:
- Clearing a missing or already-zero allowance still stores zero and can emit an `Approval` event, but it is not a meaningful revocation.
- Spender address validation still applies.

Failure and exceptional cases:
- Failing function: `clear spending approval`
  - Failure condition: no prior nonzero allowance.
  - Why it fails: endpoint may succeed, but there was no usable allowance to clear.
  - Violated prerequisite or constraint: prior nonzero allowance required for revocation semantics.
- Failing function: `create spending approval`
  - Failure condition: invalid setup approval.
  - Why it fails: nonzero allowance is never established.
  - Violated prerequisite or constraint: valid setup state required.

Implementation notes:
The service does not provide a dedicated revoke endpoint; revocation is encoded as `approve(..., 0)`.

### Behavior 9: Retrieve a Spending Allowance

Business goal:
Check how many tokens one spender may spend from one owner.

Domain context:
Allowance inspection is required before delegated spending and after approval changes.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
3. Use function `create spending approval` (`POST /{contractAddress}/approve`) with `spender=spenderAddress`, `value=10000`, and optional `privateFor` header.
4. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with query `ownerAddress=fromAddress` and `spenderAddress=spenderAddress`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the token and allowance already exist, skip deployment and approval.
- If only a zero allowance is acceptable, approval setup can be skipped; the read will return `0`.
- Direct blockchain setup may create `allowed[ownerAddress][spenderAddress]`.

Parameter and value bindings:
- `ownerAddress` must match the approval event owner, which is configured `fromAddress`.
- `spenderAddress` must match the approval body `spender`.
- The same `contractAddress` scopes both approval and allowance read.

Business result:
The client receives the current allowance for a specific owner/spender pair.

Constraints and invariants:
- Missing allowance is represented as `0`, not as a missing resource.
- Query values must be ABI-valid addresses.

Failure and exceptional cases:
- Failing function: `retrieve spending allowance`
  - Failure condition: invalid contract address.
  - Why it fails: service calls `allowance(ownerAddress, spenderAddress)` on an incompatible address.
  - Violated prerequisite or constraint: valid token contract required.
- Failing function: `retrieve spending allowance`
  - Failure condition: wrong owner/spender pair.
  - Why it fails: endpoint succeeds but returns `0`.
  - Violated prerequisite or constraint: queried pair must match the created allowance.

Implementation notes:
Swagger correctly models allowance as query parameters.

### Behavior 10: Transfer Approved Tokens From an Owner

Business goal:
Move tokens using an allowance granted to the configured service sender.

Domain context:
This is delegated ERC-20 spending. In this API, the spender is always the configured `fromAddress`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
3. Use function `create spending approval` (`POST /{contractAddress}/approve`) with the same `contractAddress`, body `spender=fromAddress`, `value=10000`, and optional `privateFor` header so the configured sender is approved spender.
4. Use function `transfer approved tokens from an owner` (`POST /{contractAddress}/transferFrom`) with the same `contractAddress`, body `from=fromAddress`, `to=recipientAddress`, `value=5000`, and optional `privateFor` header.

Optional verification workflow:
1. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=fromAddress` to inspect remaining allowance.
2. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `ownerAddress=recipientAddress` to inspect the recipient balance.
3. Use function `retrieve address token balance` (`GET /{contractAddress}/balanceOf/{ownerAddress}`) with `ownerAddress=fromAddress` to inspect the source balance.

Existing-state shortcuts:
- If the token exists, the owner has sufficient balance, and `allowed[owner][fromAddress] >= value`, setup deployment and approval can be skipped.
- Direct blockchain setup can create the more natural case where an external owner approved the service sender.
- The `transferFrom` action itself cannot be skipped.

Parameter and value bindings:
- `spender` in approval must equal configured `fromAddress`, because `transferFrom` checks `allowed[from][msg.sender]`.
- `from` in transferFrom must match the allowance owner.
- `value=5000` must be less than or equal to both owner balance and allowance.
- The same `contractAddress` scopes approval, transfer, allowance read, and balances.

Business result:
On success, `value` tokens move from `from` to `to`, and allowance from `from` to configured sender is reduced by `value`.

Constraints and invariants:
- With only API calls, the owner that can approve is also the configured sender, so the fully API-realizable workflow is effectively self-approved delegated transfer.
- `value` must be greater than `0`.
- Swagger omits `TransferFromRequest.value`, but the controller requires it.

Failure and exceptional cases:
- Failing function: `transfer approved tokens from an owner`
  - Failure condition: approval spender is not configured `fromAddress`.
  - Why it fails: contract checks `allowed[_from][msg.sender]`; `msg.sender` is the configured sender.
  - Violated prerequisite or constraint: allowance must target the configured service sender.
- Failing function: `transfer approved tokens from an owner`
  - Failure condition: `value=0`, value exceeds owner balance, or value exceeds allowance.
  - Why it fails: Solidity returns `false`, no `Transfer` event is emitted, and balances remain unchanged.
  - Violated prerequisite or constraint: positive value, sufficient balance, and sufficient allowance required.
- Failing function: `transfer approved tokens from an owner`
  - Failure condition: request follows Swagger and omits `value`.
  - Why it fails: controller reads `transferFromRequest.getValue()` and passes it to Web3j.
  - Violated prerequisite or constraint: implementation-required `value` body field is missing.

Implementation notes:
The integration test documents this limitation: approving `OTHER_ACCOUNT` does not let the service perform `transferFrom` as `OTHER_ACCOUNT`, so the transfer returns a transaction hash without an event.

### Behavior 11: Create Approval and Notify Spender Contract

Business goal:
Grant allowance and notify a spender contract in one transaction.

Domain context:
This is intended for contract-to-contract workflows where the spender reacts to approval.

Starting point:
A compatible spender contract/address already exists outside this REST API, and no prior token contract state exists.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
2. Use function `create approval and notify spender` (`POST /{contractAddress}/approveAndCall`) with the same `contractAddress`, body `spender=spenderContractAddress`, `value=10000`, `extraData="payload"`, and optional `privateFor` header.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=spenderContractAddress` to inspect allowance.

Existing-state shortcuts:
- If the token already exists, skip deployment.
- If a compatible spender receiver already exists, reuse its address.
- There is no API function to create the spender receiver contract.

Parameter and value bindings:
- `spender` becomes the allowance spender and the notification call target.
- `extraData` must be non-null because the controller calls `extraData.getBytes()`.
- Owner is implicit configured `fromAddress`.
- Same `contractAddress` scopes approval and allowance verification.

Business result:
If the notification call succeeds, allowance from configured sender to `spenderContractAddress` equals `10000`, an `Approval` event is emitted, and the spender target is called.

Constraints and invariants:
- `spender` must be ABI-valid and able to accept the notification call.
- Swagger defines `ApproveAndCallRequest` as an empty object, but implementation requires `spender`, `value`, and `extraData`.
- The Solidity implementation uses a low-level call with encoded string data, which may not match standard `receiveApproval(address,uint256,address,bytes)` receiver expectations.

Failure and exceptional cases:
- Failing function: `create approval and notify spender`
  - Failure condition: spender is an externally owned account, has no code, reverts, or otherwise does not accept the low-level call.
  - Why it fails: Solidity reverts when the low-level call returns false.
  - Violated prerequisite or constraint: spender notification target must be compatible.
- Failing function: `create approval and notify spender`
  - Failure condition: missing `spender`, `value`, or `extraData`.
  - Why it fails: address encoding, uint encoding, or `extraData.getBytes()` fails.
  - Violated prerequisite or constraint: source-required body fields must be supplied.

Implementation notes:
The receiver contract cannot be deployed or registered through this API, so this behavior is only partially API-realizable.

### Behavior 12: Replace Approval and Notify Spender Contract

Business goal:
Change an existing notified spender allowance and notify the same spender again.

Domain context:
This extends allowance replacement to contract-aware spenders.

Starting point:
A compatible spender receiver exists outside this REST API.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
2. Use function `create approval and notify spender` (`POST /{contractAddress}/approveAndCall`) with `spender=spenderContractAddress`, `value=10000`, `extraData="initial"`, and optional `privateFor` header.
3. Use function `replace approval and notify spender` (`POST /{contractAddress}/approveAndCall`) with the same `contractAddress`, same `spender=spenderContractAddress`, body `value=25000`, `extraData="replacement"`, and optional `privateFor` header.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=spenderContractAddress` to verify `25000`.

Existing-state shortcuts:
- If token, spender receiver, and prior allowance already exist, skip deployment and first approval-notification.
- Direct blockchain setup may establish `allowed[fromAddress][spenderContractAddress]`.
- Replacement notification still requires the second `approveAndCall`.

Parameter and value bindings:
- The same `contractAddress` and `spenderContractAddress` identify the allowance being replaced.
- `value=25000` replaces `value=10000`; it does not add to it.
- `extraData` may differ to communicate replacement context to the receiver, if the receiver can interpret the call.

Business result:
Allowance for the spender contract is overwritten to `25000`, and the spender is notified again if the low-level call succeeds.

Constraints and invariants:
- If no prior allowance exists, the call creates allowance rather than replacing it.
- Receiver compatibility is required for both setup and replacement calls.

Failure and exceptional cases:
- Failing function: `replace approval and notify spender`
  - Failure condition: no prior allowance exists for the same owner/spender.
  - Why it fails: endpoint may create allowance, but it is not replacement behavior.
  - Violated prerequisite or constraint: prior allowance required for replacement semantics.
- Failing function: `replace approval and notify spender`
  - Failure condition: replacement notification fails.
  - Why it fails: Solidity reverts on failed low-level call.
  - Violated prerequisite or constraint: compatible notification receiver required.

Implementation notes:
The same Swagger/body mismatch and low-level notification concerns apply as in creation.

### Behavior 13: Clear Approval and Notify Spender Contract

Business goal:
Revoke a spender contract allowance and notify it of the revocation.

Domain context:
This is the notified form of allowance revocation.

Starting point:
A compatible spender receiver exists outside this REST API.

Required execution workflow:
1. Use function `deploy ERC-20 token` (`POST /deploy`) with body `initialAmount=1000000`, `tokenName="Quorum Token"`, `decimalUnits=25`, `tokenSymbol="QT"` and optional `privateFor` header; capture `contractAddress`.
2. Use function `create approval and notify spender` (`POST /{contractAddress}/approveAndCall`) with `spender=spenderContractAddress`, `value=10000`, `extraData="initial"`, and optional `privateFor` header.
3. Use function `clear approval and notify spender` (`POST /{contractAddress}/approveAndCall`) with the same `contractAddress`, same `spender=spenderContractAddress`, body `value=0`, `extraData="clear"`, and optional `privateFor` header.

Optional verification workflow:
1. Use function `get application configuration` (`GET /config`) to capture `fromAddress`.
2. Use function `retrieve spending allowance` (`GET /{contractAddress}/allowance`) with `ownerAddress=fromAddress` and `spenderAddress=spenderContractAddress` to verify `0`.

Existing-state shortcuts:
- If a nonzero notified allowance already exists, skip deployment and setup notification.
- Direct blockchain setup may establish the prior allowance, but the spender receiver still needs to accept the clearing notification.
- The zero-value notification call is required.

Parameter and value bindings:
- Same `contractAddress` and `spenderContractAddress` bind the setup and clearing calls.
- `value=0` means revocation.
- `extraData` must be non-null on the clearing call.

Business result:
Allowance for the spender contract becomes `0`, and the spender is notified if the low-level call succeeds.

Constraints and invariants:
- Clearing a missing or already-zero allowance may still store zero and notify, but it is not a meaningful revocation.
- Receiver compatibility is still required even for zero-value clearing.

Failure and exceptional cases:
- Failing function: `clear approval and notify spender`
  - Failure condition: no prior nonzero allowance exists.
  - Why it fails: endpoint may succeed but does not revoke an active allowance.
  - Violated prerequisite or constraint: nonzero allowance required for clearing semantics.
- Failing function: `clear approval and notify spender`
  - Failure condition: receiver call fails.
  - Why it fails: Solidity reverts the transaction.
  - Violated prerequisite or constraint: spender must accept notification.

Implementation notes:
Because the API has no receiver deployment or discovery support, this workflow depends on external blockchain setup.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Act on Behalf of Arbitrary Wallet Users

Priority:
Critical domain gap

Expected business goal:
A token service user should be able to transfer, approve, or delegated-transfer using their own blockchain identity.

Why it is unsupported:
Every write function uses `ClientTransactionManager` with configured `fromAddress`. No endpoint accepts a sender account, signature, credentials, session, or delegated authorization.

Existing functions considered:
- `transfer owned tokens`: can transfer only from configured `fromAddress`.
- `create spending approval`: can approve only from configured `fromAddress`.
- `transfer approved tokens from an owner`: spender is still configured `fromAddress`.
- `get application configuration`: only reveals the configured sender.

Missing capability:
Per-request sender identity, signed transaction submission, wallet/session binding, or multi-account transaction manager support.

Proof that function composition is insufficient:
No chain of existing functions can change Solidity `msg.sender` away from configured `fromAddress`. Creating approvals cannot make the service act as another spender. Transfers cannot debit arbitrary users unless they already approved configured `fromAddress`.

Evidence from existing functions/source:
`ContractService.load(...)` and `deploy(...)` always construct `ClientTransactionManager(quorum, nodeConfiguration.getFromAddress(), privateFor)`.

Business impact:
The API is unsuitable as a general multi-user ERC-20 service; it behaves as a single service-account wallet.

### Missing Behavior 2: Discover or List Deployed Token Contracts

Priority:
Critical domain gap

Expected business goal:
A client should be able to list, search, or retrieve tokens deployed through the service.

Why it is unsupported:
Deployment returns only a raw contract address. The service persists no registry and exposes no listing endpoint.

Existing functions considered:
- `deploy ERC-20 token`: returns a contract address once.
- `retrieve token name`, `retrieve token symbol`, `retrieve total token supply`: require the address to already be known.

Missing capability:
Token registry persistence and endpoints such as `GET /tokens` or `GET /tokens/{contractAddress}` with ownership/source metadata.

Proof that function composition is insufficient:
If the deployment response is lost, no available function can recover deployed addresses. Metadata reads require the missing address as input.

Evidence from existing functions/source:
No repository, database model, or controller method stores deployed contract addresses.

Business impact:
Clients must maintain their own registry; service-created assets can become undiscoverable.

### Missing Behavior 3: Reliable Approval-and-Callback Integration

Priority:
Critical domain gap

Expected business goal:
Approve a spender contract and call its `receiveApproval(address,uint256,address,bytes)` method with owner, value, token address, and extra data.

Why it is unsupported:
The REST API cannot deploy or validate a compatible receiver, and the Solidity call appears to encode only the signature string rather than the expected selector and arguments.

Existing functions considered:
- `create approval and notify spender`: attempts a low-level call but provides no receiver setup.
- `replace approval and notify spender`: same limitation.
- `clear approval and notify spender`: same limitation.

Missing capability:
Receiver contract provisioning/discovery, proper ABI-encoded callback call, and failure diagnostics.

Proof that function composition is insufficient:
No REST function creates a receiver contract or repairs the callback payload. Chaining approvals only changes allowance; it cannot make an incompatible receiver accept the notification.

Evidence from existing functions/source:
`HumanStandardToken.approveAndCall` calls `_spender.call(abi.encode("receiveApproval(address,uint256,address,bytes)"))` and reverts on failure.

Business impact:
Contract-integrated approval workflows are fragile or unusable for normal ERC-20 approval receivers.

### Missing Behavior 4: Confirm Transaction Success as a Domain Result

Priority:
Important robustness gap

Expected business goal:
A transfer or delegated transfer should clearly report whether tokens moved.

Why it is unsupported:
The contract returns `false` for invalid transfer conditions, but the REST service returns a transaction hash and no event instead of a domain error or success flag.

Existing functions considered:
- `transfer owned tokens`: returns transaction hash even when no `Transfer` event exists.
- `transfer approved tokens from an owner`: same behavior.
- `retrieve address token balance`: can verify after the fact, but does not make the original write result reliable.

Missing capability:
Expose contract boolean return value, map missing events to failed domain result, or enforce pre-checks before submitting.

Proof that function composition is insufficient:
Post-read verification can detect unchanged balances only after the transaction. It cannot turn the original write response into an atomic success/failure contract.

Evidence from existing functions/source:
`processEventResponse` returns `TransactionResponse(transactionHash)` when event list is empty.

Business impact:
Clients can treat failed token movements as successful unless they manually inspect events or balances.

### Missing Behavior 5: Accurate OpenAPI Contract for Write Bodies

Priority:
Important robustness gap

Expected business goal:
Generated clients should know the required request fields.

Why it is unsupported:
Swagger omits `TransferFromRequest.value` and defines `ApproveAndCallRequest` as an empty object, while the controller requires fields.

Existing functions considered:
- `transfer approved tokens from an owner`: needs `from`, `to`, and `value`.
- `create approval and notify spender`: needs `spender`, `value`, and `extraData`.

Missing capability:
Correct OpenAPI schemas and required-field declarations.

Proof that function composition is insufficient:
Following the published schema produces requests that the implementation cannot execute. No preparatory function supplies the missing body fields.

Evidence from existing functions/source:
Controller request classes include `value` for `TransferFromRequest` and `spender/value/extraData` for `ApproveAndCallRequest`; Swagger definitions do not.

Business impact:
Client generation and API integration are unreliable.

### Missing Behavior 6: Safe Allowance Adjustment

Priority:
Important robustness gap

Expected business goal:
Increase, decrease, or safely replace allowances without ERC-20 overwrite race risks.

Why it is unsupported:
Only raw overwrite approval is exposed.

Existing functions considered:
- `create spending approval`: overwrites allowance.
- `replace spending approval`: same endpoint, same overwrite semantics.
- `clear spending approval`: can set zero but does not enforce zero-before-nonzero transitions.

Missing capability:
`increaseAllowance`, `decreaseAllowance`, or validation requiring current allowance to be zero before nonzero replacement.

Proof that function composition is insufficient:
A client can manually clear then set, but the service does not make that atomic or enforce it. Another spender action can occur between calls.

Evidence from existing functions/source:
`StandardToken.approve` directly assigns `allowed[msg.sender][_spender] = _value`.

Business impact:
Allowance management is vulnerable to known ERC-20 approval race patterns.

### Missing Behavior 7: Mint, Burn, or Adjust Supply After Deployment

Priority:
Important robustness gap

Expected business goal:
Token administrators may expect lifecycle operations to mint additional supply, burn tokens, or retire supply.

Why it is unsupported:
The contract creates finite supply in the constructor and exposes no mint or burn REST endpoints.

Existing functions considered:
- `deploy ERC-20 token`: creates initial supply only.
- `retrieve total token supply`: reads supply but cannot change it.
- `transfer owned tokens`: redistributes existing supply only.

Missing capability:
Mint/burn smart contract functions and admin-scoped REST endpoints.

Proof that function composition is insufficient:
Transfers only move balances and preserve `totalSupply`. Deploying a new contract creates a different token, not a supply change on the existing token.

Evidence from existing functions/source:
`HumanStandardToken` constructor sets `totalSupply`; no later function modifies it.

Business impact:
The service only supports fixed-supply tokens.

### Missing Behavior 8: Event History, Audit, and Transaction Search

Priority:
Important robustness gap

Expected business goal:
Clients should inspect approvals, transfers, and historical token activity.

Why it is unsupported:
The API returns only the event from the current transaction and exposes no event log search.

Existing functions considered:
- `transfer owned tokens`, `transfer approved tokens from an owner`, `create spending approval`: return current transaction event only.
- `retrieve address token balance`, `retrieve spending allowance`: expose current state only.

Missing capability:
Endpoints for transfer history, approval history, transaction receipt lookup, filtering by address, and block range.

Proof that function composition is insufficient:
Current balance and allowance cannot reconstruct historical sequence, failed attempts, or transaction-level audit.

Evidence from existing functions/source:
No controller method queries logs except extracting events from the just-submitted receipt.

Business impact:
Reconciliation, audit, compliance, and user activity views are blocked.

### Missing Behavior 9: Validate Token Contract Ownership or Provenance

Priority:
Important robustness gap

Expected business goal:
The service should distinguish contracts it deployed from arbitrary addresses.

Why it is unsupported:
Every contract-scoped endpoint loads whatever `contractAddress` the caller supplies.

Existing functions considered:
- `retrieve token name`, `transfer owned tokens`, `create spending approval`: all accept raw `contractAddress`.
- `deploy ERC-20 token`: does not persist deployed addresses for later validation.

Missing capability:
Deployment registry, provenance check, or bytecode/interface verification endpoint.

Proof that function composition is insufficient:
Without stored deployment records or verification logic, later calls cannot prove whether an address came from this service.

Evidence from existing functions/source:
`HumanStandardToken.load(contractAddress, ...)` is called directly with path input.

Business impact:
Clients can accidentally or intentionally operate against unrelated compatible contracts.

### Missing Behavior 10: Batch Token Summary Retrieval

Priority:
API ergonomics gap

Expected business goal:
Fetch token name, symbol, decimals, version, total supply, and configured sender balance in one call.

Why it is unsupported:
Each field is exposed as a separate endpoint.

Existing functions considered:
- `retrieve token name`, `retrieve token symbol`, `retrieve token decimal precision`, `retrieve token version`, `retrieve total token supply`, `retrieve address token balance`: each reads one field.

Missing capability:
A summary endpoint such as `GET /{contractAddress}` or `GET /{contractAddress}/summary`.

Proof that function composition is insufficient:
Multiple calls can assemble the summary, but not atomically or efficiently through one API operation.

Evidence from existing functions/source:
Controller defines only field-specific read endpoints.

Business impact:
Clients perform extra round trips and must handle partial reads.

## Cross-Behavior Observations

- The service is effectively a single-account ERC-20 wallet and deployer because all writes use configured `fromAddress`.
- Deployment is not registered; contract address capture and reuse are client responsibilities.
- Transfers can fail at the contract level while the REST response still returns HTTP 200 and a transaction hash.
- Missing events are the only exposed signal for failed `transfer` or `transferFrom`.
- Approval overwrites are raw ERC-20 semantics; no safe allowance adjustment is implemented.
- `approveAndCall` is only partially usable because receiver setup is external and the Solidity low-level call appears incompatible with standard callback arguments.
- Swagger disagrees with implementation for `TransferFromRequest.value` and the whole `ApproveAndCallRequest` body.
- Swagger lists generic 401/403/404 responses, but the controller does not implement authentication, authorization, or resource-not-found branches.
- The contract does not protect recipient balance overflow in `transfer` and `transferFrom`; Solidity 0.5 does not add automatic overflow checks.
- No event history, token registry, or provenance validation exists.

## Coverage Summary

Supported domain areas:
- service configuration read
- ERC-20 token deployment
- token metadata, supply, balance, and allowance reads
- service-account direct transfers
- service-account approvals and allowance clearing
- delegated transfer only when the configured sender is the approved spender
- optional Quorum private transaction routing via `privateFor`

Partially supported domain areas:
- delegated spending, limited by fixed configured sender identity
- approval-and-callback workflows, limited by external receiver setup and callback implementation concerns
- transaction success reporting, limited to transaction hash plus optional event

Unsupported domain areas:
- multi-user wallet identity and signed per-user transactions
- deployed token listing/search/registry
- minting, burning, or token metadata updates
- robust event history and audit
- safe allowance adjustment
- contract provenance validation
- accurate generated-client support for all write request schemas