# Leech Protocol Smart Contracts


## LeechRouterOptimism

LeechRouterOptimism is a contract that users use to make deposits and withdrawals to preferred pools. 
User interaction with this contract must proceed only on the official website https://app.leechprotocol.com/.
DO NOT USE THIS CONTRACT DIRECTLY OR THROUGH THE BLOCKCHAIN EXPLORER!


The contract can accept only allowed tokens that are major stablecoins. Currently accepted ones are USDT and USDC. 
 
### Supported Networks:


1. **Mainnet BSC:**
2. **Mainnet Avalanche:**
3. **Mainnet Optimism:**


### Roles:


- **finalizer** is a role held by the backend for finalizing cross-chain deposits and withdrawals.
- **validator** - is a transaction Signer that validates a requested transaction is allowed. 
- **ADMIN_ROLE** is eligible for controlling all the main contract settings and crucial functions such as migration, migrateAllocations, crossChainMigration, finalizeCrosschainMigration, setValidator, setFinalizer, setStrategy, setPool, setRouter, setTransporter, setWithdrawDelay, switchWhitelistStatus, setTreasury, setBanned, setDepositToken.
The Admin role must be held by a multisignature wallet. 
- **PAUSER_ROLE** is eligible to pause all deposits and withdrawals.


# Solidity API


## Helpers

__                   __
      / /   ___  ___  _____/ /_
     / /   / _ \/ _ \/ ___/ __ \
    / /___/  __/  __/ /__/ / / / v.0.2-beta
   /_____/\___/\___/\___/_/ /_/           __
   / __ \_________  / /_____  _________  / /
  / /_/ / ___/ __ \/ __/ __ \/ ___/ __ \/ /
 / ____/ /  / /_/ / /_/ /_/ / /__/ /_/ / /
/_/   /_/   \____/\__/\____/\___/\____/_/

### DENOMINATOR

```solidity
uint16 DENOMINATOR
```

For decimals (1 = 0.01).

### MAX_FEE

```solidity
uint16 MAX_FEE
```

Leech Protocol fee is limited by 20%.

### PercentExeedsMaximalValue

```solidity
error PercentExeedsMaximalValue()
```

Percent is more than denominator or max fee amount.

### calcFee

```solidity
function calcFee(uint256 amount, uint16 fees) external pure returns (uint256)
```

Calc protocol fee amount.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Full amount. |
| fees | uint16 | Slippage percent. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Fee amount. |

### withSlippage

```solidity
function withSlippage(uint256 amount, uint16 slippage) external pure returns (uint256)
```

Calc minAmount for tokens swap.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | Full amount. |
| slippage | uint16 | Slippage tolerance percentage (1% = 100). |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Minimal token amount after swap. |

### toAddress

```solidity
function toAddress(bytes b) external pure returns (address decoded)
```

Converts "abi.encode(address)" string back to address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| b | bytes | Bytes with address. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| decoded | address | Recovered address. |

### rescue

```solidity
function rescue(contract IERC20 token) external
```

Emergency withdraw for stuck tokens.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | Token instance. |

### approveAll

```solidity
function approveAll(contract IERC20 token, address to) external
```

Approve tokens for external contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | Token instance. |
| to | address | Address to be approved. |

## LeechRouterBase

__                   __
      / /   ___  ___  _____/ /_
     / /   / _ \/ _ \/ ___/ __ \
    / /___/  __/  __/ /__/ / / / v.0.2-beta
   /_____/\___/\___/\___/_/ /_/           __
   / __ \_________  / /_____  _________  / /
  / /_/ / ___/ __ \/ __/ __ \/ ___/ __ \/ /
 / ____/ /  / /_/ / /_/ /_/ / /__/ /_/ / /
/_/   /_/   \____/\__/\____/\___/\____/_/

The Router is the main protocol contract, for user interactions and protocol automatizations.

### FEE_MAX

```solidity
uint256 FEE_MAX
```

20% withdrawal fee limitation.

### FEE_DECIMALS

```solidity
uint256 FEE_DECIMALS
```

withdrawal fee decimals.

### ADMIN_ROLE

```solidity
bytes32 ADMIN_ROLE
```

Core team multi-sig.

### PAUSER_ROLE

