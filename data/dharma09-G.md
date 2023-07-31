## [G-01] Tightly pack storage variables/optimize the order of variable declaration(total gas saved: 4K gas )

Here, the storage variables can be tightly packed by putting data type that can fit together next to each other. Some packing is only possible as the variables involved are timestamps and thus we can reduce their size from uint256 to uint64 safely

- All
    
    ### Save 2 slots (4k gas): Pack lastEposchUpdate and epoch
    
    **lastEpochUpdate and epoch are timestamps thus we can use uint64 which would be safe for around 532 years**
    
    ```solidity
    File: /contracts/option-airdrop/AirdropBroker.sol
    
    43: contract AirdropBroker is Pausable, BoringOwnable, FullMath {
    44:    bytes public tapOracleData;
    45:    TapOFT public immutable tapOFT;
    46:    AOTAP public immutable aoTAP;
    47:    IOracle public tapOracle;
    48:    IERC721 public immutable PCNFT;
    
    50:    uint256 public epochTAPValuation; // TAP price for the current epoch
    51:    uint256 public lastEpochUpdate; // timestamp of the last epoch update
    52:    uint256 public epoch; // Represents the number of weeks since the start of the contract
    
    54:    mapping(ERC20 => PaymentTokenOracle) public paymentTokens; // Token address => PaymentTokenOracle
    55:    address public paymentTokenBeneficiary; // Where to collect the payment tokens
    ```
    
    **Mitigation**
    
    ```diff
    File: /contracts/option-airdrop/AirdropBroker.sol
    
    43: contract AirdropBroker is Pausable, BoringOwnable, FullMath {
    44:    bytes public tapOracleData;
    45:    TapOFT public immutable tapOFT;
    46:    AOTAP public immutable aoTAP;
    47:    IOracle public tapOracle;
    48:    IERC721 public immutable PCNFT;
    
    -50:    uint128 public epochTAPValuation; // TAP price for the current epoch
    -51:    uint64 public lastEpochUpdate; // timestamp of the last epoch update
    -52:    uint64 public epoch; // Represents the number of weeks since the start of the contract
    +50:    uint128 public epochTAPValuation; // TAP price for the current epoch
    +51:    uint64 public lastEpochUpdate; // timestamp of the last epoch update
    +52:    uint64 public epoch; // Represents the number of weeks since the start of the contract
    
    54:    mapping(ERC20 => PaymentTokenOracle) public paymentTokens; // Token address => PaymentTokenOracle
    55:    address public paymentTokenBeneficiary; // Where to collect the payment tokens
    ```
    

- https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L20C5-L26C29

```diff
File: /contracts/Vesting.sol

16: /// @notice the vested token
17:    IERC20 public token;
18:
19:    /// @notice returns the start time for vesting
-20:    uint256 public start;
+20:    uint128 public start;
+
+  /// @notice returns total vesting duration
+    uint128 public duration;
21:
22:    /// @notice returns the cliff period
23:    uint256 public cliff;
24:
-25:    /// @notice returns total vesting duration
-26:    uint256 public duration;
27:
28:    /// @notice returns total available tokens
29:    uint256 public seeded = 0;
....
-67: constructor(uint256 _cliff, uint256 _duration, address _owner) {
+67: constructor(uint256 _cliff, uint128 _duration, address _owner) {
```

## [G-2] Using storage instead of memory for structs/arrays saves gas

### All instances are not found by bot race

Results : `INSTANCES 21`, Saves `8400 GAS`

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a `Gcoldsload (2100 gas)` for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

