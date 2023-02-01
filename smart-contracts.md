# Leech Protocol Smart Contracts

## Farm

This early beta is intended for a narrow circle of ambassadors. The operation of this contract is closely
related to the centralized infrastructure of the protocol at the moment, at least as long as this smart contract is
active. You can check if the contract is active by calling `stats()` function of this contract. Therefore, direct
interaction with this contract outside the user interface is not provided. To avoid loss of funds, use this
contract only from the official page https://app.leechprotocol.com/.

DO NOT USE THIS CONTRACT DIRECTLY OR THROUGH THE BLOCKCHAIN EXPLORER!

_Has owner role (single account) driven by `onlyOwner` modifier (OperZeppeln Ownable.sol).
Has admins role (multiple accounts) driven by `onlyAdmin` modifier.
This contract does not hold any user funds. Don't send your funds directly to the contract!
All ERC20 tokens are well known stablecoins._

### Supported Networks:

1.  **Mainnet BSC:** [soon](https://bscscan.com/address/0x...#readProxyContract).
2.  **Mainnet AVAX:** [soon](https://snowtrace.io/address/0x...#readProxyContract).
3.  **Mainnet ETH:** [soon](https://snowtrace.io/address/0x...#readProxyContract).

### Dependencies:

-   **Ownable:** Provides a basic access control mechanism.
-   **IERC20:** Interface of the ERC20 standard as defined in the EIP.

### Roles:

-   **Owner** is able to set deposit and withdrawal addresses of farm pools and start or end farm to farm moving. Also, owner can add and remove admins.
-   **Admin** is able to change most of the contract data, except for those protected by the `onlyOwner` modifier. Also, admin can pause most of the changes in the contract, block and unblock users.
-   **User** is able to make deposits and withdrawals.

## API Documentation

### users

```solidity
mapping(address => struct IFarm.User) users
```

Mapping data for quick access by index or address.

### farmServices

```solidity
mapping(uint64 => struct IFarm.FarmService) farmServices
```

Get `FarmService` by id

### farmPairs

```solidity
mapping(uint64 => struct IFarm.FarmPair) farmPairs
```

Get `FarmPair` by id

### farmPools

```solidity
mapping(uint64 => struct IFarm.FarmPool) farmPools
```

Get `FarmPool` by id

### isAdmin

```solidity
mapping(address => bool) isAdmin
```

Checking is address belongs to the admin

### deposits

```solidity
mapping(address => mapping(uint64 => struct IFarm.Transfer[])) deposits
```

Get user deposits by farmPoolId

### withdrawals

```solidity
mapping(address => mapping(uint64 => struct IFarm.Transfer[])) withdrawals
```

Get user withdrawals by farmPoolId

### enabled

```solidity
modifier enabled()
```

_Throws if called when variable (`serviceDisabled`) is equals (`true`)._

### onlyAdmin

```solidity
modifier onlyAdmin()
```

_Throws if called by any account other than the admin._

### allowed

```solidity
modifier allowed()
```

_Throws if account is banned._

### notZeroAmount

```solidity
modifier notZeroAmount(uint256 one, uint256 two)
```

_Throws if zero amount provided._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| one | uint256 | Amount of the first token. |
| two | uint256 | Amount of the second token. |

### farmPoolIsStatic

```solidity
modifier farmPoolIsStatic(uint64 poolId)
```

_Throws if farm pool is moving._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| poolId | uint64 | Id if the farm pool. |

### deposit

```solidity
function deposit(uint256 _amountFirst, uint256 _amountSecond, uint64 _farmPoolId, address _referral) external
```

Make deposit to farm pool.

_Attention! At this stage of the development of the project (Stage 1), we process deposits only from a
limited number of ambassadors. This check takes place on the backend side, so if you are not one of the project
ambassadors, your deposit will not be credited, and the refund will be difficult.

To verify that you are eligible to make a deposit, please use the client area at https://app.leechprotocol.com/

All ERC20 is trusted, well known stablecoins. Reentrancy attack is imposible.

Writing to the state is used to store statistics and does not affect the operation of the protocol at this stage
of development.

Service should be enabled
User should be allowed to make deposits and withdrawals
Amounts at least of one of the token should be great than zero
Pool isn't mooving right now_

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _amountFirst | uint256 | Amount of the first token in luquidity pair |
| _amountSecond | uint256 | Amount of the second token in luquidity pair |
| _farmPoolId | uint64 | Id of the farm_pool instance on the BE side |
| _referral | address | An address of user's referal partner |

### withdraw

```solidity
function withdraw(uint64 _farmPoolId, uint256 _amountFirst, uint256 _amountSecond) external
```

Withdraw from pool.

_Attention! Calling this function signals to the backend that the user intends to leave the pool.
Applications are processed on the backend once a day. At this stage of development, this function must be called
through the web application at https://app.leechprotocol.com/, otherwise the output will not be processed.

Writing to the state is used to store statistics and does not affect the operation of the protocol at this stage
of development.

Service should be enabled
User should be allowed to make deposits and withdrawals
Amounts at least of one of the token should be great than zero
Pool isn't mooving right now_

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | Id of the farm_pool instance on the BE side |
| _amountFirst | uint256 | Amount of the first token in luquidity pair |
| _amountSecond | uint256 | Amount of the second token in luquidity pair |

### setServiceStatus

```solidity
function setServiceStatus(bool _isEnabled) external
```

Enables and disables all callable methods of service.

_Can only be called by the admin address._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _isEnabled | bool | Is service enabled |

### ban

```solidity
function ban(address _address, bool _isBanned) external
```

Ban or unban account.

_Can only be called by the admin address.
Service should be enabled_

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _address | address | Address of a user to be blocked |
| _isBanned | bool |  |

### setFarmService

```solidity
function setFarmService(uint64 _id, uint56 _network, bool _isActive) external
```

Adds or updates (`FarmService`) object.

_Can only be called by the admin address.
Service should be enabled_

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _id | uint64 | Id of the targeted farm service |
| _network | uint56 | Network id. |
| _isActive | bool | Is FarmService is active. |

### setFarmPair

```solidity
function setFarmPair(uint64 _id, uint64 _farmServiceId, uint64 _farmPoolId, bool _isActive) external
```

Adds or updates (`Farm Pair`) object.

_Can only be called by the admin address.
Service should be enabled_

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _id | uint64 | Id if the farm pair |
| _farmServiceId | uint64 | Id of the corresponding farm service |
| _farmPoolId | uint64 | Id of the corresponding pool |
| _isActive | bool | Status of the pair |

### setFarmPool

```solidity
function setFarmPool(uint64 _id, uint256 _firstTokenMin, uint256 _secondTokenMin, uint256 _farmPairId, contract IERC20 _firstToken, contract IERC20 _secondToken, bool _isActive) external
```

Adds or updates (`Farm Pool`) object.

_Service should be enabled
Can only be called by the admin address._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _id | uint64 | Farm pool Id. |
| _firstTokenMin | uint256 | Minimal first token deposi amount |
| _secondTokenMin | uint256 | Minimal first token deposi amount |
| _farmPairId | uint256 | Farm pair Id. |
| _firstToken | contract IERC20 | Address of the first token. |
| _secondToken | contract IERC20 | Address of the second token . |
| _isActive | bool | Status of the pool. |

### setAdmin

```solidity
function setAdmin(address _account, bool _isAdmin) external
```

Set administrator rights to the address.

_Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _account | address | Address of the user |
| _isAdmin | bool | Is this accounts has admin role |

### setDepositAddress

```solidity
function setDepositAddress(uint64 _farmPoolId, address _address) external
```

Sets deposit address.

_Service should be enabled
Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | ID of the corresponding farming pool. |
| _address | address | Deposit address for the selected pool. |

### setWithdrawAddress

```solidity
function setWithdrawAddress(uint64 _farmPoolId, address _address) external
```

Sets withdraw address.

_Service should be enabled
Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | ID of the corresponsing  pool. |
| _address | address | Withdrawal address of the corresponding farming pool. |

### startFarmToFarm

```solidity
function startFarmToFarm(uint64 _farmPoolId, uint64 _newFarmPairId) external
```

Starts moving farm to farm.

_Service should be enabled
Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | Id of the pool. |
| _newFarmPairId | uint64 | Id of the new farm. |

### endFarmToFarm

```solidity
function endFarmToFarm(uint64 _farmPoolId) external
```

Ends moving farm to farm.

_Service should be enabled
Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | Id of the pool |

### recoverERC20

```solidity
function recoverERC20(contract IERC20 _token, address _user, uint256 _amount) external
```

Recover ERC20 tokens.

_NEVER SEND TOKENS DIRECTLY TO THIS CONTRACT!
This contract does not store tokens or other funds. This function is used to return tokens sent by mistake back
to the sender.
Can only be called by the current owner._

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _token | contract IERC20 | ERC20 token |
| _user | address | Token receiver |
| _amount | uint256 | Amount of tokens |

### stats

```solidity
function stats() external view returns (uint64 farmPairsCount, uint64 farmPoolsCount, uint64 farmServicesCount, uint56 adminsCount, bool serviceDisabled)
```

Returns all contract stats.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| farmPairsCount | uint64 | Total farm pairs amount |
| farmPoolsCount | uint64 | Total farm pools amount |
| farmServicesCount | uint64 | Total farm services amount |
| adminsCount | uint56 | Total active admins amount |
| serviceDisabled | bool | Is service on maintance or outdated |

### getDeposits

```solidity
function getDeposits(address _account, uint64 _poolId) external view returns (struct IFarm.Transfer[])
```

Returns all user deposits to the pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _account | address | User address |
| _poolId | uint64 | Farming pool id |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct IFarm.Transfer[] | Array of user deposits |

### getWithdrawals

```solidity
function getWithdrawals(address _account, uint64 _poolId) external view returns (struct IFarm.Transfer[])
```

Returns all user withdrawals from the pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _account | address | User address |
| _poolId | uint64 | Farming pool id |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct IFarm.Transfer[] | Array of user withdrawals |

## IFarm

### FarmService

```solidity
struct FarmService {
  uint56 network;
  bool isActive;
}
```

### FarmPair

```solidity
struct FarmPair {
  uint64 farmServiceId;
  uint64 farmPoolId;
  bool isActive;
}
```

### FarmPool

```solidity
struct FarmPool {
  uint256 farmPairId;
  uint256 firstTokenMin;
  uint256 secondTokenMin;
  address depositAddress;
  address withdrawAddress;
  contract IERC20 firstToken;
  contract IERC20 secondToken;
  bool isActive;
  bool isFarmMoving;
}
```

### Transfer

```solidity
struct Transfer {
  uint256 amountFirst;
  uint256 amountSecond;
}
```

### User

```solidity
struct User {
  address referral;
  bool isBlocked;
  uint120 depositCount;
  uint120 withdrawCount;
}
```

### NewDeposit

```solidity
event NewDeposit(address user, uint256 amountFirst, uint256 amountSecond, uint64 farmPoolId)
```

Emitted while user deposit process

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| user | address | Address of the user that makes a deposit |
| amountFirst | uint256 | Amount of the first token in luquidity pair |
| amountSecond | uint256 | Amount of the second token in luquidity pair |
| farmPoolId | uint64 | Id of the farm_pool instance on the BE side |

### NewWithdraw

```solidity
event NewWithdraw(address user, uint256 amountFirst, uint256 amountSecond, uint64 farmPoolId)
```

Emitted while user deposit process

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| user | address | Address of the user that makes a withdrawal |
| amountFirst | uint256 | Amount of the first token in luquidity pair |
| amountSecond | uint256 | Amount of the second token in luquidity pair |
| farmPoolId | uint64 | Id of the farm_pool instance on the BE side |

### Ban

```solidity
event Ban(address account, bool isBanned)
```

Emitted while user deposit process

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | Address of the user to be blocked/unblocked |
| isBanned | bool | Is this account banned |

### NewUser

```solidity
event NewUser(address user, address referral)
```

Emitted when new user registered

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| user | address | Address of the new user to be registred |
| referral | address | Address of the new users' referral partner |

### AdminUpdated

```solidity
event AdminUpdated(address account, bool isAdmin)
```

Emitted when a new admin as added

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| account | address | Address of the new admin |
| isAdmin | bool |  |

### ServiceEnabled

```solidity
event ServiceEnabled(bool isEnabled)
```

Emitted when this contract is disabled or enabled

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| isEnabled | bool | Is service currently enabled |

### FarmServiceChanged

```solidity
event FarmServiceChanged(uint64 id, uint56 networkId, bool isActive)
```

Emitted when a farm service is added or updated

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| id | uint64 | Farm service id |
| networkId | uint56 | Network id |
| isActive | bool | Is farm service active |

### FarmPairChanged

```solidity
event FarmPairChanged(uint64 farmPairId, bool isActive)
```

Emitted when a farm pair is added or updated

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| farmPairId | uint64 | Id of the farm pair |
| isActive | bool | If farm pair acctive |

### FarmPoolChanged

```solidity
event FarmPoolChanged(uint64 farmPoolId, address token1, address token2)
```

Emitted when a farm pool is added or updated

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| farmPoolId | uint64 | Id of the pool |
| token1 | address |  |
| token2 | address |  |

### FarmToFarmMovingStart

```solidity
event FarmToFarmMovingStart(uint256 time, uint256 farmPairId)
```

Emitted when a pool migrates to a new farm

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| time | uint256 | Block timestamp when migration started |
| farmPairId | uint256 | Id of the farm pair |

### FarmToFarmMovingEnd

```solidity
event FarmToFarmMovingEnd(uint256 time, uint256 farmPairId)
```

Emitted when the pool ends migration to the new farm

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| time | uint256 | Block timestamp when migration ended |
| farmPairId | uint256 | of the farm pair |

### RestrictedAccess

```solidity
error RestrictedAccess()
```

_Sender in not a admin._

### ServiceDisabled

```solidity
error ServiceDisabled()
```

_Service disabled (maintanance or new version released)._

### Banned

```solidity
error Banned()
```

_Account has ban._

### ZeroAmount

```solidity
error ZeroAmount()
```

_Deposit or withdraw zero amount of tokens._

### FarmToFarmMoving

```solidity
error FarmToFarmMoving()
```

_Assets moving in progress, try again in few minutes._

### Inactive

```solidity
error Inactive()
```

_Pool is disabled or doesn't exist._

### AlreadyExist

```solidity
error AlreadyExist()
```

_If something already exist_

### deposit

```solidity
function deposit(uint256 _amountFirst, uint256 _amountSecond, uint64 _farmPoolId, address _referral) external
```

Make deposit

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _amountFirst | uint256 | Amount of the first token in luquidity pair |
| _amountSecond | uint256 | Amount of the second token in luquidity pair |
| _farmPoolId | uint64 | Id of the farm_pool instance on the BE side |
| _referral | address | An address of user's referal partner |

### withdraw

```solidity
function withdraw(uint64 _farmPoolId, uint256 _amountFirst, uint256 _amountSecond) external
```

Withdraw user liquidity from

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _farmPoolId | uint64 | Id of the farm_pool instance on the BE side |
| _amountFirst | uint256 | Amount of the first token in luquidity pair |
| _amountSecond | uint256 | Amount of the second token in luquidity pair |

### stats

```solidity
function stats() external view returns (uint64 farmPairsCount, uint64 farmPoolsCount, uint64 farmServicesCount, uint56 adminsCount, bool serviceDisabled)
```

Returns all contract stats.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| farmPairsCount | uint64 | Total farm pairs amount |
| farmPoolsCount | uint64 | Total farm pools amount |
| farmServicesCount | uint64 | Total farm services amount |
| adminsCount | uint56 | Total active admins amount |
| serviceDisabled | bool | Is service on maintance or outdated |

### getDeposits

```solidity
function getDeposits(address _account, uint64 _poolId) external view returns (struct IFarm.Transfer[])
```

Returns all user deposits to the pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _account | address | User address |
| _poolId | uint64 | Farming pool id |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct IFarm.Transfer[] | Array of user deposits |

### getWithdrawals

```solidity
function getWithdrawals(address _account, uint64 _poolId) external view returns (struct IFarm.Transfer[])
```

Returns all user withdrawals from the pool.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| _account | address | User address |
| _poolId | uint64 | Farming pool id |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | struct IFarm.Transfer[] | Array of user withdrawals |

## ERC20Token

### constructor

```solidity
constructor(string _name, string _symbol) public
```

### mint

```solidity
function mint(address to, uint256 amount) public
```

