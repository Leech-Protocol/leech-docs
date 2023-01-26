# Leech Protocol Smart Contracts

### Farm

The Farm is the main smart contract of the Leech Protocol. <br>
The contract is responsible for managing farm pools data, allowing users to make deposits and withdrawals.

### Supported Networks:

1.  **Mainnet BSC:** [Proxy](https://bscscan.com/address/0xe7dce0fc5017e171d87f647d79a8494747c39190#readProxyContract) | [Implementation](https://bscscan.com/address/0xffe9a7794bdc6502d4fb1d67323aab967a5bd306#code#F1#L1).
2.  **Mainnet AVAX:** [Proxy](https://snowtrace.io/address/0xe7dce0fc5017e171d87f647d79a8494747c39190#readProxyContract) | [Implementation](https://snowtrace.io/address/0xffe9a7794bdc6502d4fb1d67323aab967a5bd306#code#F1#L1).

### Dependencies:

-   **FarmCore:** Store and provide information about all core contract data.
-   **Context:** Provides information about the current execution context.
-   **Ownable:** Provides a basic access control mechanism.
-   **IERC20:** Interface of the ERC20 standard as defined in the EIP.

### Roles:

-   **Owner** is able to set deposit and withdrawal addresses of farm pools and start or end farm to farm moving. Also, owner can add and remove admins.
-   **Admin** is able to change most of the contract data, except for those protected by the `onlyOwner` modifier. Also, admin can pause most of the changes in the contract, block and unblock users.
-   **User** is able to make deposits and withdrawals.

### Constructor Arguments (Farm):

Instead of a constructor, the `initialize()` initialization method is used:

-   Set `itialized` to `true`;
-   Set caller address as `owner`;
-   Set caller address as `admin`;
-   Set `MINTVL`, `CAPY`, `MINAPY`, `servicePercent` initial values;

### Key Contract Modifier:

-   `modifier onlyOwner()`;
    -   Throws if called by any account other than the owner.
-   `modifier onlyWhenServiceEnabled()`;
    -   Throws if called when variable (`serviceDisabled`) is equals (`true`).
-   `modifier onlyAdmin()`;
    -   Throws if called by any account other than the admin.

### Only Owner Callable Contract Key Methods:

-   `function addAdmin(address _address) public onlyWhenServiceEnabled onlyOwner`;
    -   Gives administrator rights to the address.
    -   Can only be called by the current owner.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function removeAdmin(address _address, uint256 _index) external onlyWhenServiceEnabled onlyOwner`;
    -   Removes administrator rights from the address.
    -   Can only be called by the current owner.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function setDepositAddress(uint256 _farmPoolId, address _address) external onlyWhenServiceEnabled onlyOwner`;
    -   Set deposit address of specific farm pool.
    -   Can only be called by the current owner.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function setWithdrawAddress(uint256 _farmPoolId, address _address) external onlyWhenServiceEnabled onlyOwner`;
    -   Set withdraw address of specific farm pool.
    -   Can only be called by the current owner.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function startFarmToFarm(uint256 _farmPoolId, uint256 _newFarmPairId) external onlyWhenServiceEnabled onlyOwner`;
    -   Set new farm pair of specific farm pool.
    -   Set `isFarmMoving` to `true` of specific farm pool.
    -   Can only be called by the current owner.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).

### Only Admin Callable Contract Key Methods:

-   `function setFarmService( uint256 _id, string memory _name, Network _network, bool _isActive ) external onlyWhenServiceEnabled onlyAdmin`;
    -   Adds or update (`FarmService`) object.
    -   Can only be called by the admin address.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function setFarmPair( uint256 _id, uint256 _farmServiceId, uint256 _farmPoolId, address _contractAddress, Network _network, bool _isActive ) external onlyWhenServiceEnabled onlyAdmin`;
    -   Adds or update (`Farm Pair`) object.
    -   Can only be called by the admin address.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function setFarmPool( uint256 _id, uint256 _farmPairId, string memory _name, IERC20 _firstToken, IERC20 _secondToken, bool _isActive ) external onlyWhenServiceEnabled onlyAdmin`;
    -   Adds or update (`Farm Pool`) object.
    -   Can only be called by the admin address.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function disableService() external onlyAdmin`;
    -   Disable all callable methods of service except (`enableService()`).
    -   Can only be called by the admin.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function enableService() external onlyAdmin`;
    -   Enable all callable methods of service.
    -   Can only be called by the admin.
        <br> &nbsp;
-   `blockUser(address _address) external onlyWhenServiceEnabled onlyAdmin`;
    -   Block user by address.
    -   Can only be called by the admin.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).
        <br> &nbsp;
-   `function unblockUser(address _address) external onlyWhenServiceEnabled onlyAdmin`;
    -   Unblock user by address.
    -   Can only be called by the admin.
    -   Can only be called when (`serviceDisabled`) is equals (`false`).

### All Callable Contract Key Methods:

-   `function deposit( uint256 _amountFirst, uint256 _amountSecond, uint256 _farmPoolId, address _referral ) external onlyWhenServiceEnabled`;
    -   Transfers the amount of tokens to the current deposit pool. If its called by new address then new user will be created. Creates new object of (`Deposit`) struct and emits a {`NewDeposit`} event.
    -   To call this method, certain conditions are required, as described below: - Checks if user isn't blocked. - Checks if (`_amountFirst`) or (`_amountSecond`) is greater than zero. - Checks if contact has required amount of token for transfer from current caller. - Checks if farm pool is active. - Checks if farm pool is not moving.
        <br> &nbsp;
-   `function withdraw( uint256 _farmPoolId, uint256 _amountFirst, uint256 _amountSecond, address _referral ) external onlyWhenServiceEnabled`;
    -   Creates new object of (`Withdrawal`) struct. If its called by new address then new user will be created. Can be called an infinite number of times. Balances are validated in API. Emits a {`NewWithdraw`} event.
    -   To call this method, certain conditions are required, as described below:
        -   Checks if user isn't blocked.
        -   Checks if (`_amountFirst`) or (`_amountSecond`) is greater than zero.
        -   Checks if farm pool is not moving.

### Private and Internal Contract Key Methods:

-   `function createNewUser(address _referral) private`;
    -   Create new (`User`) object by address.
    -   Emits a {NewUser} event.
        <br> &nbsp;
-   `function _transferTokens(IERC20 _token, address _depositAddress, uint256 _amount) internal virtual`;
    -   Checks allowance.
    -   Transfer ERC20 tokens to deposit address.
    -   Internal function without access restriction.

### Getter Contract Key Methods:

-   `function getUserDeposit( address _userAddress, uint256 _farmPoolId ) external view returns (Deposit memory)`;
    -   Return User deposits info by address and farm pool ID.
        <br> &nbsp;
-   `function getUserWithdraw( address _userAddress, uint256 _withdrawId ) external view returns (Withdrawal memory)`;
    -   Return User withdraw info by address and user withdraw ID.
