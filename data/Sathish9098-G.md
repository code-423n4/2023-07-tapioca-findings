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
##

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

##

##

## [G-3] Structs can be packed into fewer storage slots

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct.

### ``Vesting.sol`` :``latestClaimTimestamp`` and ``revoked`` can be packed same ``SLOT`` . Saves ``2000 ``, ``1 SLOT``

```diff
FILE: Breadcrumbstap-token-audit/contracts/Vesting.sol

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

## [G-3] Don't initialize the default values to state or local variables for saving gas  

When you initialize a variable with its default value, the Solidity compiler has to store the default value in storage, which costs gas. If you don't initialize the variable, the compiler can optimize it out, which saves gas.

### ``Vesting.sol``: ``seeded`` variable is initialized in ``init()`` function. So assigning default value is waste of gas : Saves ``2000 GAS``,``SSTORE``

```diff
FILE: tap-token-audit/contracts/Vesting.sol

28: /// @notice returns total available tokens
- 29:    uint256 public seeded = 0;
+ 29:    uint256 public seeded;

```
##

## [G-5] Using ``calldata`` instead of ``memory`` for read-only arguments in ``external`` functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it’s still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract, and cases where a constructor is involved


##

## [G-12] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct




##

## [G-4] Optimizing gas usage Order your checks correctly

FAIL CHEEPLY INSTEAD OF COSTLY

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case. 


##

## [G-6] Calldata pointer is used instead of memory pointer for loops or not modified

 ``calldata`` pointer should be used instead of ``memory`` pointer inside the loops that do not modify the data. This is because ``calldata`` is a read-only data location, so it cannot be modified by the contract code.

##

## [G-7] Don't ``emit`` ``state`` variables when ``stack`` variable available 

When you emit a ``state`` variable, it is stored on the blockchain permanently. This means that it takes gas to store the variable, and it also takes gas to access the variable. If you have a stack variable that is available, you can use that variable instead of emitting a state variable. This will save you gas because you will not need to store the variable on the blockchain


##

## [G-8] Combine events to save 2 Glogtopic (375 gas)

 We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional two events.

##

## [G-9] Use immutable for unchanged values instead of external calls 

It is a good practice to use immutable for unchanged values instead of external calls. This is because external calls can be gas-intensive, especially if the contract is calling a contract on another blockchain

##

## [G-10] Cache state variables outside of loop to avoid reading/writing storage on every iteration

Tt is a good practice to cache state variables outside of loop to avoid reading/writing storage on every iteration. This is because reading and writing storage is gas-intensive, especially if the state variable is large.



## [G-11] Cache external calls outside the loop

It is a good practice to cache external calls outside the loop. This is because calling an external contract can be gas-intensive, especially if the contract is calling a contract on another blockchain



## Multiple access of mapping/array can be cached 

## Use assembly to perform efficient back-to-back calls


## Transfer of 0 should be checked 

## A mapping is more efficient than an array

## Private or modifiers only called once can be inlined 

Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard

Using true and false will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using 1 and 2 (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

## Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it)