- All instances
    
    ### Using `storage` variables can potentially save gas by `eliminating the cost of copying the struct from storage to memory`
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L172
    
    ```diff
    File: /contracts/governance/twTAP.sol
    
    -172 : Participation memory position = participants[_tokenId];
    +172 : Participation storage position = participants[_tokenId];
    
    -526 : Participation memory position = participants[_tokenId];
    +526 : Participation storage position = participants[_tokenId];
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L115
    
    ```diff
    File: /contracts/options/TapiocaOptionLiquidityProvision.sol
    
    -115: LockPosition memory lockPosition = lockPositions[_tokenId];
    +115: LockPosition storage lockPosition = lockPositions[_tokenId];
    
    -131: uint256[] memory _singularities = singularities;
    +131: uint256[] storage _singularities = singularities;
    
    -134: SingularityPool[] memory pools = new SingularityPool[](len);
    +134: SingularityPool[] storage pools = new SingularityPool[](len);
    
    -214: LockPosition memory lockPosition = lockPositions[_tokenId];
    +214: LockPosition storage lockPosition = lockPositions[_tokenId];
    
    -304: uint256[] memory _singularities = singularities;
    +304: uint256[] storage _singularities = singularities;
    
    -349: LockPosition memory lockPosition = lockPositions[tokenId];
    +349: LockPosition storage lockPosition = lockPositions[_tokenId];
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/LiquidationQueue/LiquidationQueue.sol
    
    ```diff
    File: /contracts/LiquidationQueue/LiquidationQueue.sol
    
    -145: OrderBookPoolInfo memory poolInfo = orderBookInfos[pool];
    +145: OrderBookPoolInfo storage poolInfo = orderBookInfos[pool];
    
    -157: OrderBookPoolInfo memory poolInfo = orderBookInfos[pool];
    +157: OrderBookPoolInfo storage poolInfo = orderBookInfos[pool];
    
    -220: OrderBookPoolInfo memory poolInfo = orderBookInfos[pool];
    +220: OrderBookPoolInfo storage poolInfo = orderBookInfos[pool];
    
    -321: Bidder memory bidder = bidPools[pool].users[user];
    +321: Bidder storage bidder = bidPools[pool].users[user];
    
    -330: OrderBookPoolInfo memory poolInfo = orderBookInfos[pool];
    +330: OrderBookPoolInfo storage poolInfo = orderBookInfos[pool];
    
    -775: OrderBookPoolInfo memory poolInfo = orderBookInfos[pool];
    +775: OrderBookPoolInfo storage poolInfo = orderBookInfos[pool];
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol
    
    ```diff
    File: /contracts/options/TapiocaOptionBroker.sol
    
    -165: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
    166:            _paymentToken
    167:        ];
    +165: PaymentTokenOracle storage paymentTokenOracle = paymentTokens[
    166:            _paymentToken
    167:        ];
    
    -222: TWAMLPool memory pool = twAML[lock.sglAssetID];
    +222: TWAMLPool storage pool = twAML[lock.sglAssetID];
    
    -308: Participation memory participation = participants[oTAPPosition.tOLP];
    +308: Participation storage participation = participants[oTAPPosition.tOLP];
    
    -312: TWAMLPool memory pool = twAML[lock.sglAssetID];
    +312: TWAMLPool storage pool = twAML[lock.sglAssetID];
    
    -363: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
    364:            _paymentToken
    365:        ];
    +363: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
    364:            _paymentToken
    365:        ];
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L223
    
    ```diff
    File: contracts/usd0/BaseUSDO.sol
    
    -223: ICommonData.IApproval[] memory approvals
    +223: ICommonData.IApproval[] storage approvals
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol
    
    ```diff
    File: /contracts/governance/twTAP.sol
    
    361: function claimAndSendRewards(
            uint256 _tokenId,
    -        IERC20[] memory _rewardTokens
        ) external {
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L523
    
    ```diff
    File: /contracts/option-airdrop/AirdropBroker.sol
    
    -162: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
    163:            _paymentToken
    164:        ];
    +162: PaymentTokenOracle storage paymentTokenOracle = paymentTokens[
    163:            _paymentToken
    164:        ];
    
    -237: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
    238:            _paymentToken
    239:        ];
    +237: PaymentTokenOracle storage paymentTokenOracle = paymentTokens[
    238:            _paymentToken
    239:        ];
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L166
    
    ```diff
    File: /contracts/tokens/BaseTapOFT.sol
    
    163: function claimRewards(
            address to,
            uint256 tokenID,
    -        address[] memory rewardTokens,
            uint16 lzDstChainId,
            address zroPaymentAddress,
            bytes calldata adapterParams,
            IRewardClaimSendFromParams[] calldata rewardClaimSendParams
        ) external payable {
    ```
    

## [G-3] External function calls should be cached outside the loop

Results : `INSTANCES` , Saves `700 GAS per call`

When caching external call values, it's generally more efficient to perform the external call outside of any loops to avoid unnecessary redundant calls.

In terms of opcode savings, caching the external call result outside the loop can eliminate repeated opcodes such as `CALL` that would have been used for the external calls within the loop. This can reduce the overall gas consumption.

- All
    - https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L219C1-L221C48
    
    `_getRevertMsg(result)` **external call should be cached outside the loop to avoid redundant calls saves at least `700 GAS` for every iterations.**
    
    ```solidity
    File: contracts/markets/bigBang/BigBang.sol
    
    209: function execute(
    210:        bytes[] calldata calls,
    211:        bool revertOnFail
    212:    ) external returns (bool[] memory successes, string[] memory results) {
    213:        successes = new bool[](calls.length);
    214:        results = new string[](calls.length);
    215:        for (uint256 i = 0; i < calls.length; i++) {
    216:            (bool success, bytes memory result) = address(this).delegatecall(
    217:                calls[i]
    218:            );
    219:            require(success || !revertOnFail, _getRevertMsg(result)); //@audit cache outside the loop
    220:            successes[i] = success;
    221:            results[i] = _getRevertMsg(result); //@audit cache outside the loop
    222:        }
    223:    }
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L191C1-L194C10
    
    ```solidity
    File: /contracts/markets/singularity/Singularity.sol
    181: function execute(
    182:        bytes[] calldata calls,183:
    183:        bool revertOnFail
    184:    ) external returns (bool[] memory successes, string[] memory results) {
    185:        successes = new bool[](calls.length);
    186:        results = new string[](calls.length);
    187:        for (uint256 i = 0; i < calls.length; i++) {
    188:            (bool success, bytes memory result) = address(this).delegatecall(
    189:                calls[i]
    190:            );
    191:            require(success || !revertOnFail, _getRevertMsg(result)); //@audit cache outside the loop
    192:            successes[i] = success;
    193:            results[i] = _getRevertMsg(result); //@audit cache outside the loop
    194:        }
    195:    }
    ```
    

### [G-04]  Using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode () step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution

**Saves `312 GAS per iteration`**

- https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L163C3-L167C29

```diff
File: /contracts/tokens/BaseTapOFT.sol

163: function claimRewards(
164:        address to,
165:        uint256 tokenID,
-166:        address[] memory rewardTokens,
+166:        address[] calldata rewardTokens,
167:        uint16 lzDstChainId,
```

**can be declared as constant to save large volume of gas**

The `maxSupply` value not changed any where inside the contract. This value only used for checking `totalSupply() + amount <= maxSupply`

The maxSupply variable can be declared as a constant to save gas. This is because constants are stored in memory, which is a much cheaper operation than storing variables in storage.

The expression 100_000_000 * 1e18 evaluates to a very large number, which would require a lot of gas to store in storage. By declaring maxSupply as a constant, we can avoid this gas cost.

- https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L42

```solidity
File: /contracts/tokens/TapOFT.sol
42: uint256 public dso_supply = 53_313_405 * 1e18;
```

### [G-5] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Saves `15 GAS, Per instance`

- All
    - https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L611C3-L614C12
    
    ```solidity
    File: /contracts/Magnetar/modules/MagnetarMarketModule.sol
    
    611:        if (
    612:            !removeAndRepayData.assetWithdrawData.withdraw &&
    613:            removeAndRepayData.repayAssetOnBB
    614:        ) {
    
    ```
    
    - https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/BoringMath.sol#L27C6-L28C22
    
    ```solidity
    File: contracts/BoringMath.sol
    
    27: if (roundUp && (result * div) / mul < value) {
    28:            result++;
    29:        }
    ```
    
    - https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxRebase.sol#L35C9-L37C10
    
    ```solidity
    File: contracts/YieldBoxRebase.sol
    
    35: if (roundUp && (share * totalAmount) / totalShares_ < amount) {
    36:            share++;
    37:        }
    ...
    58: if (roundUp && (amount * totalShares_) / totalAmount < share) {
    59:            amount++;
    60:        }
    ```
    

## [G-6] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. **Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

- All Instances
    
    `connectedOFTs[_srcOft][_dstChainId]` should be cache
    
    - https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L185
    
    ```diff
    File: /contracts/Balancer.sol
    
    172: function rebalance(
            address payable _srcOft,
            uint16 _dstChainId,
            uint256 _slippage,
            uint256 _amount,
            bytes memory _ercData
        )
            external
            payable
            onlyOwner
            onlyValidDestination(_srcOft, _dstChainId)
            onlyValidSlippage(_slippage)
        {
            if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
                revert RebalanceAmountNotSet();
    
            //check if OFT is still valid
            if (
                !_isValidOft(
                    _srcOft,
                    connectedOFTs[_srcOft][_dstChainId].dstOft,
                    _dstChainId
                )
            ) revert DestinationOftNotValid();
    ```
    

## [G-7] Can Make The Variable Outside The Loop To Save Gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

- All
    
    ```solidity
    contract MyContract {
        function sum(uint256[] memory values) public pure returns (uint256) {
            uint256 total = 0;
            
            for (uint256 i = 0; i < values.length; i++) {
                total += values[i];
            }
            
            return total;
        }
    }
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L566
    
    ```solidity
    File: contracts/options/TapiocaOptionBroker.sol
    
    566: uint256 currentPoolWeight = sglPools[i].poolWeight;
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L73
    
    ```solidity
    File: /contracts/Multicall/Multicall3.sol
    73: uint256 val = calli.value;
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L488C17-L488C45
    
    ```solidity
    File: /contracts/governance/twTAP.sol
    
    488: uint256 amount = amounts[i];
    
    508: uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];
    509: uint256 amount = amounts[i];
    ```
    

## [G-8] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas

- All instances
    
    ```solidity
    File:  /contracts/governance/twTAP.sol
    
    82:    uint256 constant dMAX = 100 * 1e4; // 10% - 100% voting power multiplier
    83:    uint256 constant dMIN = 10 * 1e4;
    ....
    95:    uint256 constant DIST_PRECISION = 2 ** 128;
    ```
    
    ```solidity
    File: /contracts/option-airdrop/AirdropBroker.sol
    69: uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4;
    85: uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;
    93: uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;
    ```
    
    ```solidity
    File: /contracts/tokens/TapOFT.sol
    41: uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO
    42:    uint256 public dso_supply = 53_313_405 * 1e18;
    ```
    

### [G-09] ****`internal` functions not called by the contract should be removed to save deployment gas**

If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword.

- https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol#L195

```solidity
File: contracts/markets/singularity/SGLStorage.sol

195: function _accrue() internal virtual override {}
```


