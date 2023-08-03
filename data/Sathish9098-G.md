# GAS OPTIMIZATIONS

| Gas Count | Issues | Instances  | Gas Saved  |
| :------------ | :------------ | :------------ |:------------ |
| [G-1]   | ``State variables`` can be packed into fewer storage slots | 16 |  32000   |
| [G-2]   | ``Structs`` can be packed into fewer storage slots    | 2   | 4000   |
| [G-3]   | Using ``calldata`` instead of ``memory`` for read-only arguments in ``external`` functions saves gas    |  13  | 3102 |
| [G-4]   |  Using ``storage`` instead of ``memory`` for structs/arrays saves gas    | 4  | 16800   |
| [G-5]   | Don't initialize the default values to state or local variables for saving gas      | 15   | 2388 |
| [G-6]   | Optimizing gas usage Order your checks correctly   | 2   | 16000 |
| [G-7]   | Combine events to save Glogtopic (375 gas)     | 7  | 2450    |
| [G-8]   | Cache the ``state variables`` outside the ``loop`` to avoid ``SLOD`` in every iterations | 2  | 200 |
| [G-9]   | Calldata pointer is used instead of memory pointer for loops    | 2   | 34    |
| [G-10]   | Don't ``emit`` ``state`` variables when ``stack`` variable available      | 1    | 100   |
| [G-11]   | Multiple accesses of a ``mapping/array`` should use a local variable cache  | 6    | 600   |
| [G-12]   | ``<x> += <y>`` costs more gas than ``<x> = <x> + <y>`` for state variables ``(SAME TO <x> -= <y>)``  | 13    | 1469 |



[G-12] Consider consolidating the ``addAsset`` and ``removeAsset`` , ``addCollateral``and ``removeCollateral`` related functions that could potentially be consolidated to reduce gas costs

# TRADITIONS GAS FINDINGS 
##

## [G-1] State variables can be packed into fewer storage slots

### Saves ``32000 GAS``, ``16 SLOTs``

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.

### ``Market.sol`` : State variables can be packed less slots : Saves ``16000 GAS``, ``8 SLOT``

``callerFee``,``protocolFee``,``minLiquidatorReward``,``maxLiquidatorReward``,``liquidationBonusAmount``,``collateralizationRate``,``borrowOpeningFee`` state variable values not exceeds ``FEE_PRECISION`` constant value because of this ``callerFee <= FEE_PRECISION, _minLiquidatorReward < FEE_PRECISION,_minLiquidatorReward < FEE_PRECISION,_liquidationBonusAmount < FEE_PRECISION,_collateralizationRate <= FEE_PRECISION,_val <= FEE_PRECISION`` checks in setter function. the state variable values always less than or equal to ``FEE_PRECISION ``. The ``FEE_PRECISION`` constant value is ``1e5``. So ``uint64`` is more than enough instead of ``uint256 ``

-  ``yieldBox,callerFee`` can be packed single ``SLOT``

-  ``penrose,protocolFee`` can be packed single ``SLOT``

-  ``collateral,minLiquidatorReward `` can be packed single ``SLOT``

-  ``asset,maxLiquidatorReward `` can be packed single ``SLOT``

-  ``conservator,liquidationBonusAmount `` can be packed single ``SLOT``

-  ``oracle, collateralizationRate`` can be packed single ``SLOT``

``liquidationMultiplier `` value is not changed inside the contract . The value is always ``12000`` . So ``uint32`` is more than enough instead of ``uint256``

-  ``totalBorrow,borrowOpeningFee,liquidationMultiplier `` can be packed single ``SLOT``


https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L20-L77

```diff
FILE: tapioca-bar-audit/contracts/markets/Market.sol

     /// @notice returns YieldBox address
     YieldBox public yieldBox;
+     uint64 public callerFee; // 90%
     /// @notice returns Penrose address
     IPenrose public penrose;
+    uint64: public protocolFee; // 10%

     /// @notice collateral token address
     IERC20 public collateral;
+    uint64 public minLiquidatorReward = 1e3; //1%
    /// @notice collateral token YieldBox id
    uint256 public collateralId;
    /// @notice asset token address
    IERC20 public asset;
+    uint64 public maxLiquidatorReward = 1e4; //10%
    /// @notice asset token YieldBox id
    uint256 public assetId;

   /// @notice contract's pause state
   bool public paused;
    /// @notice conservator's addresss
   /// @dev conservator can pause/unpause the contract
    address public conservator;
+     uint64 public liquidationBonusAmount = 1e4; //10%

    /// @notice oracle address
   IOracle public oracle;
+     uint64 public collateralizationRate; // 75%
   /// @notice oracleData
    bytes public oracleData;
    /// @notice Exchange and interest rate tracking.
    /// This is 'cached' here because calls to Oracles can be very expensive.
    /// Asset -> collateral = assetAmount * exchangeRate.
    uint256 public exchangeRate;

    /// @notice total amount borrowed
   /// @dev elastic = Total token amount to be repayed by borrowers, base = Total parts of the debt held by borrowers
    Rebase public totalBorrow;
+    uint64 public borrowOpeningFee = 50; //0.05%
+     uint32 public liquidationMultiplier = 12000; //12%
   /// @notice total collateral supplied
    uint256 public totalCollateralShare;
    /// @notice max borrow cap
    uint256 public totalBorrowCap;
    /// @notice borrow amount per user
   mapping(address => uint256) public userBorrowPart;
    /// @notice collateral share per user
    mapping(address => uint256) public userCollateralShare;

    /// @notice liquidation caller rewards
-    uint256 public callerFee; // 90%
    /// @notice liquidation protocol rewards
-    uint256 public protocolFee; // 10%
    /// @notice min % a liquidator can receive in rewards
-     uint256 public minLiquidatorReward = 1e3; //1%
   /// @notice max % a liquidator can receive in rewards
-     uint256 public maxLiquidatorReward = 1e4; //10%
    /// @notice max liquidatable bonus amount
    /// @dev max % added to the amount that can be liquidated
-     uint256 public liquidationBonusAmount = 1e4; //10%
    /// @notice collateralization rate
-     uint256 public collateralizationRate; // 75%
    /// @notice borrowing opening fee
-     uint256 public borrowOpeningFee = 50; //0.05%
   /// @notice liquidation multiplier used to compute liquidator rewards
-     uint256 public liquidationMultiplier = 12000; //12%

```