```solidity
bytes32 PAUSER_ROLE
```

Malicious actions observers.

### transporter

```solidity
contract ILeechTransporter transporter
```

Bridge interface abstraction.

### baseToken

```solidity
contract IERC20 baseToken
```

base protocol stablecoin.

### chainId

```solidity
uint256 chainId
```

chain ID of the current network.

### treasury

```solidity
address treasury
```

Protocol fee receiver.

### validator

```solidity
address validator
```

Signature validator address (EOA).

### uniV2

```solidity
address uniV2
```

Uniswap based router for simple exchanges.

### finalizer

```solidity
address finalizer
```

Finalizer role.

### withdrawDelay

```solidity
uint248 withdrawDelay
```

Withdraw delay in blocks for security reasons.

### whitelistEnabled

```solidity
bool whitelistEnabled
```

Switcher for the signature based input validation.

### depositMinAmount

```solidity
mapping(address => uint256) depositMinAmount
```

Token minimal deposit amount.

### pools

```solidity
mapping(uint16 => struct ILeechRouter.Pool) pools
```

Pools list.

### strategies

```solidity
mapping(uint16 => struct ILeechRouter.Strategy) strategies
```

Mapping will return active strategy struct for the specific pool id.

### routers

```solidity
mapping(uint256 => struct ILeechRouter.Router) routers
```

Mapping will return active router struct for the specific chain id.

_Get router by chainId._

### banned

```solidity
mapping(address => bool) banned
```

_Mapping for disabling malicious actors._

### enabled

```solidity
modifier enabled(address user)
```

Modifier allows exlude banned addresses from execution, even if the valid signature exists.

### isActivePool

```solidity
modifier isActivePool(uint16 poolId)
```

Check pool id.

### checkWhitelist

```solidity
modifier checkWhitelist(bytes signature, uint256 maxBlockNumber)
```

Limit access for early adopters.

### zeroAddr

```solidity
modifier zeroAddr(address checkAddr)
```

Prohibit zero address.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| checkAddr | address | Address to check. |

### zeroVal

```solidity
modifier zeroVal(uint256 checkVal)
```

Prohibit zero amount.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| checkVal | uint256 | Value to check. |

### checkDepositAmount

```solidity
modifier checkDepositAmount(contract IERC20 depositToken, uint256 amount)
```

Check deposit token for requirments.

### checkDelay

```solidity
modifier checkDelay(uint16 poolId, address user)
```

Check withdraw delay.

### onlyFinalizer

```solidity
modifier onlyFinalizer()
```

Only for finalizer service.

### initialize

```solidity
function initialize(contract IERC20 _baseToken, address _uniV2Address, address _treasury, address _validator, address _finalizer) public virtual
```

Initializer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _baseToken | contract IERC20 | Base stablecoin. |
| _uniV2Address | address | Dex router address. |
| _treasury | address | Fees receiver. |
| _validator | address | Whitelist validator. |
| _finalizer | address | Crosschain finalizer service. |

### deposit

```solidity
function deposit(uint16 poolId, contract IERC20 depositToken, uint256 amount, bytes signature, uint256 maxBlockNumber, bytes data) external payable virtual
```

User deposit method.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Selected pool ID. |
| depositToken | contract IERC20 | Deposited token address. |
| amount | uint256 | Amount of deposited token. |
| signature | bytes | Signed message by the back-end. |
| maxBlockNumber | uint256 | Max block for the signature expiring. |
| data | bytes | Additional pool data. |

### crosschainDeposit

```solidity
function crosschainDeposit(uint16 poolId, contract IERC20 depositToken, contract IERC20 bridgedToken, uint256 amount, bytes signature, uint256 maxBlockNumber) external payable virtual
```

User crosschain deposit method.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Selected pool ID. |
| depositToken | contract IERC20 | Deposited token address. |
| bridgedToken | contract IERC20 | Token for the crosschain bridging. |
| amount | uint256 | Amount of deposited token. |
| signature | bytes | Signed message by the back-end. |
| maxBlockNumber | uint256 | Max block for the signature expiring. |

### finalizeDeposit

```solidity
function finalizeDeposit(address user, uint256 amount, uint16 poolId, bytes data) external
```

