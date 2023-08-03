# GAS OPTIMIZATIONS

All the gas optimizations are determined based opcodes and sample tests.

| Count | Issues | Instances | Gas Saved |
| --- | --- | --- | --- |
| [[G-1]](#) | Tightly pack storage variables/optimize the order of variable declaration(total gas saved: 6K gas ) | 3 | 10000 |
| [[G-2]](#) | Using storage instead of memory for structs/arrays saves gas | 21 | 42100 |
| [[G-3]](#) | External function calls should be cached outside the loop | 1 | PerCall (700 gas) |
| [[G-4]](#) | Using calldata instead of memory for read-only arguments in external functions saves gas | 1 | 312 |
| [[G-5]](#) | Use nested if and, avoid multiple check combinations | 5 | 75 |
| [[G-6]](#) | Multiple accesses of a mapping/array should use a local variable cache | 20 | 850 |
| [[G-7]](#) |  Can Make The Variable Outside The Loop To Save Gas | 11 | per loop (40 gas) |
| [[G-8]](#) | Do not calculate constants | 3 | — |
| [[G-9]](#) | With assembly, .call (bool success) transfer can be done gas-optimized | 12 | — |
| [[G-10]](#) | internal functions not called by the contract should be removed to save deployment gas | 1 | — |
| [[G-11]](#) | Shorten the array rather than copying to a new one | 8 | 400 |
| [[G-12]](#) | Use struct dot notation to avoid memory allocation | 3 | 531 |
| [[G-13]](#) | Cache balance out side of the loop to avoid recalculation in each loop | 1 | 100 |

### Total gas saved: 56168

## [G-01] Tightly pack storage variables/optimize the order of variable declaration

Here, the storage variables can be tightly packed by putting data type that can fit together next to each other. Some packing is only possible as the variables involved are timestamps and thus we can reduce their size from uint256 to uint64 safely

`Total Gas save : 10k`

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

`**Vesting.sol` : duration and start variables can be uint128 instead of uint256 : `saves 2000, 1 SLOT`**

- `duration,start` can be changed from `uint256` to `uint128`. The maximum value that a uint128 variable can store is 18,446,744,073,709,551,615 (2^128 - 1). The block timestamp will not reach this value for over 280 billion years. Therefore, a uint128 variable is enough to store the block timestamp for the foreseeable future. `Saves 2000 gas , 1 SLOT`
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

`**Market.sol` : FEE_PRECISION_DECIMALS variable can be uint16 instead of uint256**

`save 1 SLOT`: `2000 gas` 

```diff
File: contracts/markets/Market.sol

82:      uint256 internal EXCHANGE_RATE_PRECISION; //not costant, but can only be set in the 'init' method

83:      uint256 internal constant FEE_PRECISION = 1e5;

-84:      uint256 internal constant FEE_PRECISION_DECIMALS = 5;
+84:      uint16 internal constant FEE_PRECISION_DECIMALS = 5;

129:     bool internal initialized;
```

**`GlpStrategy.sol` : FEE_BPS variable can be uint16 instead of uint256** 

`save 1 SLOT` :  `2000 gas` 

- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L49

```diff
File: /contracts/glp/GlpStrategy.sol

-49:      uint256 internal constant FEE_BPS = 100;
+49:      uint16 internal constant FEE_BPS = 100;
50:      address public feeRecipient;
51:      uint256 public feesPending;
```

## [G-2] Using storage instead of memory for structs/arrays saves gas

### All instances are not found by bot race

Results : `INSTANCES 21`, Saves `44100 GAS`

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a `Gcoldsload (2100 gas)` for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

<details><summary>All instances</summary>    
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
</details>   

## [G-3] External function calls should be cached outside the loop

Results : `INSTANCES` , Saves `700 GAS per call`

When caching external call values, it's generally more efficient to perform the external call outside of any loops to avoid unnecessary redundant calls.

In terms of opcode savings, caching the external call result outside the loop can eliminate repeated opcodes such as `CALL` that would have been used for the external calls within the loop. This can reduce the overall gas consumption.

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

### [G-5] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Saves `15 GAS, Per instance` `=75`

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
    
    - https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L226C8-L226C74
    
    ```solidity
    File: /contracts/Balancer.so
    226: if (!isNative && _ercData.length == 0) revert PoolInfoRequired();
    ```
    

### [G-6] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. **Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

`Gas save: 840 gas` 

- All Instances
    
    `Balancer.sol.rebalance` : `connectedOFTs[_srcOft][_dstChainId]` should be cache
    
    - https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L137C5-L160C6
    
    ```diff
    File: /contracts/Balancer.sol
    
    137: function checker(
    138:        address payable _srcOft,
    139:        uint16 _dstChainId
    140:   ) external view returns (bool canExec, bytes memory execPayload) {
    141:        bytes memory ercData;
    +           ConnectedOFTs storage connectedOFTs = connectedOFTs[_srcOft][_dstChainId];
    142:        if (ITapiocaOFT(_srcOft).erc20() == address(0)) {
    143:            ercData = abi.encode(
    -144:                connectedOFTs[_srcOft][_dstChainId].srcPoolId,
    +145:                connectedOFTs.srcPoolId,
    -145:                connectedOFTs[_srcOft][_dstChainId].dstPoolId
    +145:                connectedOFTs.dstPoolId
    146:            );
    147:        }
    148:
    -149:        canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;
    +149:        canExec = connectedOFTs.rebalanceable > 0;
    150:        execPayload = abi.encodeCall(
    151:            Balancer.rebalance,
    152:            (
    153:                _srcOft,
    154:                _dstChainId,
    155:                1e3, //1% slippage
    -156:                connectedOFTs[_srcOft][_dstChainId].rebalanceable,
    +156:                connectedOFTs.rebalanceable,
    157:                ercData
    158:            )
    159:        );
    160:    }
    ```
    
    - https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L185
    
    ```diff
    File: /contracts/Balancer.sol
    
    172: function rebalance(
    173:        address payable _srcOft,
    174:        uint16 _dstChainId,
    175:        uint256 _slippage,
    176:        uint256 _amount,
    177:        bytes memory _ercData
    178:    )        external
    179:        payable
    180:        onlyOwner
    181:        onlyValidDestination(_srcOft, _dstChainId) onlyValidSlippage(_slippage)
    182:    {
    +           ConnectedOFTs storage connectedOFTs = connectedOFTs[_srcOft][_dstChainId];
    -183:        if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
    +184:        if (connectedOFTs.rebalanceable < _amount)
    184:            revert RebalanceAmountNotSet();
    185:
    186:        //check if OFT is still valid
    187:        if (
    188:            !_isValidOft(
    189:                _srcOft,
    -190:                connectedOFTs[_srcOft][_dstChainId].dstOft,
    +190:                connectedOFTs.dstOft,
    191:                _dstChainId
    192:            )
    193:        ) revert DestinationOftNotValid();
    ....
    -210: connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
    +210: connectedOFTs[_srcOft][_dstChainId].rebalanceable = connectedOFTs - _amount;
    ```
    
    `activeSingularities[singularity]` Should cache
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L276C2-L293C6
    
    ```diff
    File: /contracts/options/TapiocaOptionLiquidityProvision.sol
    
    276: function registerSingularity(
    277:       IERC20 singularity,
    278:        uint256 assetID,
    279:        uint256 weight
    280:    ) external onlyOwner updateTotalSGLPoolWeights {
    281:        require(assetID > 0, "tOLP: invalid asset ID");
    +              ActiveSingularities storage activeSingularities = activeSingularities[singularity];
    282:        require(
    -283:            activeSingularities[singularity].sglAssetID == 0,
    +283:            activeSingularities.sglAssetID == 0,
    284:            "tOLP: already registered"
    285:        );
    286:
    -287:        activeSingularities[singularity].sglAssetID = assetID;
    -288:        activeSingularities[singularity].poolWeight = weight > 0 ? weight : 1;
    +287:        activeSingularities.sglAssetID = assetID;
    +288:        activeSingularities.poolWeight = weight > 0 ? weight : 1;
    289:        sglAssetIDToAddress[assetID] = singularity;
    290:        singularities.push(assetID);
    291:
    292:       emit RegisterSingularity(address(singularity), assetID);
    293:    }
    ```
    
    `paymentTokens[_paymentToken]` should cache
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L344C1-L353C6
    
    ```diff
    File: /contracts/option-airdrop/AirdropBroker.sol
    
    344: function setPaymentToken(
    345:        ERC20 _paymentToken,
    246:        IOracle _oracle,
    347:        bytes calldata _oracleData
    348:    ) external onlyOwner {
    +           PaymentTokens storage paymentToken = paymentTokens[_paymentToken];
    -349:        paymentTokens[_paymentToken].oracle = _oracle;
    -350:        paymentTokens[_paymentToken].oracleData = _oracleData;
    +349:        paymentToken.oracle = _oracle;
    +350:        paymentToken.oracleData = _oracleData;
    351:
    352:        emit SetPaymentToken(_paymentToken, _oracle, _oracleData);
    353:    }
    ```
    
    `paymentTokens[_paymentToken]` should cache
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L458C1-L467C6
    
    ```diff
    File: /contracts/options/TapiocaOptionBroker.sol
    
    458: function setPaymentToken(
    459:        ERC20 _paymentToken,
    460:        IOracle _oracle,
    461:        bytes calldata _oracleData
    462:    ) external onlyOwner {
    +           PaymentTokens storage paymentToken = paymentTokens[_paymentToken];
    -463:        paymentTokens[_paymentToken].oracle = _oracle;
    -464:        paymentTokens[_paymentToken].oracleData = _oracleData;
    +463:        paymentToken.oracle = _oracle;
    +464:        paymentToken.oracleData = _oracleData;
    465:
    466:        emit SetPaymentToken(_paymentToken, _oracle, _oracleData);
    467:    }
    ```
    
    `assets[assetId]` should cache
    
    - https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L434C5-L441C1
    
    ```diff
    File: /contracts/YieldBox.sol
    434: function toShare(uint256 assetId, uint256 amount, bool roundUp) external view returns (uint256 share) {
    +           Asset storage asset = assets[assetId];
    -435:        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    +435:        if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
    436:            share = amount;
    437:        } else {
    -438:           share = amount._toShares(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), roundUp);
    +438:           share = amount._toShares(totalSupply[assetId], _tokenBalanceOf(asset), roundUp);
    439:        }
    440:    }
    ```
    
    `assets[assetId]` should cache
    
    - https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L447C1-L454C1
    
    ```diff
    File: /contracts/YieldBox.sol
    447: function toAmount(uint256 assetId, uint256 share, bool roundUp) external view returns (uint256 amount) {
    +           Asset storage asset = assets[assetId];
    -448:        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    +448:        if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
    449:            amount = share;
    450:        } else {
    -451:           amount = share._toAmount(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), roundUp);
    +451:           amount = share._toAmount(totalSupply[assetId], _tokenBalanceOf(asset), roundUp);
    452:        }
    453:    }
    ```
    
    `assets[assetId]` should cache
    
    - https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L459
    
    ```diff
    File: /contracts/YieldBox.sol
    458: function amountOf(address user, uint256 assetId) external view returns (uint256 amount) {
    +           Asset storage asset = assets[assetId];
    -459:        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    +459:        if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
    460:            amount = balanceOf[user][assetId];
    461:        } else {
    -462:            amount = balanceOf[user][assetId]._toAmount(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), false);
    +462:            amount = balanceOf[user][assetId]._toAmount(totalSupply[assetId], _tokenBalanceOf(asset), false);
    463:        }
    464:    }
    ```
    

### [G-7] Can Make The Variable Outside The Loop To Save Gas

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
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L376
    
    ```solidity
    File: /contracts/option-airdrop/AirdropBroker.sol
    
    376: ERC20 paymentToken = ERC20(_paymentTokens[i]);
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L566
    
    ```solidity
    File: contracts/options/TapiocaOptionBroker.sol
    
    490: ERC20 paymentToken = ERC20(_paymentTokens[i]);
    566: uint256 currentPoolWeight = sglPools[i].poolWeight;
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L73
    
    ```solidity
    File: /contracts/Multicall/Multicall3.sol
    73: uint256 val = calli.value;
    ```
    
    ```solidity
    File: /contracts/YieldBox.sol
    321: uint256 id = ids[i];
    323: uint256 value = values[i];
    346: address to = tos[i];
    347: uint256 share_ = shares[i];
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L488C17-L488C45
    
    ```solidity
    File: /contracts/governance/twTAP.sol
    
    488: uint256 amount = amounts[i];
    ...
    508: uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];
    509: uint256 amount = amounts[i];
    ```
    

### [G-8] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas

<details> <summary>All instances</summary>

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

</details>

### [G-09] With assembly, `.call (bool success)` transfer can be done gas-optimized

`return` data `(bool success,)` has to be stored due to EVM architecture, but in a usage like below, 'out' and 'outsize' values are given (0,0), this storage disappears and gas optimization is provided.

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

- List of Instances
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-bar-audit/contracts/Penrose.sol#L444-L444
    
    ```solidity
    File: contracts/Penrose.sol
    444:             (success[i], result[i]) = mc[i].call(data[i]);
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapiocaz-audit/contracts/TapiocaWrapper.sol#L120-L120
    
    ```solidity
    File: contracts/TapiocaWrapper.sol
    
    120:         (success, result) = payable(_toft).call{value: msg.value}(_bytecode);
    141:             (success, results[i]) = payable(_call[i].toft).call{
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L380-L380
    
    ```solidity
    File: contracts/tOFT/BaseTOFT.sol
    380:         (bool sent, ) = to.call{value: amount}("");
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280-L280
    
    ```solidity
    File: contracts/tOFT/modules/BaseTOFTLeverageModule.sol
    280:         (bool sent, ) = to.call{value: amount}("");
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol#L206-L206
    
    ```solidity
    File: contracts/Magnetar/MagnetarV2.sol
    206:                     _action.call.*length* > 0,
    1045:        (bool success, bytes memory returnData) = target.call(actionCalldata);
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-periph-audit/contracts/Multicall/Multicall3.sol#L51-L51
    
    ```solidity
    File: contracts/Multicall/Multicall3.sol
    51:              (result.success, result.returnData) = calli.target.call(
    79:              (result.success, result.returnData) = calli.target.call{value: val}(
    ```
    
    - https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol#L99-L99
    
    ```solidity
    File: contracts/Swapper/BaseSwapper.sol
    99:          (bool success, bytes memory data) = token.call(
    ```
    
    https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L356-L356
    
    ```solidity
    File: contracts/convex/ConvexTricryptoStrategy.sol
    356:         (bool successEmtptyApproval, ) = token.call(
    
    369:         (bool success, bytes memory data) = token.call(
    ```
    

### [G-10] ****`internal` functions not called by the contract should be removed to save deployment gas**

If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword.

- https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol#L195

```solidity
File: contracts/markets/singularity/SGLStorage.sol

195: function _accrue() internal virtual override {}
```

### [G-11] Shorten the array rather than copying to a new one

Inline-assembly can be used to shorten the array by changing the length slot, so that the entries don't have to be copied to a new, shorter array

`Gas save : 400 gas`

- List of instances
    - https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L627C8-L627C52
    
    ```solidity
    File: /contracts/markets/bigBang/BigBang.sol
    627: address[] memory _users = new address[](1);
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L358
    
    ```solidity
    File: /contracts/markets/singularity/SGLLiquidation.sol
    358: address[] memory _users = new address[](1);
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L170C8-L170C54
    
    ```solidity
    File: /contracts/governance/twTAP.sol
    127: uint256[] memory result = new uint256[](len);
    ```
    
    - https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L134C8-L134C69
    
    ```solidity
    File /contracts/options/TapiocaOptionLiquidityProvision.sol
    134: SingularityPool[] memory pools = new SingularityPool[](len);
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol#L155C8-L155C74
    
    ```solidity
    File: /contracts/balancer/BalancerStrategy.sol
    155: uint256[] memory maxAmountsIn = new uint256[](poolTokens.length); 
    224: uint256[] memory minAmountsOut = new uint256[](poolTokens.length);
    276: uint256[] memory minAmountsOut = new uint256[](poolTokens.length);
    ```
    
    - https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/aave/AaveStrategy.sol#L146C13-L146C56
    
    ```solidity
    File: /contracts/aave/AaveStrategy.sol 
    146: address[] memory tokens = new address[](1);
    ```
    

### [G-12] Use struct `dot` notation to avoid memory allocation

In the instances below, structs values are being assigned via the following method: `Type({field1: value1, field2: value2});`. This method results in a new struct being created in memory with our specified fields. That memory struct is then written to storage. We can leverage the struct `dot` notation to assign values **directly to storage**, thus avoiding allocating memory space for a temporary struct.

`Gas Saved: 531 gas`

- https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L235C7-L240C12

```diff
File: /contracts/Balancer.sol
235: OFTData memory oftData = OFTData({
-236:            srcPoolId: _srcPoolId,
-237:            dstPoolId: _dstPoolId,
-238:            dstOft: _dstOft,
-239:            rebalanceable: 0
+236:            oftData.srcPoolId: _srcPoolId,
+237:            oftData.dstPoolId: _dstPoolId,
+238:            oftData.dstOft: _dstOft,
+239:            oftData.rebalanceable: 0
240:        });
```

- https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L328C8-L338C12

```diff
File: /contracts/governance/twTAP.sol

+327: Participation storage position = participants[_tokenId];
328: participants[tokenId] = Participation({
-329:            averageMagnitude: pool.averageMagnitude,
-330:            hasVotingPower: hasVotingPower,
-331:            divergenceForce: divergenceForce,
-332:            tapReleased: false,
-333:            expiry: uint56(expiry),
-334:            tapAmount: uint88(_amount),
-335:            multiplier: uint24(multiplier),
-336:            lastInactive: uint40(w0),
-337:            lastActive: uint40(w1)
+329:            position.averageMagnitude: pool.averageMagnitude,
+330:            positionhasVotingPower: hasVotingPower,
+331:            position.divergenceForce: divergenceForce,
+332:            position.tapReleased: false,
+333:            position.positionexpiry: uint56(expiry),
+334:            position.tapAmount: uint88(_amount),
+335:            position.multiplier: uint24(multiplier),
+336:            position.lastInactive: uint40(w0),
+337:            position.lastActive: uint40(w1)
338:        });
```

- https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L180C8-L192C16

```diff
File: /contracts/Swapper/UniswapV3Swapper.sol

180: ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
181:            .ExactInputSingleParams({
-182:                tokenIn: tokenIn,
-183:                tokenOut: tokenOut,
-184:                fee: poolFee,
-185:                recipient: swapData.yieldBoxData.depositToYb
-186:                    ? address(this)
-187:                    : to,
-188:                deadline: deadline,
-189:                amountIn: amountIn,
-190:                amountOutMinimum: amountOutMin,
-191:                sqrtPriceLimitX96: 0
+182:                params.tokenIn: tokenIn,
+183:                params.tokenOut: tokenOut,
+184:                params.fee: poolFee,
+185:                params.recipient: swapData.yieldBoxData.depositToYb
+186:                    ? address(this)
187:                    : to,
+188:                params.deadline: deadline,
+189:                params.amountIn: amountIn,
+190:                params.amountOutMinimum: amountOutMin,
+191:                params.sqrtPriceLimitX96: 0
192:            });
```

### [G-13] Cache balance out side of the loop to avoid recalculation in each loop

`**BaseTapOFT.sol._claimRewards`  :  `IERC20(rewardTokens[i]).balanceOf(address(this))`  should cache for loop** 

`save 100 gas`

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L235

```solidity
File: /contracts/tokens/BaseTapOFT.sol

198: function _claimRewards(
199:        uint16 _srcChainId,
200:        bytes memory _payload
201:    ) internal virtual {
202:        (
....
228: for (uint i = 0; i < len; ) {
229:                ISendFrom(address(rewardTokens[i])).sendFrom{
230:                    value: rewardClaimSendParams[i].ethValue
231:                }(
232:                    address(this),
233:                    _srcChainId,
234:                    LzLib.addressToBytes32(to),
235:                    IERC20(rewardTokens[i]).balanceOf(address(this)), //@audit balance should cache
236:                    rewardClaimSendParams[i].callParams
237:                );
238:                ++i;
239:            }
```

```diff
File: /contracts/tokens/BaseTapOFT.sol

198: function _claimRewards(
199:        uint16 _srcChainId,
200:        bytes memory _payload
201:    ) internal virtual {
202:        (
....
+          uint256 rewardToken = IERC20(rewardTokens[i]).balanceOf(address(this))
228: for (uint i = 0; i < len; ) {
229:                ISendFrom(address(rewardTokens[i])).sendFrom{
230:                    value: rewardClaimSendParams[i].ethValue
231:                }(
232:                    address(this),
233:                    _srcChainId,
234:                    LzLib.addressToBytes32(to),
-235:                    IERC20(rewardTokens[i]).balanceOf(address(this)),
+235:                    rewardToken,
236:                    rewardClaimSendParams[i].callParams
237:                );
238:                ++i;
239:            }
```