### ``Penrose.sol`` : ``usdoToken``,``usdoAssetId`` values can be packed in same slot. Saves ``2000 GAS``, ``1 SLOT``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L44-L51

``usdoAssetId`` is always downcast to uint96 when ever assign values. So its safe ``uint96`` instead of ``uint256`` 

```solidity

302: usdoAssetId = uint96(
            yieldBox.registerAsset(
                TokenType.ERC20,
                _usdoToken,
                emptyStrategies[_usdoToken],
                0
            )
        );

```


```diff
FILE: tapioca-bar-audit/contracts/Penrose.sol

45: uint256 public immutable tapAssetId;
46:    /// @notice returns USDO contract
47:    IERC20 public usdoToken;
48:    /// @notice returns USDO asset id registered in the YieldBox contract
- 49:    uint256 public usdoAssetId;
+ 49:    uint96 public usdoAssetId;
50:    /// @notice returns the WETH contract
51:    IERC20 public immutable wethToken;

```

### ``TapiocaOptionBrok`` : ``lastEpochUpdate``,``epoch``,``epochTAPValuation`` can be packed with same 
SLOT : Saves ``4000 GAS``,``2 SLOT``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L60-L62

``epochTAPValuation`` can be uint128, ``lastEpochUpdate`` can be uint64, ``epoch`` can be uint64 as per [AirdropBroker.sol](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L50-L52) contract . So this down casting is perfectly safe. This down casting not affect the protocol  


```diff
FILE: tap-token-audit/contracts/options/TapiocaOptionBroker.sol

- 60:  uint256 public lastEpochUpdate; // timestamp of the last epoch update
+ 60:  uint64 public lastEpochUpdate; // timestamp of the last epoch update
+ 62:  uint64 public epoch; // Represents the number of weeks since the start of the contract
- 61:  uint256 public epochTAPValuation; // TAP price for the current epoch
+ 61:  uint128 public epochTAPValuation; // TAP price for the current epoch
- 62:  uint256 public epoch; // Represents the number of weeks since the start of the contract

```

### ``twTAP.sol`` : ``mintedTWTap``,``lastProcessedWeek`` can be packed with same SLOT : Saves ``2000 GAS``,``1 SLOT``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L106-L108

``uint128`` is more than enough for ``mintedTWTap``,``lastProcessedWeek`` state variables. 

  - ``mintedTWTap`` is incremented always by 1
  - ``lastProcessedWeek`` is dealing timestamp 


```diff
FILE: tap-token-audit/contracts/governance/twTAP.sol

- 106:    uint256 public mintedTWTap;
+ 106:    uint128 public mintedTWTap;
+ 108:    uint128 public lastProcessedWeek;
107:    uint256 public creation; // Week 0 starts here
- 108:    uint256 public lastProcessedWeek;

```
### ``Vesting.sol`` : ``token,start``,``cliff,duration`` can be packed in same SLOT : Saves ``4000 GAS``, ``2 SLOT``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L17-L26

- ``start`` only initialized once in init() function. So ``uint96`` alone more than enough instead of ``uint256``

- For handing time uint128 is is more than enough ``4.2 million`` times more years than the current Unix time.

```diff
FILE: Breadcrumbstap-token-audit/contracts/Vesting.sol

16:  /// @notice the vested token
17:    IERC20 public token;
+ 20:    uint96 public start;
18:
19:    /// @notice returns the start time for vesting
- 20:    uint256 public start;
21:
22:    /// @notice returns the cliff period
- 23:    uint256 public cliff;
+ 23:    uint128 public cliff;
24:
25:    /// @notice returns total vesting duration
- 26:    uint256 public duration;
+ 26:    uint128 public duration;
```