After bridging completed we need to place tokens to farm.

_Used only to finalize cross-chain deposits._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| user | address | User address who performed a cross-chain deposit. |
| amount | uint256 | Amount of base token. |
| poolId | uint16 | Pool Id. |
| data | bytes | Additional data. |

### withdraw

```solidity
function withdraw(uint16 poolId, contract IERC20 tokenOut, uint256 shares, uint256 maxBlockNumber, bytes signature, bytes data) external payable
```

Withdraw from the pool.
Due to the cross-chain architecture of the protocol, share prices are stored on the BE side.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Selected pool ID. |
| tokenOut | contract IERC20 | Withdrwalas token address. Filtering on the BE side. |
| shares | uint256 | Amount of withdrawal token. |
| maxBlockNumber | uint256 | Max block for the signature expiring. |
| signature | bytes | Signed message by the back-end. |
| data | bytes | Additional data. |

### crosschainWithdraw

```solidity
function crosschainWithdraw(uint16 poolId, contract IERC20 tokenOut, uint256 shares, uint256 maxBlockNumber, bytes signature) external payable
```

User creates crosschain withdrawal request.
Due to the cross-chain architecture of the protocol, share prices are stored on the BE side.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Selected pool ID. |
| tokenOut | contract IERC20 | Withdrwalas token address. Filtering on the BE side. |
| shares | uint256 | Amount of withdrawal token. |
| maxBlockNumber | uint256 | Max block for the signature expiring. |
| signature | bytes | Signed message by the back-end. |

### finalizeWithdrawal

```solidity
function finalizeWithdrawal(uint16 poolId, uint256 shares, address user, contract IERC20 tokenOut, uint256 targetChainId, bytes data) external payable
```

BE calls after WithdrawalRequested event was catched.
Should be called on chain with active strategy

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Pool Id. |
| shares | uint256 | Amount in "want" token on strategy: LP or single token. |
| user | address | User address. |
| tokenOut | contract IERC20 | Withdrwalas token address. Filtering on the BE side. |
| targetChainId | uint256 | Chain ID of the network where the withdrawal requests was created. |
| data | bytes | Additional parameters. |

### migration

```solidity
function migration(uint16 poolId, uint16 newStrategyId, uint16 slippage, bytes data) external virtual
```

Move liquidity to the new strategy.

_Should be called only on active strategy chain._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Pool Id to be migrated. |
| newStrategyId | uint16 | Move to this strategy. |
| slippage | uint16 | Wanted slippage for swaps. |
| data | bytes | Additional params. |

### crosschainMigration

```solidity
function crosschainMigration(uint256 targetChainId, uint16 poolId, uint16 newStrategyId, uint16 slippage, bytes data) external payable
```

Move liquidity to another blockchain.

_Should be called only on active strategy chain._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| targetChainId | uint256 | Move assets to this chainId. |
| poolId | uint16 | Pool Id to be migrated. |
| newStrategyId | uint16 | Move to this strategy. |
| slippage | uint16 | Wanted slippage for swaps. |
| data | bytes | Additional params. |

### migrateAllocations

```solidity
function migrateAllocations(uint16 poolId, address[] users, uint256[] amounts) external
```

2nd additional step for change strategy
Should be called only if pool total allocation on this chain is equal to zero.

_Can be called multiple times if arrays is too large._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | ID of the pool. |
| users | address[] | Array of users to be migrated. |
| amounts | uint256[] | Array of users amounts. |

### finalizeCrosschainMigration

```solidity
function finalizeCrosschainMigration(uint16 poolId, uint16 newStrategyId, uint256 baseAmount, bytes data) external
```

3rd additional step for change strategy
Should be called only if cross-chain migration was initialized and tokens received.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Pool Id to be migrated. |
| newStrategyId | uint16 | Id of the target strategy. |
| baseAmount | uint256 | Migrated amount in base stable token. |
| data | bytes | Additional params. |

### setValidator

```solidity
function setValidator(address _validator) external
```

Validator setter. Only multisig role.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _validator | address | New validator. |

### setFinalizer

```solidity
function setFinalizer(address _finalizer) external
```

Finalizer setter. Only multisig role.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _finalizer | address | New finalizer. |

### setStrategy

