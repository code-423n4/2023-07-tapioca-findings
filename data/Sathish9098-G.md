# GAS OPTIMIZATIONS

##

## [G-1] State variables can be packed into fewer storage slots

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

-  ``totalBorrow,borrowOpeningFee , liquidationMultiplier `` can be packed single ``SLOT``


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

## [G-2] State variables only set in the constructor should be declared ``immutable``

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While ``strings`` are not value types, and therefore cannot be ``immutable/constant`` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract ``abstract`` with ``virtual`` functions for the ``string`` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

### ``BaseUSDOStorage.sol`` : ``flashMintFee``, ``maxFlashMint `` can be declared ``immutable``. The values only set in the constructor : Saves ``4194 GAS``

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L77-L78


```diff
FILE: tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol

32:   /// @notice returns the flash mint fee
33:    uint256 public flashMintFee;
34:    /// @notice returns the maximum amount of USDO that can be minted through the EIP-3156 flow
35:    uint256 public maxFlashMint;


77: flashMintFee = 10; // 0.001%
78: maxFlashMint = 100_000 * 1e18; // 100k USDO

```






## Calldata should be used instead of memory for external 

## Calldata pointer is used instead of memory pointer for loops or not modified 

## Don't emit state variable 

## remove multiple emit use combined emit 

## Don't initialize the variable 

## Use immutables for unchanged values instead of external calls 

## Cache state variables outside of loop to avoid reading/writing storage on every iteration

## Cache external calls outside the loop

## Optimize names to save gas

## if blocks should be separate loops 

## A mapping is more efficient than an array

## Using storage instead of memory for structs/arrays saves gas

## Structs can be packed in fewer slots 

## State variables can be packed in fewer slots 

## State variable only set in constructor can be immutable 

## State variable can be chached with stack varibale 

## Multiple access of mapping/array can be cached 

## Result of function calls should be cached 

## The condition check should be top of the functions 

## Use assembly to perform efficient back-to-back calls

## Check from protocol any ways to saves gas 

## Transfer of 0 should be checked 

## Private or modifiers only called once can be inlined 

Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard

Using true and false will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using 1 and 2 (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

## Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it)