### ``erc20, hostChainID, _decimalCache`` can be packed with same SLOT : Saves ``4000 GAS, 2 SLOTs``

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L26-L32

Other protocols using ``uint32`` for ``ChainID``. So its safer to change ``hostChainID`` to ``uint64`` instead of ``uint256``

```diff
FILE: Breadcrumbstapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol

26:   IYieldBoxBase public yieldBox;
27:    /// @notice The ERC20 to wrap.
28:    address public erc20;
29:    /// @notice The host chain ID of the ERC20
- 30:    uint256 public hostChainID;
+ 30:    uint64 public hostChainID;
31:    /// @notice Decimal cache number of the ERC20.
32:    uint8 internal _decimalCache;


```

##

## [G-2] Structs can be packed into fewer storage slots

### Saves ``4000 GAS``, ``2 SLOTs``

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct.

### ``Vesting.sol`` :``latestClaimTimestamp`` and ``revoked`` can be packed same ``SLOT`` . Saves ``2000 ``, ``1 SLOT``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L32-L37

```diff
FILE: tap-token-audit/contracts/Vesting.sol

``latestClaimTimestamp`` can be ``uint248 `` to accommodate  ``revoked`` in same SLOT. Down casting is not affect protocol in any way . This way followed many of the protocols to avoid extra ``SLOT`` and ``GAS ``

31: /// @notice user vesting data
32:    struct UserData {
33:        uint256 amount;
34:        uint256 claimed;
- 35:        uint256 latestClaimTimestamp;
+ 35:        uint248 latestClaimTimestamp;
36:        bool revoked;
37:    }

```

### ``TapiocaOptionLiquidityProvision.sol`` : ``sglAssetID``,``totalDeposited`` can be ``uint128`` instead of ``uint256`` : Saves ``2000 GAS``, ``1 SLOT``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L34-L38

- Other struct ``LockPosition`` is used ``uint128`` for ``sglAssetID``

- ``totalDeposited`` always add or subtract with ``uint128`` values   ``activeSingularities[_singularity].totalDeposited += _amount;`` 


```diff
FILE: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol

34: struct SingularityPool {
35:    uint256 sglAssetID; // Singularity market YieldBox asset ID
- 36:    uint256 totalDeposited; // total amount of tOLR tokens deposited, used for pool share calculation
+ 36:    uint128 totalDeposited; // total amount of tOLR tokens deposited, used for pool share calculation
- 37:    uint256 poolWeight; // Pool weight to calculate emission
+ 37:    uint128 poolWeight; // Pool weight to calculate emission
38: }

```
##

## [G-3] Using ``calldata`` instead of ``memory`` for read-only arguments in ``external`` functions saves gas

### Saves ``3102 GAS``, ``13 Instances``

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it’s still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract, and cases where a constructor is involved.

### ``Penrose.sol`` : ``data`` can be ``calldata`` instead of ``memory`` : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L424-L428

```diff
FILE: Breadcrumbstapioca-bar-audit/contracts/Penrose.sol

424: function executeMarketFn(
425:        address[] calldata mc,
- 426:        bytes[] memory data,
+ 426:        bytes[] calldata data,
427:        bool forceSuccess
428:    )
429:        external

```

### ``BaseUSDO.sol`` : ``approvals`` can be calldata instead of memory : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L223


```diff
FILE: tapioca-bar-audit/contracts/usd0/BaseUSDO.sol

215: function initMultiHopBuy(
216:        address from,
217:        uint256 collateralAmount,
218:        uint256 borrowAmount,
219:        IUSDOBase.ILeverageSwapData calldata swapData,
220:        IUSDOBase.ILeverageLZData calldata lzData,
221:        IUSDOBase.ILeverageExternalContractsData calldata externalData,
222:        bytes calldata airdropAdapterParams,
- 223:        ICommonData.IApproval[] memory approvals
+ 223:        ICommonData.IApproval[] calldata approvals
224:    ) external payable {


```

### ``approvals`` can be calldata instead of memory : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L156-L164


```diff
FILE : Breadcrumbstapiocaz-audit/contracts/tOFT/BaseTOFT.sol

 function initMultiSell(
        address from,
        uint256 share,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData,
        bytes calldata airdropAdapterParams,
-        ICommonData.IApproval[] memory approvals
+        ICommonData.IApproval[] calldata approvals
    ) external payable {


```

### ``_ercData`` can be changed to ``calldata`` instead of ``memory`` : Saves ``564 GAS ``, ``2 Instances``


https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L177

Here we need to change private function parameter into ``calldata ``

```diff
FILE: tapiocaz-audit/contracts/Balancer.sol

    function rebalance(
        address payable _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
-         bytes memory _ercData
+         bytes calldata _ercData
    )
        external
        payable
        onlyOwner
        onlyValidDestination(_srcOft, _dstChainId)
        onlyValidSlippage(_slippage)
    {

function _sendToken(
        address payable _oft,
        uint256 _amount,
        uint16 _dstChainId,
        uint256 _slippage,
-         bytes memory _data
+         bytes calldata _data
    ) private {


219: function initConnectedOFT(
        address _srcOft,
        uint16 _dstChainId,
        address _dstOft,
-        bytes memory _ercData
+        bytes calldata _ercData
    ) external onlyOwner {

```