```solidity
function setStrategy(uint16 strategyId, struct ILeechRouter.Strategy _strategy) external
```

Strategy setter. Only multisig role.

_Should be invoked with special caution to prevent overriding active strategies.
For the active strategies with non-zero ballance migration should be used._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| strategyId | uint16 | ID of the strategy |
| _strategy | struct ILeechRouter.Strategy | Tupple of the new strategy. |

### setPool

```solidity
function setPool(uint16 poolId, uint16 strategyId, uint256 _chainId) external
```

Pool setter. Only multisig role.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Id of the pool. |
| strategyId | uint16 | Id of the strategy, related to current pool. |
| _chainId | uint256 | Pool's blockchain |

### setRouter

```solidity
function setRouter(uint256 _chainId, struct ILeechRouter.Router _router) external
```

Router setter. Only multisig role.

_Should be invoked with special caution to prevent overriding active routers._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _chainId | uint256 | Chain Id. |
| _router | struct ILeechRouter.Router | Tupple of the router. |

### setTransporter

```solidity
function setTransporter(address _transporter) external
```

Transporter setter. Only multisig role.

_Should be invoked with special caution to prevent blocking cross-chain operations._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _transporter | address | New transporter. |

### setWithdrawDelay

```solidity
function setWithdrawDelay(uint248 newDelay) external
```

Set withdraw delay.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| newDelay | uint248 | New delay in blocks. |

### unpause

```solidity
function unpause() external
```

@notice Disable pause. Only multisig role.

### switchWhitelistStatus

```solidity
function switchWhitelistStatus() external
```

@notice On/Off signature based validation. Only multisig role.

### setUniV2

```solidity
function setUniV2(address _uniV2Address) external
```

Uniswap based router setter. Only multisig role.

_Should be invoked with special caution to prevent blocking cross-chain operations._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _uniV2Address | address | New uniV2 address. |

### setTreasury

```solidity
function setTreasury(address _treasury) external
```

Fee receiver setter. Only multisig role.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _treasury | address | New treasury address. |

### setBanned

```solidity
function setBanned(address _user, bool _status) external
```

Function for disable and enable specific user

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _user | address | User addess |
| _status | bool | Boolean status |

### setDepositToken

```solidity
function setDepositToken(address _token, uint256 _minAmount) external
```

Function for disable and enable specific user

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _token | address | Token address |
| _minAmount | uint256 | Min amount of token with decimals. 0 to disable token. |

### pause

```solidity
function pause() external
```

@notice Panic mode ON.
 @dev Only security observers can call this function.

### quotePotentialWithdraw

```solidity
function quotePotentialWithdraw(uint16 poolId, uint256 shares, address[] token0toTokenOut, address[] token1toTokenOut, bytes data) external view returns (uint256 amountOut)
```

Calc potential withdraw amount from pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | ID of the pool. |
| shares | uint256 | Pool shares amount to withdraw. |
| token0toTokenOut | address[] | May be used in some strategies. |
| token1toTokenOut | address[] | May be used in some strategies. |
| data | bytes | Additional params. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| amountOut | uint256 | Amount converted from shares value to base token amount. |

### userAllocation

```solidity
function userAllocation(uint16 poolId, address user) external view returns (uint256)
```

Pool balance of user.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | ID of the pool. |
| user | address | User's address. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | User allocation. |

### totalAllocation

```solidity
function totalAllocation(uint16 poolId) external view returns (uint256)
```

Pool total allocation.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | ID of the pool. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Total allocation. |

### base

```solidity
function base() external view returns (address)
```

Base token of current router.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | address | Address of the base token. |

### balance

```solidity
function balance(uint16 poolId) public view returns (uint256)
```

Amount of LPs staked into Masterchef.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Id of the Leech pool. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Strategy allocation of the pool. |

### _approveAll

```solidity
function _approveAll(contract IERC20 token, address to) internal
```

Approve tokens for external contract.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| token | contract IERC20 | Token instance. |
| to | address | Address to be approved. |

### _swap

```solidity
function _swap(uint256 amount, contract IERC20 tokenIn, contract IERC20 tokenOut) internal virtual returns (uint256 swapedAmount)
```