### ``airdropAdapterParam`` can be changed to ``calldata`` instead of ``memory`` : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L89-L97


```diff
FILE: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol

89:   function retrieveFromStrategy(
        address _from,
        uint256 amount,
        uint256 share,
        uint256 assetId,
        uint16 lzDstChainId,
        address zroPaymentAddress,
-        bytes memory airdropAdapterParam
+        bytes calldata airdropAdapterParam
    ) external payable {

```

### ``rewardTokens`` can be changed to ``calldata`` instead of ``memory`` : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L166C39-L166C39


```diff
FILE: Breadcrumbstap-token-audit/contracts/tokens/BaseTapOFT.sol

function claimRewards(
        address to,
        uint256 tokenID,
-        address[] memory rewardTokens,
+        address[] calldata rewardTokens,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams,
        IRewardClaimSendFromParams[] calldata rewardClaimSendParams
    ) external payable {

```

### ``adapterParams`` can be changed to ``calldata`` instead of ``memory`` : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L740

```diff
FILE: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol

 function withdrawToChain(
        IYieldBoxBase yieldBox,
        address from,
        uint256 assetId,
        uint16 dstChainId,
        bytes32 receiver,
        uint256 amount,
        uint256 share,
-         bytes memory adapterParams,
+         bytes calldata adapterParams,
        address payable refundAddress,
        uint256 gas
    ) external payable {

```
### ``adapterParams`` can be changed to ``calldata`` instead of ``memory`` : Saves ``282 GAS ``

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L32

```diff
FILE: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol

24: function withdrawToChain(
        IYieldBoxBase yieldBox,
        address from,
        uint256 assetId,
        uint16 dstChainId,
        bytes32 receiver,
        uint256 amount,
        uint256 share,
-        bytes memory adapterParams,
+        bytes calldata adapterParams,
        address payable refundAddress,
        uint256 gas
    ) external payable {

```

### ``bytecode``,``contractName`` can be changed to ``calldata`` instead of ``memory`` : Saves ``564 GAS ``

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/TapiocaDeployer/TapiocaDeployer.sol#L25-L26

```diff
FILE: tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol

 function deploy(
        uint256 amount,
        bytes32 salt,
-        bytes memory bytecode,
+        bytes calldata bytecode,
-        string memory contractName
+        string calldata contractName
    ) external payable returns (address addr) {

```
##

## [G-4] Using storage instead of memory for structs/arrays saves gas

### Saves ``16800 GAS``, ``4 Instances``

NOTE: Instances missed in bot race

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

### ``storage`` should be used instead of ``memory`` : Saves ``16800 GAS``, ``4 Instances``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L363


```diff
FILE: Breadcrumbstap-token-audit/contracts/options/TapiocaOptionBroker.sol

- 363: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
            _paymentToken
        ];
+ 363: PaymentTokenOracle storage paymentTokenOracle = paymentTokens[
            _paymentToken
        ];

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L237

```diff
FILE: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol

- 237: PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
            _paymentToken
        ];

+ 237: PaymentTokenOracle storage paymentTokenOracle = paymentTokens[
            _paymentToken
        ];

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L115

```diff
FILE: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol

- 115: LockPosition memory lockPosition = lockPositions[_tokenId];
+ 115: LockPosition storage lockPosition = lockPositions[_tokenId];

- 349: LockPosition memory lockPosition = lockPositions[tokenId];
+ 349: LockPosition storage lockPosition = lockPositions[tokenId];

```
##

## [G-5] Don't initialize the default values to state or local variables for saving gas  

Saves ``2206 GAS`` In state variable and ``182 GAS`` from loop

When you initialize a variable with its default value, the Solidity compiler has to store the default value in storage, which costs gas. If you don't initialize the variable, the compiler can optimize it out, which saves gas.

### ``Vesting.sol``: ``seeded`` variable is initialized in ``init()`` function. So assigning default value is waste of gas : Saves ``2206 GAS``,``SSTORE``

```diff
FILE: tap-token-audit/contracts/Vesting.sol

28: /// @notice returns total available tokens
- 29:    uint256 public seeded = 0;
+ 29:    uint256 public seeded;

```

### Default values initialization in loops : Saves ``182 GAS``, ``14 Instances```

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