_Use uniswapV2Router for simple swaps. Used for the cross-chain operations._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | TokenIn amount. |
| tokenIn | contract IERC20 | Swap from this token. |
| tokenOut | contract IERC20 | Swap to this token. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| swapedAmount | uint256 | TokenOut amount. |

## LeechRouterOptimism

__                   __
      / /   ___  ___  _____/ /_
     / /   / _ \/ _ \/ ___/ __ \
    / /___/  __/  __/ /__/ / / / v.0.2-beta
   /_____/\___/\___/\___/_/ /_/           __
   / __ \_________  / /_____  _________  / /
  / /_/ / ___/ __ \/ __/ __ \/ ___/ __ \/ /
 / ____/ /  / /_/ / /_/ /_/ / /__/ /_/ / /
/_/   /_/   \____/\__/\____/\___/\____/_/

The Router is the main protocol contract, for user interactions and protocol automatizations.

### routes

```solidity
mapping(contract IERC20 => mapping(contract IERC20 => struct IRouterVelodrome.route[])) routes
```

Paths for token swaps.

_tokenFrom => tokenTo => Array with routes_

### initialize

```solidity
function initialize(contract IERC20 _baseToken, address _uniV2Address, address _treasury, address _validator, address _finalizer) public
```

Initializer.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _baseToken | contract IERC20 | Base stablecoin. |
| _uniV2Address | address | Dex router address. |
| _treasury | address | Fees receiver. |
| _validator | address | Whitelist validator. |
| _finalizer | address | Crosschain finalizer. |

### setRoutes

```solidity
function setRoutes(contract IERC20 tokenFrom, contract IERC20 tokenTo, struct IRouterVelodrome.route[] paths) public
```

Set routes for token swaps.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokenFrom | contract IERC20 | Token in. |
| tokenTo | contract IERC20 | Token out. |
| paths | struct IRouterVelodrome.route[] | Array with routes. |

### _swap

```solidity
function _swap(uint256 amount, contract IERC20 tokenIn, contract IERC20 tokenOut) internal returns (uint256 swapedAmount)
```

_Use uniswapV2Router for simple swaps. Used for the cross-chain operations._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | TokenIn amount. |
| tokenIn | contract IERC20 | Swap from this token. |
| tokenOut | contract IERC20 | Swap to this token. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| swapedAmount | uint256 | TokenOut amount. |


## StrategyVelodromeFarm

StrategyVelodromeFarm is one of the protocol’s strategy contracts. It receives user funds from LeechRouter and generates yield from the Velodrom farm.
Only the LeechRouter contract can deposit to and withdraw from the strategy. 


### Supported Networks:
1. **Mainnet Optimism:**


### Roles:
- **owner** has control over all the main settings: fee, slippage, token exchange routes, controller address, treasury address.
- **controller** is the backend address.
- **router** is the LeechRouter contract that can call deposits, withdrawals and migrate deposited funds from the strategy to a new strategy. 

# Solidity API

## BaseFarmStrategy

__                   __
      / /   ___  ___  _____/ /_
     / /   / _ \/ _ \/ ___/ __ \
    / /___/  __/  __/ /__/ / / / v.0.2-beta
   /_____/\___/\___/\___/_/ /_/           __
   / __ \_________  / /_____  _________  / /
  / /_/ / ___/ __ \/ __/ __ \/ ___/ __ \/ /
 / ____/ /  / /_/ / /_/ /_/ / /__/ /_/ / /
/_/   /_/   \____/\__/\____/\___/\____/_/

### MAX_FEE

```solidity
uint16 MAX_FEE
```

The protocol fee limit is 12%.

### DENOMINATOR

```solidity
uint16 DENOMINATOR
```

Used for fractional part (1 = 0.01)

### controller

```solidity
address controller
```

Address of Leech's backend.

### router

```solidity
address router
```

Address of LeechRouter.

### treasury

```solidity
address treasury
```

Treasury address.

### protocolFee

```solidity
uint16 protocolFee
```

Leech's comission.

### totalAllocation

```solidity
uint256 totalAllocation
```

Sum of all pools shares.

### slippage

```solidity
uint16 slippage
```

Swap slippage.

### allocationOf

```solidity
mapping(uint16 => uint256) allocationOf
```

Share of pool

_poolId => allocPoints_

### onlyRouter

```solidity
modifier onlyRouter()
```

_Limit access for the LeechRouter only._

### notZeroAmount

```solidity
modifier notZeroAmount(uint256 amountToCheck)
```

_Unsigned integer should be great than zero._

### autocompound

```solidity
function autocompound(uint16) public virtual
```

Take fees and re-invests rewards.

### migrate

```solidity
function migrate(uint16 pool, uint16 _slippage, bytes data) external returns (uint256 amountOut)
```

Move liquidity to another strategy.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| pool | uint16 | Pool ID. |
| _slippage | uint16 | Slippage tolerance. |
| data | bytes | Additional params. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| amountOut | uint256 | Withdraw token amount. |

### deposit

```solidity
function deposit(uint16 poolId, contract IERC20 depositToken, bytes data) public virtual returns (uint256 share)
```

Depositing into the farm pool.

_Only LeechRouter can call this function.
Re-entrancy lock on the LeechRouter side._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Pool identifier. |
| depositToken | contract IERC20 | Incoming token. |
| data | bytes | Additional data. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| share | uint256 | Deposit allocation. |

### withdraw

```solidity
function withdraw(uint16 poolId, uint256 shares, contract IERC20 tokenOut, bytes data) public virtual returns (uint256 tokenOutAmount)
```

Withdrawing staking token (LP) from the strategy.

_Can only be called by LeechRouter.
Re-entrancy lock on the LeechRouter side._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint16 | Pool identifier. |
| shares | uint256 | Amount of the strategy shares to be withdrawn. |
| tokenOut | contract IERC20 | Token to be swapped to. |
| data | bytes | Output token encoded to bytes string. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokenOutAmount | uint256 | Amount of the token returned to LeechRouter. |

### setFee

```solidity
function setFee(uint16 _fee) external virtual
```

Sets fee taken by the Leech protocol.

_Only owner can set the protocol fee._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _fee | uint16 | Fee value. |

### setTreasury

```solidity
function setTreasury(address _treasury) external virtual
```

Sets the tresury address.

_Only owner can set the treasury address._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _treasury | address | The address to be set. |

### setController

```solidity
function setController(address _controller) external virtual
```

Sets the controller address.

_Only owner can set the controller address._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _controller | address | The address to be set. |

### setSlippage

```solidity
function setSlippage(uint16 _slippage) external virtual
```

Sets slippage tolerance.

_Only owner can set the slippage tolerance._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _slippage | uint16 | Slippage percent (1 == 0.01%). |

### quotePotentialWithdraw

```solidity
function quotePotentialWithdraw(uint256 shares, address[] token0toTokenOut, address[] token1toTokenOut, bytes data, uint256 price0, uint256 price1) public view virtual returns (uint256 amountOut)
```

Function returns estimated amount of token out from the LP withdrawn LP amount.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| shares | uint256 | Amount of shares. |
| token0toTokenOut | address[] | Path to output token. |
| token1toTokenOut | address[] | Path to output token. |
| data | bytes | Additional params. |
| price0 | uint256 | Price of token0. |
| price1 | uint256 | Price of token1. |

### base

```solidity
function base() public view virtual returns (address)
```

Address of base token.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | address | Base token address. |

### balance

```solidity
function balance() public view virtual returns (uint256 amount)
```

Amount of LPs staked into Masterchef.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| amount | uint256 | LP amount. |

### claimable

```solidity
function claimable() public view virtual returns (address[] tokens, uint256[] amounts)
```

Amounts of pending rewards.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokens | address[] | Array of reward tokens. |
| amounts | uint256[] | Array of reward amounts. |

### _deposit

```solidity
function _deposit(bytes, contract IERC20) internal virtual returns (uint256 share)
```

_Depositing into the farm pool._

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| share | uint256 | External pool deposit LP amount. |

### _withdraw

```solidity
function _withdraw(uint256, contract IERC20, bytes, uint16) internal virtual returns (uint256 tokenOutAmount)
```

_Withdrawing staking token (LP) from the strategy._

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokenOutAmount | uint256 | Amount of the token returned to LeechRouter. |


## StrategyVelodromeFarm