666: for (uint256 i = 0; i < users.length; i++) {

215: for (uint256 i = 0; i < calls.length; i++) {

FILE: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol

187: for (uint256 i = 0; i < calls.length; i++) {

FILE: Breadcrumbstapioca-bar-audit/contracts/Penrose.sol

437: for (uint256 i = 0; i < len; ) {

492: for (uint256 i = 0; i < length; ) {

550: for (uint256 i = 0; i < _masterContractLength; ) {

564: for (uint256 i = 0; i < _masterContractLength; ) {

569: for (uint256 j = 0; j < clonesOfLength; ) {

FILE: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol

383:  uint256 liquidatedCount = 0;

384:for (uint256 i = 0; i < users.length; i++) {

45: for (uint256 i = 0; i < maxBorrowParts.length; i++) {

107: for (uint256 i = 0; i < users.length; i++) {

```

##

## [G-6] Optimizing gas usage Order your checks correctly

### Saves ``12000 GAS``

FAIL CHEEPLY INSTEAD OF COSTLY

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case. 

###  ``Singularity.sol`` : ``require`` check should be top of the function : Saves around ``12000 GAS`` 

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L92-L104

If any revert after storage write this cost more volume of gas . So the conditions should be checked first then the values should be write in storage variables. 


```diff
FILE: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol

function init(bytes calldata data) external onlyOnce {
        (
            address _liquidationModule,
            address _borrowModule,
            address _collateralModule,
            address _leverageModule,
            IPenrose tapiocaBar_,
            IERC20 _asset,
            uint256 _assetId,
            IERC20 _collateral,
            uint256 _collateralId,
            IOracle _oracle,
            uint256 _exchangeRatePrecision
        ) = abi.decode(
                data,
                (
                    address,
                    address,
                    address,
                    address,
                    IPenrose,
                    IERC20,
                    uint256,
                    IERC20,
                    uint256,
                    IOracle,
                    uint256
                )
            );

+         require(
+            address(_collateral) != address(0) &&
+                address(_asset) != address(0) &&
+                address(_oracle) != address(0),
+            "SGL: bad pair"
+        );

        liquidationModule = SGLLiquidation(_liquidationModule);
        collateralModule = SGLCollateral(_collateralModule);
        borrowModule = SGLBorrow(_borrowModule);
        leverageModule = SGLLeverage(_leverageModule);
        penrose = tapiocaBar_;
        yieldBox = YieldBox(tapiocaBar_.yieldBox());
        owner = address(penrose);

-         require(
-            address(_collateral) != address(0) &&
-                address(_asset) != address(0) &&
-                address(_oracle) != address(0),
-            "SGL: bad pair"
-        );

```

### ``_collateral, _oracle`` should be checked top of the function to avoid unwanted failures after storage write : Saves ``4000 GAS``


https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L133-L138

if we check ``address(_collateral) != address(0), address(_oracle) != address(0)`` after ``penrose,yieldBox `` storage write if address is zero then we need to pay 4000 GAS even after revert.


```solidity
FILE: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol

               )
            );

+        require(
+            address(_collateral) != address(0) &&
+                address(_oracle) != address(0),
+            "BigBang: bad pair"
+        );


        penrose = tapiocaBar_;
        yieldBox = YieldBox(tapiocaBar_.yieldBox());
        owner = address(penrose);

        address _asset = penrose.usdoToken();

+        require(address(_asset) != address(0) ,"BigBang: bad pair");
-        require(
-            address(_collateral) != address(0) &&
-                address(_asset) != address(0) &&
-                address(_oracle) != address(0),
-            "BigBang: bad pair"
-        );

```


##

## [G-7] Combine events to save Glogtopic (375 gas)

### Saves ``2450 GAS``

We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional events.

### ``BigBang.sol`` : ``LogRemoveCollateral``,``LogRepay`` events can be combined  : Saves ``Glogtopic (375 gas)``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L587-L588

```diff
FILE: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol

- 587: emit LogRemoveCollateral(user, address(swapper), collateralShare);
- 588: emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

+ emit LogRemoveCollateralAndRepay(
+    user,
+    address(swapper),
+    collateralShare,
+    borrowAmount,
+    borrowPart
+ );

```

### ``Singularity.sol`` : ``Transfer``,``LogWithdrawFees`` events can be combined  : Saves ``Glogtopic (375 gas)``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L466-L468

```diff
FILE: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol

465:  balanceOf[_feeTo] += _feesEarnedFraction;
- 466:  emit Transfer(address(0), _feeTo, _feesEarnedFraction);
467:  accrueInfo.feesEarnedFraction = 0;
- 468:  emit LogWithdrawFees(_feeTo, _feesEarnedFraction);

+ emit LogTransferAndWithdrawFees(address(0),
+     _feeTo,
+     _feesEarnedFraction,
+ );

```
### ``Singularity.sol`` : ``LogRemoveCollateral``,``LogRepay`` events can be combined  : Saves ``Glogtopic (375 gas)``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L134-L144


```diff
FILE: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol

- 134:  emit LogRemoveCollateral(
- 135:                    user,
- 136:                    address(liquidationQueue),
- 137:                    collateralShare
- 138:                );
- 139:                emit LogRepay(
- 140:
- 141:                    address(liquidationQueue),
- 142:                    user,
- 143:                    borrowAmount,
- 144:                    borrowPart
- 145:                );

+ emit LogRemoveCollateralAndRepay(
+            user,
+            address(liquidationQueue),
+            collateralShare,borrowAmount,      
+            borrowPart
+              );
            
```

### ``Singularity.sol`` : ``Transfer``,``LogWithdrawFees`` and ``LogRemoveCollateral``,``LogRepay`` events can be combined  : Saves ``2 Glogtopic (750 gas)``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L134-L144


```diff
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/singularity/Singularity.sol

465:  balanceOf[_feeTo] += _feesEarnedFraction;
- 466:  emit Transfer(address(0), _feeTo, _feesEarnedFraction);
467:  accrueInfo.feesEarnedFraction = 0;
- 468:  emit LogWithdrawFees(_feeTo, _feesEarnedFraction);

+ emit LogTransferAndWithdrawFees(address(0),
+     _feeTo,
+     _feesEarnedFraction,
+ );



- 321: emit LogRemoveCollateral(user, address(swapper), collateralShare);
- 322: emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

+ emit LogRemoveCollateralAndRepay(
+    user,
+    address(swapper),
+    collateralShare,
+    borrowAmount,
+    borrowPart
+ );

```

### ``SGLCommon.sol`` : ``Transfer``,``LogRemoveAsset`` and ``Transfer`` ,``LogAddAsset`` events can be combined  : Saves ``2 Glogtopic (750 gas)``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L237

```diff
FILE: tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol

- 237:  emit Transfer(from, address(0), fraction);
238:        _totalAsset.elastic -= uint128(share);
239:        _totalAsset.base -= uint128(fraction);
240:        require(_totalAsset.base >= 1000, "SGL: min limit");
242:        totalAsset = _totalAsset;
- 243:        emit LogRemoveAsset(from, to, share, fraction);
+    emit LogTransferAndRemoveAsset(from, address(0), fraction, to, share);


- 215: emit Transfer(address(0), to, fraction);
216:
217:        _addTokens(from, to, assetId, share, totalAssetShare, skim);
- 218:        emit LogAddAsset(skim ? address(yieldBox) : from, to, share, fraction);
+    emit LogTransferAndAddAsset(address(0), to, fraction, skim ? address(yieldBox) : from, share);

```
##

## [G-8] Cache the ``state variables`` outside the ``loop`` to avoid ``SLOD`` in every iterations

### Saves ``200 GAS``, ``2 SLOD `` per iterations

Cache state variables outside loops to avoid unnecessary gas costs associated with reading and writing state variables repeatedly

### ``paymentTokenBeneficiary`` caching out side the loops saves ``100 GAS`` per Iteration

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L492

```diff
FILE: Breadcrumbstap-token-audit/contracts/options/TapiocaOptionBroker.sol

486:   uint256 len = _paymentTokens.length;
487:
+    address paymentTokenBeneficiary_ = paymentTokenBeneficiary ;
488:        unchecked {
489:            for (uint256 i = 0; i < len; ++i) {
490:                ERC20 paymentToken = ERC20(_paymentTokens[i]);
491:                paymentToken.transfer(
- 492:                    paymentTokenBeneficiary,
+ 492:                    paymentTokenBeneficiary_ ,
493:                    paymentToken.balanceOf(address(this))
494:                );
495:            }

```

### ``paymentTokenBeneficiary`` caching out side the loops saves ``100 GAS`` per Iteration

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L378

```diff
FILE: Breadcrumbstap-token-audit/contracts/option-airdrop/AirdropBroker.sol

 uint256 len = _paymentTokens.length;
+  address paymentTokenBeneficiary_ = paymentTokenBeneficiary;
        unchecked {
            for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
-                     paymentTokenBeneficiary,
+                     paymentTokenBeneficiary_ ,
                    paymentToken.balanceOf(address(this))
                );
            }

```

##

## [G-9] ``Calldata`` pointer is used instead of ``memory`` pointer for loops 

### Saves ``34 GAS`` per iterations

If our function parameter is specified as ``calldata``, using a memory pointer will copy that ``calldata`` value into memory, which can result in memory expansion costs. To avoid this, we should use a calldata pointer to read from ``calldata`` directly.

```

- Memory copy: 20 gas
- Calldata read: 3 gas

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L48

```diff
FILE: tapioca-periph-audit/contracts/Multicall/Multicall3.sol

47: for (uint256 i = 0; i < length; ) {
- 48:            Result memory result = returnData[i];
+ 48:            Result calldata result = returnData[i];
49:            calli = calls[i];


70: for (uint256 i = 0; i < length; ) {
- 71:            Result memory result = returnData[i];
+ 71:            Result calldata result = returnData[i];
72:            calli = calls[i];
73:            uint256 val = calli.value;

```

##

## [G-10] Don't ``emit`` ``state`` variables when ``stack`` variable available 

### Saves ``100 GAS``, ``1 SLOD ``

When you emit a ``state`` variable, it is stored on the blockchain permanently. This means that it takes gas to store the variable, and it also takes gas to access the variable. If you have a stack variable that is available, you can use that variable instead of emitting a state variable. This will save you gas because you will not need to store the variable on the blockchain

### ``_epochTAPValuation`` emit this instead of ``epochTAPValuation `` state varibale : Saves ``100 GAS``, ``1 SLOD``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L293

```diff
FILE: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol

292:  epochTAPValuation = uint128(_epochTAPValuation);
- 293:   emit NewEpoch(epoch, epochTAPValuation);
+ 293:   emit NewEpoch(epoch, uint128(_epochTAPValuation));

```

##

## [G-11] Multiple accesses of a mapping/array should use a local variable cache

### Saves ``600 GAS,6 SLOD``

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

### ``userCollateralShare[user]`` should be cached : Saves ``200 GAS, 2 SLODs``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L816-L819

```diff
FILE: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
+ uint256  userCollateralShareuser_ = userCollateralShare[user];
- 816:   if (collateralShare > userCollateralShare[user]) {
+ 816:   if (collateralShare > userCollateralShareuser_ ) {
- 817:            collateralShare = userCollateralShare[user];
+ 817:            collateralShare = userCollateralShareuser_ ;
818:        }
- 819:        userCollateralShare[user] -= collateralShare;
+ 819:        userCollateralShare[user] =userCollateralShareuser_ - collateralShare;

```

### ``connectedOFTs[_srcOft][_dstChainId].rebalanceable`` should be cached : Saves ``300 GAS, 3 SLOD``

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L149-L156

```diff
FILE: Breadcrumbstapiocaz-audit/contracts/Balancer.sol

+ uint256 rebalanceable_ = connectedOFTs[_srcOft][_dstChainId].rebalanceable ;
-  canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;
+  canExec = rebalanceable_ > 0;
        execPayload = abi.encodeCall(
            Balancer.rebalance,
            (
                _srcOft,
                _dstChainId,
                1e3, //1% slippage
-                 connectedOFTs[_srcOft][_dstChainId].rebalanceable,
+                 rebalanceable_ ,
                ercData
            )



+ uint256 rebalanceable_ = connectedOFTs[_srcOft][_dstChainId].rebalanceable ;
{
-         if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
+         if (rebalanceable_ < _amount)

            revert RebalanceAmountNotSet();

        //check if OFT is still valid
        if (
            !_isValidOft(
                _srcOft,
                connectedOFTs[_srcOft][_dstChainId].dstOft,
                _dstChainId
            )
        ) revert DestinationOftNotValid();

        //extract
        ITapiocaOFT(_srcOft).extractUnderlying(_amount);

        //send
        bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
        if (_isNative) {
            if (msg.value <= _amount) revert FeeAmountNotSet();
            _sendNative(_srcOft, _amount, _dstChainId, _slippage);
        } else {
            if (msg.value == 0) revert FeeAmountNotSet();
            _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
        }

-         connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
+         connectedOFTs[_srcOft][_dstChainId].rebalanceable =rebalanceable_ - _amount;



function addRebalanceAmount(
        address _srcOft,
        uint16 _dstChainId,
        uint256 _amount
    ) external onlyValidDestination(_srcOft, _dstChainId) onlyOwner {
+  uint256 rebalanceable_ = connectedOFTs[_srcOft][_dstChainId].rebalanceable ;
-         connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;
+         connectedOFTs[_srcOft][_dstChainId].rebalanceable = rebalanceable_ + _amount;
        emit RebalanceAmountUpdated(
            _srcOft,
            _dstChainId,
            _amount,
-             connectedOFTs[_srcOft][_dstChainId].rebalanceable
+             rebalanceable_ 
        );
    }
```

## ``aoTAPCalls[_aoTAPTokenID][cachedEpoch]`` should be cached : Saves ``100 GAS, 1 SLOD``

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L254-L259

```diff
FILE: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
+   uint256 aoTAPCalls_  = aoTAPCalls[_aoTAPTokenID][cachedEpoch] ;
- 254: eligibleTapAmount -= aoTAPCalls[_aoTAPTokenID][cachedEpoch]; // Subtract already exercised amount
+ 254: eligibleTapAmount = eligibleTapAmount - aoTAPCalls_ ; // Subtract already exercised amount
255:        require(eligibleTapAmount >= _tapAmount, "adb: Too high");
256:
257:        uint256 chosenAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;
258:        require(chosenAmount >= 1e18, "adb: Too low");
- 259:        aoTAPCalls[_aoTAPTokenID][cachedEpoch] += chosenAmount; // Adds up exercised amount to current epoch
+ 259:        aoTAPCalls[_aoTAPTokenID][cachedEpoch] =aoTAPCalls_+chosenAmount; // Adds up exercised amount to current epoch

```

##

## [G-12] <x> += <y> costs more gas than <x> = <x> + <y> for state variables (SAME TO <x> -= <y>)

### Saves ``1469 GAS, 13 Instances``

Using the addition operator instead of plus-equals saves [113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

### 

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L147

```diff
FILE: tapioca-bar-audit/contracts/markets/MarketERC20.sol

- 147: balanceOf[to] += amount;
+ 147: balanceOf[to] =balanceOf[to] + amount;

- 82: balanceOf[to] += amount;
+ 82: balanceOf[to] =balanceOf[to] + amount;

- allowance[from][msg.sender] -= share;
+ allowance[from][msg.sender] =allowance[from][msg.sender] - share;

- 89: allowanceBorrow[from][msg.sender] -= share;
+ 89: allowanceBorrow[from][msg.sender] = allowanceBorrow[from][msg.sender] -  share;
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L255

```diff
FILE: tapiocaz-audit/contracts/Balancer.sol

- 255: connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;
+ 255: connectedOFTs[_srcOft][_dstChainId].rebalanceable =connectedOFTs[_srcOft][_dstChainId].rebalanceable + _amount;

- 210: connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
+ 210: connectedOFTs[_srcOft][_dstChainId].rebalanceable =connectedOFTs[_srcOft][_dstChainId].rebalanceable - _amount;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L343-L344

```diff
FILE: Breadcrumbstap-token-audit/contracts/governance/twTAP.sol

- 343: weekTotals[w0 + 1].netActiveVotes += int256(votes);
- 344: weekTotals[w1 + 1].netActiveVotes -= int256(votes);
+ 343: weekTotals[w0 + 1].netActiveVotes =weekTotals[w0 + 1].netActiveVotes + int256(votes);
+ 344: weekTotals[w1 + 1].netActiveVotes =weekTotals[w1 + 1].netActiveVotes - int256(votes);

- 412: next.netActiveVotes += prev.netActiveVotes;
+ 412: next.netActiveVotes =next.netActiveVotes + prev.netActiveVotes;

- 414: next.totalDistPerVote[i] += prev.totalDistPerVote[i];
+ 414: next.totalDistPerVote[i] = next.totalDistPerVote[i] + prev.totalDistPerVote[i];

- 445: totals.totalDistPerVote[_rewardTokenId] +=
-            (_amount * DIST_PRECISION) /
-            uint256(totals.netActiveVotes);
+ 445: totals.totalDistPerVote[_rewardTokenId] = totals.totalDistPerVote[_rewardTokenId] +
+            (_amount * DIST_PRECISION) /
+            uint256(totals.netActiveVotes);

- 491: claimed[_tokenId][i] += amount;
+ 491: claimed[_tokenId][i] = claimed[_tokenId][i] + amount;

- 513: claimed[_tokenId][claimableIndex] += amount;
+ 513: claimed[_tokenId][claimableIndex] =claimed[_tokenId][claimableIndex] + amount;

```
##



# OTHER GAS SUGGESTIONS

## [G-] Consider consolidating the ``addAsset`` and ``removeAsset`` , ``addCollateral``and ``removeCollateral`` related functions that could potentially be consolidated to reduce gas costs

By consolidating multiple related operations into a single function call, you can reduce the number of transactions and, consequently, the amount of gas required. This is particularly valuable in Ethereum, where gas costs can have a significant impact on the cost and speed of transactions.

The decision of whether or not to consolidate these functions is a trade-off between gas costs and code complexity. In some cases, the reduction in gas costs may be worth the additional complexity. In other cases, it may be better to keep the functions separate

```Solidity
FILE: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol

function addAsset(
        address from,
        address to,
        bool skim,
        uint256 share
    ) public notPaused allowedLend(from, share) returns (uint256 fraction) {
        _accrue();
        fraction = _addAsset(from, to, skim, share);
    }

 function removeAsset(
        address from,
        address to,
        uint256 fraction
    ) public notPaused returns (uint256 share) {
        _accrue();
        share = _removeAsset(from, to, fraction, true);
        _allowedLend(from, share);
    }


function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public {
        _executeModule(
            Module.Collateral,
            abi.encodeWithSelector(
                SGLCollateral.addCollateral.selector,
                from,
                to,
                skim,
                amount,
                share
            )
        );
    }

function removeCollateral(address from, address to, uint256 share) public {
        _executeModule(
            Module.Collateral,
            abi.encodeWithSelector(
                SGLCollateral.removeCollateral.selector,
                from,
                to,
                share
            )
        );
    }

```

### Recommended Mitigation

Consolidated functions as follows,

```solidity

// Consolidated function for asset management
function manageAsset(
    address from,
    address to,
    bool skim,
    uint256 share,
    bool isAddition // True for asset addition, False for removal
) public notPaused allowedLend(from, share) {
    _accrue();
    
    if (isAddition) {
        _addAsset(from, to, skim, share);
    } else {
        _removeAsset(from, to, share, true);
        _allowedLend(from, share);
    }
}

// Consolidated function for collateral management
function manageCollateral(
    address from,
    address to,
    bool skim,
    uint256 amount,
    uint256 share,
    bool isAddition // True for collateral addition, False for removal
) public {
    _executeModule(
        Module.Collateral,
        abi.encodeWithSelector(
            isAddition ? SGLCollateral.addCollateral.selector : SGLCollateral.removeCollateral.selector,
            from,
            to,
            skim,
            amount,
            share
        )
    );
}

```


Nested if is cheaper than single statement



Check storage check 

Is this possible any variable to declare immutable ? 


## Transfer of 0 should be checked  

State variables should be cached 

Upgrade Solidity’s optimizer
Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

  30:   solidity: {
  31:     compilers: [
  32:       {
  33:         version: "0.8.12",
  34:         settings: {
  35:           optimizer: { enabled: true, runs: 200 },
  36:         },
  37:       },