__                   __
      / /   ___  ___  _____/ /_
     / /   / _ \/ _ \/ ___/ __ \
    / /___/  __/  __/ /__/ / / / v.0.2-beta
   /_____/\___/\___/\___/_/ /_/           __
   / __ \_________  / /_____  _________  / /
  / /_/ / ___/ __ \/ __/ __ \/ ___/ __ \/ /
 / ____/ /  / /_/ / /_/ /_/ / /__/ /_/ / /
/_/   /_/   \____/\__/\____/\___/\____/_/

Only for the stable pairs on Velodrome V1.

### VELODROME_ROUTER

```solidity
contract IRouterVelodrome VELODROME_ROUTER
```

Velodrome dex router

### USDC

```solidity
contract IERC20 USDC
```

USD Cirle token.

### USDT

```solidity
contract IERC20 USDT
```

Tether token.

### VELO

```solidity
contract IERC20 VELO
```

Pool reward.

### token0

```solidity
contract IERC20 token0
```

First token of the pair.

### token1

```solidity
contract IERC20 token1
```

Second token of the pair.

### lp

```solidity
contract IVelodromePair lp
```

Liquidity provider token.

### gauge

```solidity
contract IGauge gauge
```

Pair gauge.

### poolId

```solidity
uint16 poolId
```

External pool id.

### routes

```solidity
mapping(contract IERC20 => mapping(contract IERC20 => struct IRouterVelodrome.route[])) routes
```

Token exchnage route paths

_tokenIn => tokenOut => Velodrome routes array_

### initialize

```solidity
function initialize(struct IStrategyMasterchefFarmV2.InstallParams params, contract IERC20 _token0, contract IERC20 _token1, contract IVelodromePair _lp, contract IGauge _gauge, uint16 _poolId) external
```

Executes by contract deployment.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| params | struct IStrategyMasterchefFarmV2.InstallParams | General strategy parameters. |
| _token0 | contract IERC20 | First token of the pair. |
| _token1 | contract IERC20 | Second token of the pair. |
| _lp | contract IVelodromePair | LP token. |
| _gauge | contract IGauge |  |
| _poolId | uint16 | Id of the farmed Velodrome pair. |

### autocompound

```solidity
function autocompound(uint16) public
```

Re-invests rewards.

### setRoutes

```solidity
function setRoutes(contract IERC20 tokenIn, contract IERC20 tokenOut, struct IRouterVelodrome.route[] routesList) external
```

Sets paths for token swap.

_Only owner can set a paths._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokenIn | contract IERC20 | From token. |
| tokenOut | contract IERC20 | To token. |
| routesList | struct IRouterVelodrome.route[] | Velodrome routes array. |

### _deposit

```solidity
function _deposit(bytes, contract IERC20 depositToken) internal returns (uint256 shares)
```

Depositing into the farm pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | bytes |  |
| depositToken | contract IERC20 | Token in. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| shares | uint256 | Pool share of user. |

### _withdraw

```solidity
function _withdraw(uint256 shares, contract IERC20 withdrawToken, bytes, uint16) internal returns (uint256 tokenOutAmount)
```

Withdrawing staking token (LP) from the strategy.

_Can only be called by LeechRouter._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| shares | uint256 | Amount of the strategy shares to be withdrawn. |
| withdrawToken | contract IERC20 |  |
|  | bytes |  |
|  | uint16 |  |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokenOutAmount | uint256 | Amount of the token returned to LeechRouter. |

### balance

```solidity
function balance() public view returns (uint256)
```

Amount of LPs staked into Masterchef.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | uint256 | Amount in want token. |

### quotePotentialWithdraw

```solidity
function quotePotentialWithdraw(uint256 shares, address[], address[], bytes, uint256, uint256) public view returns (uint256 amountOut)
```

Function returns estimated amount of token out from the LP withdrawn LP amount.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| shares | uint256 | Amount of shares. |
|  | address[] |  |
|  | address[] |  |
|  | bytes |  |
|  | uint256 |  |
|  | uint256 |  |

### claimable

```solidity
function claimable() public view returns (address[] tokens, uint256[] amounts)
```

Amount of pending rewards

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| tokens | address[] | Array of reward tokens. |
| amounts | uint256[] | Array of reward amounts. |

