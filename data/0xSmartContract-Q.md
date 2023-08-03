### QA Report Issues List

- [x] **Low 01** → `zroPaymentAddress;` is given twice in the arguments of the `BaseUSDO.triggerSendFrom()` function and it is not checked whether these values are the same
- [x] **Low 02** → The constant ` flashMintFee ` set in `Constructor` is not competitive.
- [x] **Low 03** → There are payable functions in the `BaseUSDO.sol` contract, but there is no withdraw function to withdraw Ethers in the contract
- [x] **Low 04** → The Pause architecture in the `Singularity.sol` contract is missing.
- [x] **Low 05** → There may be problems with the use of Layer2
- [x] **Low 06** → It is safer to define the value of InterestPerSecond with a single variable instead of 2 separate variables
- [x] **Low 07**→ `Market.sol` contract has inconsistent `callerFee` fee definition value
- [x] **Low 08**→ Considering the `USDO` as a fixed $1 causes problems.
- [x] **Low 09**→ `setMarketConfig()` function design is unsafe 
- [x] **Low 10**→ The `execute()` function can be re-entranted
- [x] **Low 11**→ Use a more recent version of OpenZeppelin dependencies
- [x] **Low 12**→ `execute()` function is not payable and does not process `msg.value` but subaccount is payable and wants to process `msg.value`, this makes the function unusable
- [x] **Low 13**→ If onlyOwner runs `renounceOwnership()` in the `TapiocaWrapper` contract, the contract may become unavailable
- [x] **Low 14**→ `ERC20Permit.sol` OZ contract import path is incorrect
- [x] **Low 15**→ There is a `receive()` function, but there is no `withdraw` mechanism to withdraw Ether from the contract
- [x] **Low 16**→ A security-risk compilation version of Vyper is used in the code of the project.
- [x] **Non-Critical  17**→ Unused Import
- [x] **Non-Critical  18**→ Assembly Codes Specific – Should Have Comments
- [x] **Non-Critical  19**→ Does not `event-emit` during significant parameter changes
- [x] **Non-Critical  20**→ Remove the unused `_executeViewModule` private function.
- [x] **Non-Critical  21**→ Test suites do not test for attack vectors, especially re-entrancy
- [x] **Non-Critical  22**→ Project Upgrade and Stop Scenario should be
- [x] **Non-Critical  23**→ Missing Event for  initialize
- [x] **Suggestion   01**→ Use descriptive names for Contracts and Libraries



### [Low-01] `zroPaymentAddress;` is given twice in the arguments of the `BaseUSDO.triggerSendFrom()` function and it is not checked whether these values are the same

The value `zroPaymentAddress;` in the arguments of the `BaseUSDO.triggerSendFrom()` function is given both directly as `zroPaymentAddress;` and inside the ISendFrom.LzCallParams struct, and the function body does not check whether these two addresses are the same, this will cause unwanted conflicts

```solidity
tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:
  157      /// @param approvals approvals array
  158:     function triggerSendFrom(
  159:         uint16 lzDstChainId,
  160:         bytes calldata airdropAdapterParams,
  161:         address zroPaymentAddress,               //@audit define zroPaymentAddress
  162:         uint256 amount,
  163:         ISendFrom.LzCallParams calldata sendFromData, //@audit define zroPaymentAddress with LzCallParams struct 
  164:         ICommonData.IApproval[] calldata approvals
  165:     ) external payable {
  166:         _executeModule(
  167:             Module.Options,
  168:             abi.encodeWithSelector(
  169:                 USDOOptionsModule.triggerSendFrom.selector,
  170:                 lzDstChainId,
  171:                 airdropAdapterParams,
  172:                 zroPaymentAddress,
  173:                 amount,
  174:                 sendFromData,
  175:                 approvals
  176:             ),
  177:             false
  178:         );
  179:     }

```

<img width="1270" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/a3cad2bd-7052-4888-b03b-3705e1a3cf68">


## Recommended Mitigation Steps

implement the following architecture where these two addresses will be the same;

```diff
tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:
  157      /// @param approvals approvals array
  158:     function triggerSendFrom(
  159:         uint16 lzDstChainId,
  160:         bytes calldata airdropAdapterParams,
- 161:         address zroPaymentAddress,               //@audit define zroPaymentAddress
  162:         uint256 amount,
  163:         ISendFrom.LzCallParams calldata sendFromData, //@audit define zroPaymentAddress with LzCallParams struct 
  164:         ICommonData.IApproval[] calldata approvals
  165:     ) external payable {
  166:         _executeModule(
  167:             Module.Options,
  168:             abi.encodeWithSelector(
  169:                 USDOOptionsModule.triggerSendFrom.selector,
  170:                 lzDstChainId,
- 171:                 airdropAdapterParams,
+ 171: 	               ISendFrom.LzCallParams. zroPaymentAddress 
  172:                 zroPaymentAddress,
  173:                 amount,
  174:                 sendFromData,
  175:                 approvals
  176:             ),
  177:             false
  178:         );
  179:     }

```


### [Low-02] The constant ` flashMintFee ` set in `Constructor` is not competitive.

When USDO Token flashloan is used, users are charged a fee, this fee is hard coded in the `BaseUSDOStorage` contract, this is not competitive, there are tokens with a flashloan fee of 0, if this fee is desired to be reduced in the future due to competition, this will not be possible.

```solidity
tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:
  69  
  70:     constructor(
  71:         address _lzEndpoint,
  72:         IYieldBoxBase _yieldBox
  73:     ) OFTV2("USDO", "USDO", 8, _lzEndpoint) {
  74:         uint256 chain = _getChainId();
  75:         allowedMinter[chain][msg.sender] = true;
  76:         allowedBurner[chain][msg.sender] = true;
  77:         flashMintFee = 10; // 0.001% // @audit-issue flashMintFee constant
  78:         maxFlashMint = 100_000 * 1e18; // 100k USDO
  79: 
  80:         yieldBox = _yieldBox;
  81:     }
```

## Recommended Mitigation Steps
Design `flashMintFee` in updatable architecture



### [Low-03] There are payable functions in the `BaseUSDO.sol` contract, but there is no withdraw function to withdraw Ethers in the contract

There are payable functions in the `BaseUSDO.sol` contract that are externally accessible to everyone, but there is no withdraw function in the contract to withdraw Ether or a function that transfers these ethers, so if a msg.value is sent via payable functions, the contract will be stuck.

```solidity
tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:
  158:     function triggerSendFrom(
  159:         uint16 lzDstChainId,
  160:         bytes calldata airdropAdapterParams,
  161:         address zroPaymentAddress,
  162:         uint256 amount,
  163:         ISendFrom.LzCallParams calldata sendFromData,
  164:         ICommonData.IApproval[] calldata approvals
  165:     ) external payable {


  186:     function exerciseOption(
  187:         ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData
  188:             calldata optionsData,
  189:         ITapiocaOptionsBrokerCrossChain.IExerciseLZData calldata lzData,
  190:         ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData
  191:             calldata tapSendData,
  192:         ICommonData.IApproval[] calldata approvals
  193:     ) external payable {


  215:     function initMultiHopBuy(
  216:         address from,
  217:         uint256 collateralAmount,
  218:         uint256 borrowAmount,
  219:         IUSDOBase.ILeverageSwapData calldata swapData,
  220:         IUSDOBase.ILeverageLZData calldata lzData,
  221:         IUSDOBase.ILeverageExternalContractsData calldata externalData,
  222:         bytes calldata airdropAdapterParams,
  223:         ICommonData.IApproval[] memory approvals
  224:     ) external payable {


  251:     function removeAsset(
  252:         address from,
  253:         address to,
  254:         uint16 lzDstChainId,
  255:         address zroPaymentAddress,
  256:         bytes calldata adapterParams,
  257:         ICommonData.ICommonExternalContracts calldata externalData,
  258:         IUSDOBase.IRemoveAndRepay calldata removeAndRepayData,
  259:         ICommonData.IApproval[] calldata approvals
  260:     ) external payable {


  284:     function sendForLeverage(
  285:         uint256 amount,
  286:         address leverageFor,
  287:         IUSDOBase.ILeverageLZData calldata lzData,
  288:         IUSDOBase.ILeverageSwapData calldata swapData,
  289:         IUSDOBase.ILeverageExternalContractsData calldata externalData
  290:     ) external payable {


  313:     function sendAndLendOrRepay(
  314:         address _from,
  315:         address _to,
  316:         uint16 lzDstChainId,
  317:         address zroPaymentAddress,
  318:         IUSDOBase.ILendOrRepayParams calldata lendParams,
  319:         ICommonData.IApproval[] calldata approvals,
  320:         ICommonData.IWithdrawParams calldata withdrawParams,
  321:         bytes calldata adapterParams
  322:     ) external payable {


```

### [Low-04] The Pause architecture in the `Singularity.sol` contract is missing.

In the `Singularity.sol` contract, in the case of Pause (when the project is stopped in emergency cases), many functions are run and the process is continued with functions such as `addCollateral` `removeColleteral`


|   |         Type        |       Bases      |                  |                 |
|:----------|:-------------------|:----------------|:----------------|:---------------|
|           |  **Function Name**  |  **Visibility**  |   |  **Modifiers**  |
| | addAsset | Public  |  Adds assets to the lending pair | notPaused  |
|  | removeAsset | Public  | Removes an asset from msg.sender and transfers it to `to`  | notPaused |
|  | refreshPenroseFees | External  | Transfers fees to penrose (onlyOwner)  | notPaused |
|  | execute | External  |  Allows batched call to Singularity ||
|  | addCollateral | Public  |  Adds `collateral` from msg.sender to the account `to ||
|  | removeCollateral | Public  | Removes `share` amount of collateral and transfers it to `to`.  ||
|  | borrow | Public  | Sender borrows `amount` and transfers it to `to`.  ||
|  | repay | Public  |  Repays a loan ||
|  | sellCollateral | External  | Lever down: Sell collateral to repay debt; excess goes to YB  ||
|  | buyCollateral | External  | Lever up: Borrow more and buy collateral with it.  ||
|  | multiHopBuyCollateral | External  | Level up cross-chain: Borrow more and buy collateral with it.  ||
|  | multiHopSellCollateral | External  |  Level up cross-chain: Borrow more and buy collateral with it. ||
|  | liquidate | External  |  Entry point for liquidations ||
|  | withdrawFeesEarned | Public  |  Withdraw the fees accumulated in `accrueInfo.feesEarnedFraction` to the balance of `feeTo` ||
|  | setSingularityConfig | External  | sets Singularity specific configuration  |  |
|  | setLiquidationQueueConfig | External  | sets LQ specific confinguration  |  |

## Recommended Mitigation Steps
Add Pause modifier to all priority functions like `addCollateral` `removeColleteral`

Consider adding comprehensive, user-friendly documentation on the pause mechanism implemented. The documentation should highlight under what conditions the system should or should not be paused, what the specific consequences of pausing are on system mechanics, and who is allowed to trigger this feature. For future versions of the protocol, it is recommended to design and implement mechanisms that support decentralization, allowing users to exit the system before behavior changes.

### [Low-05] There may be problems with the use of Layer2

According to the scope information of the project, it is stated that it can be used in rollup chains and popular EVM chains.

```js
README.md:
  192: - Does it use rollups?:   
  193: - Is it multi-chain?:  True
```

Some conventions in the project are set to Pragma ^0.8.18, allowing the conventions to be compiled with any 0.8.x compiler. The problem with this is that Arbitrum is [Compatible](https://developer.arbitrum.io/solidity-support) with 0.8.20 and newer. Contracts compiled with these versions will result in a non-functional or potentially damaged version that does not behave as expected. The default behavior of the compiler will be to use the latest version which means it will compile with version 0.8.20 which will produce broken code by default.


### [Low-06] It is safer to define the value of InterestPerSecond with a single variable instead of 2 separate variables

`onlyOnce` can be start to init with `Singularity.init()` function and define two variables `minimumInterestPerSecond` and `maximumInterestPerSecond` .
The values here are the maximum and minimum Interest per second values, it is important to stay within these values.

However, it is a safer approach to define it with a single variable (InterestPerSecond) and to follow the max and min values with `require`. Following the max and min values with two different variables will control the range of 4 different results and this is risky in terms of security.


### Define with Init
| Variable | Max Value |Min. Value|
|:--:|:-------|:--:|
|`_minimumInterestPerSecond`| - | 951293760 |
| `_maximumInterestPerSecond`| 2536783360 | -|



```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
   61      /// @notice The init function that acts as a constructor
   62:     function init(bytes calldata data) external onlyOnce {
               // ...
  112:         minimumInterestPerSecond = 951293760; // approx 3% APR
  113:         maximumInterestPerSecond = 2536783360; // approx 8% APR
               // ...

```

The values of `minimumInterestPerSecond` and `maximumInterestPerSecond` can be updated with `setSingularityConfig`function

```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  489:     function setSingularityConfig(
  490:         uint256 _lqCollateralizationRate,
  491:         uint256 _liquidationMultiplier,
  492:         uint256 _minimumTargetUtilization,
  493:         uint256 _maximumTargetUtilization,
  494:         uint64 _minimumInterestPerSecond,
  495:         uint64 _maximumInterestPerSecond,
  496:         uint256 _interestElasticity
  497:     ) external onlyOwner {

                // ...
  521:         if (_minimumInterestPerSecond > 0) {
  522:             require(
  523:                 _minimumInterestPerSecond < maximumInterestPerSecond,
  524:                 "SGL: not valid"
  525:             );
  526:             emit MinimumInterestPerSecondUpdated(
  527:                 minimumInterestPerSecond,
  528:                 _minimumInterestPerSecond
  529:             );
  530:             minimumInterestPerSecond = _minimumInterestPerSecond;
  531:         }


  533:         if (_maximumInterestPerSecond > 0) {
  534:             require(
  535:                 _maximumInterestPerSecond > minimumInterestPerSecond,
  536:                 "SGL: not valid"
  537:             );
  538:             emit MaximumInterestPerSecondUpdated(
  539:                 maximumInterestPerSecond,
  540:                 _maximumInterestPerSecond
  541:             );
  542:             maximumInterestPerSecond = _maximumInterestPerSecond;
  543:         }
                // ...

  573:     }
```



### [Low-07] `Market.sol` contract has inconsistent `callerFee` fee definition value

In the `market.sol` contract, many fee values are assigned in the definition of fees and it is specified with // and NatSpec comments, but no assignment is made to the `callerFee` and `protocolFee` values, whereas a value is specified in the NatSpec comments



```solidity
tapioca-bar-audit/contracts/markets/Market.sol:
  60  
  61:     /// @notice liquidation caller rewards
  62:     uint256 public callerFee; // 90% @audit 
  63:     /// @notice liquidation protocol rewards
  64:     uint256 public protocolFee; // 10% @audit 
  65:     /// @notice min % a liquidator can receive in rewards
  66:     uint256 public minLiquidatorReward = 1e3; //1%
  67:     /// @notice max % a liquidator can receive in rewards
  68:     uint256 public maxLiquidatorReward = 1e4; //10%
  69:     /// @notice max liquidatable bonus amount
  70:     /// @dev max % added to the amount that can be liquidated
  71:     uint256 public liquidationBonusAmount = 1e4; //10%
  72:     /// @notice collateralization rate
  73:     uint256 public collateralizationRate; // 75%
  74:     /// @notice borrowing opening fee
  75:     uint256 public borrowOpeningFee = 50; //0.05%
  76:     /// @notice liquidation multiplier used to compute liquidator rewards
  77:     uint256 public liquidationMultiplier = 12000; //12%
```

`callerFee` and `protocolFee` values are defined in the Singularity.sol contract, but `callerFee = 1000; // 1%` value is defined as %1 but above comment it is given as 90%, there is inconsistency here

```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  61      /// @notice The init function that acts as a constructor
  62:     function init(bytes calldata data) external onlyOnce {
  121          //default fees
  122:         callerFee = 1000; // 1%
```

### [Low-08] Considering the `USDO` as a fixed $1 causes problems.

`Market.updateExchangeRate()` NatSpec comment declare to `Oracle should consider USDO at 1$` , however, stable token peg can be under 1$
Also, no code confirming this comment was seen in the `updateExchangeRate()` function.

```solidity
tapioca-bar-audit/contracts/markets/Market.sol:
  334  
  335:     /// @notice Gets the exchange rate. I.e how much collateral to buy 1e18 asset.
  336:     /// @dev This function is supposed to be invoked if needed because Oracle queries can be expensive.
  337:     ///      Oracle should consider USDO at 1$
  338:     /// @return updated True if `exchangeRate` was updated.
  339:     /// @return rate The new exchange rate.
  340:     function updateExchangeRate() public returns (bool updated, uint256 rate) {
  341:         (updated, rate) = oracle.get("");
  342: 
  343:         if (updated) {
  344:             require(rate > 0, "Market: invalid rate");
  345:             exchangeRate = rate;
  346:             emit LogExchangeRate(rate);
  347:         } else {
  348:             // Return the old rate if fetching wasn't successful
  349:             rate = exchangeRate;
  350:         }
  351:     }
```



### [Low-09] `setMarketConfig()` function design is unsafe 

**Context:**
The `setMarketConfig()` function is used to update the values of the Marker contract and is only updated by the authorized `onlyOwner`

Updated Values;
- borrowOpeningFee value
- oracle address
- conservator address
- callerFee address
- liquidationBonusAmount value
- minLiquidatorReward value
- maxLiquidatorReward value
- totalBorrowCap value
- collateralizationRate value


**Description:**
Defect in design; Even updating only one value is the risk of accidentally changing other values, also gas usage is higher due to many if loops

```solidity
tapioca-bar-audit/contracts/markets/Market.sol:
  157      /// @dev values are updated only if > 0 or not address(0)
  158:     function setMarketConfig(
  159:         uint256 _borrowOpeningFee,
  160:         IOracle _oracle,
  161:         bytes calldata _oracleData,
  162:         address _conservator,
  163:         uint256 _callerFee,
  164:         uint256 _protocolFee,
  165:         uint256 _liquidationBonusAmount,
  166:         uint256 _minLiquidatorReward,
  167:         uint256 _maxLiquidatorReward,
  168:         uint256 _totalBorrowCap,
  169:         uint256 _collateralizationRate
  170:     ) external onlyOwner {
  171:         if (_borrowOpeningFee > 0) {
  172:             require(_borrowOpeningFee <= FEE_PRECISION, "Market: not valid");
  173:             emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);
  174:             borrowOpeningFee = _borrowOpeningFee;
  175:         }
  176: 
  177:         if (address(_oracle) != address(0)) {
  178:             oracle = _oracle;
  179:             emit OracleUpdated();
  180:         }
  181: 
  182:         if (_oracleData.length > 0) {
  183:             oracleData = _oracleData;
  184:             emit OracleDataUpdated();
  185:         }
  186: 
  187:         if (_conservator != address(0)) {
  188:             emit ConservatorUpdated(conservator, _conservator);
  189:             conservator = _conservator;
  190:         }
  191: 
  192:         if (_callerFee > 0) {
  193:             require(_callerFee <= FEE_PRECISION, "Market: not valid");
  194:             callerFee = _callerFee;
  195:         }
  196: 
  197:         if (_protocolFee > 0) {
  198:             require(_protocolFee <= FEE_PRECISION, "Market: not valid");
  199:             protocolFee = _protocolFee;
  200:         }
  201: 
  202:         if (_liquidationBonusAmount > 0) {
  203:             require(
  204:                 _liquidationBonusAmount < FEE_PRECISION,
  205:                 "Market: not valid"
  206:             );
  207:             liquidationBonusAmount = _liquidationBonusAmount;
  208:         }
  209: 
  210:         if (_minLiquidatorReward > 0) {
  211:             require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");
  212:             require(
  213:                 _minLiquidatorReward < maxLiquidatorReward,
  214:                 "Market: not valid"
  215:             );
  216:             minLiquidatorReward = _minLiquidatorReward;
  217:         }
  218: 
  219:         if (_maxLiquidatorReward > 0) {
  220:             require(_maxLiquidatorReward < FEE_PRECISION, "Market: not valid");
  221:             require(
  222:                 _maxLiquidatorReward > minLiquidatorReward,
  223:                 "Market: not valid"
  224:             );
  225:             maxLiquidatorReward = _maxLiquidatorReward;
  226:         }
  227: 
  228:         if (_totalBorrowCap > 0) {
  229:             emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);
  230:             totalBorrowCap = _totalBorrowCap;
  231:         }
  232: 
  233:         if (_collateralizationRate > 0) {
  234:             require(
  235:                 _collateralizationRate <= FEE_PRECISION,
  236:                 "Market: not valid"
  237:             );
  238:             collateralizationRate = _collateralizationRate;
  239:         }
  240:     }

```



**Recommendation:**
With this refactoring, each value is updated by its own specific function. This will allow you to update individual values without affecting the others, and it will improve code readability and maintainability. 

Change the code as below;

```solidity
tapioca-bar-audit/contracts/markets/Market.sol:

function setBorrowOpeningFee(uint256 _borrowOpeningFee) external onlyOwner {
    require(_borrowOpeningFee > 0 && _borrowOpeningFee <= FEE_PRECISION, "Market: not valid");
    emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);
    borrowOpeningFee = _borrowOpeningFee;
}

function setOracle(IOracle _oracle) external onlyOwner {
    require(address(_oracle) != address(0), "Market: invalid address");
    oracle = _oracle;
    emit OracleUpdated();
}

function setOracleData(bytes calldata _oracleData) external onlyOwner {
    oracleData = _oracleData;
    emit OracleDataUpdated();
}

function setConservator(address _conservator) external onlyOwner {
    require(_conservator != address(0), "Market: invalid address");
    emit ConservatorUpdated(conservator, _conservator);
    conservator = _conservator;
}

function setCallerFee(uint256 _callerFee) external onlyOwner {
    require(_callerFee > 0 && _callerFee <= FEE_PRECISION, "Market: not valid");
    callerFee = _callerFee;
}

function setProtocolFee(uint256 _protocolFee) external onlyOwner {
    require(_protocolFee > 0 && _protocolFee <= FEE_PRECISION, "Market: not valid");
    protocolFee = _protocolFee;
}

function setLiquidationBonusAmount(uint256 _liquidationBonusAmount) external onlyOwner {
    require(_liquidationBonusAmount > 0 && _liquidationBonusAmount < FEE_PRECISION, "Market: not valid");
    liquidationBonusAmount = _liquidationBonusAmount;
}

function setMinLiquidatorReward(uint256 _minLiquidatorReward) external onlyOwner {
    require(_minLiquidatorReward > 0 && _minLiquidatorReward < FEE_PRECISION && _minLiquidatorReward < maxLiquidatorReward, "Market: not valid");
    minLiquidatorReward = _minLiquidatorReward;
}

function setMaxLiquidatorReward(uint256 _maxLiquidatorReward) external onlyOwner {
    require(_maxLiquidatorReward > 0 && _maxLiquidatorReward < FEE_PRECISION && _maxLiquidatorReward > minLiquidatorReward, "Market: not valid");
    maxLiquidatorReward = _maxLiquidatorReward;
}

function setTotalBorrowCap(uint256 _totalBorrowCap) external onlyOwner {
    totalBorrowCap = _totalBorrowCap;
    emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);
}

function setCollateralizationRate(uint256 _collateralizationRate) external onlyOwner {
    require(_collateralizationRate > 0 && _collateralizationRate <= FEE_PRECISION, "Market: not valid");
    collateralizationRate = _collateralizationRate;
}


```


### [Low-10] The `execute()` function can be re-entranted

The `execute()` function allows batched call to Singularity. This function, which can run all the functions in the sigularity contract, can also be added to a call that can run the `execute()` function again, this can cause unforeseen problems.

```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  181:     function execute(
  182:         bytes[] calldata calls,
  183:         bool revertOnFail
  184:     ) external returns (bool[] memory successes, string[] memory results) {
  185:         successes = new bool[](calls.length);
  186:         results = new string[](calls.length);
  187:         for (uint256 i = 0; i < calls.length; i++) {
  188:             (bool success, bytes memory result) = address(this).delegatecall(
  189:                 calls[i]
  190:             );
  191:             require(success || !revertOnFail, _getRevertMsg(result));
  192:             successes[i] = success;
  193:             results[i] = _getRevertMsg(result);
  194:         }
  195:     }

```



**Recommendation:**
Add to re-entrancy modifier


### [Low-11] Use a more recent version of OpenZeppelin dependencies

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest OZ version.


```diff
tap-token-audit/package.json:
- 25:     "@openzeppelin/contracts": "^4.8.2",
+ 25:     "@openzeppelin/contracts": "^4.9.0",

```



**Recommendation:**
Old version of OZ is used `(4.8.2)`, newer version can be used `(4.9.0)` 

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0



### [Low-12] `execute()` function is not payable and does not process `msg.value` but subaccount is payable and wants to process `msg.value`, this makes the function unusable

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L181-L195

The `Singularity.execute()` function is not payable and thus will not handle the msg.value, but it has the ability to send a call to the `SGLLeverage.multiHopBuyCollateral()` function which is payable in subaccounts and will process the msg.value



```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  181:     function execute(
  182:         bytes[] calldata calls,
  183:         bool revertOnFail
  184:     ) external returns (bool[] memory successes, string[] memory results) {
  185:         successes = new bool[](calls.length);
  186:         results = new string[](calls.length);
  187:         for (uint256 i = 0; i < calls.length; i++) {
  188:             (bool success, bytes memory result) = address(this).delegatecall(
  189:                 calls[i]
  190:             );
  191:             require(success || !revertOnFail, _getRevertMsg(result));
  192:             successes[i] = success;
  193:             results[i] = _getRevertMsg(result);
  194:         }
  195:     }
```


`Singularity.execute()` --> `Singularity. multiHopBuyCollateral(payable)` --> `SGLLeverage. multiHopBuyCollateral(payable)` 

```solidity
tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:
  21:     function multiHopBuyCollateral(
  22:         address from,
  23:         uint256 collateralAmount,
  24:         uint256 borrowAmount,
  25:         IUSDOBase.ILeverageSwapData calldata swapData,
  26:         IUSDOBase.ILeverageLZData calldata lzData,
  27:         IUSDOBase.ILeverageExternalContractsData calldata externalData
  28:     ) external payable notPaused solvent(from) {
  42: 
  43:         //Other codes...
  49:         IUSDOBase(address(asset)).sendForLeverage{value: msg.value}( // @audit-issue need msg.value
  50:             borrowAmount,
  51:             from,
  52:             lzData,
  53:             swapData,
  54:             externalData
  55:         );
  56:     }
```

In summary; `Singularity. execute` cannot process ETH and send and receive ETH, but as a result of its call `SGLLeverage. If it is multiHopBuyCollateral(payable)` it will process msg.value


### Recommended Mitigation Steps
The msg.value cannot be used with `delegatecall`, so the design in the `execute()` function must be completely changed.


### [Low-13] If onlyOwner runs `renounceOwnership()` in the `TapiocaWrapper` contract, the contract may become unavailable


```solidity
tapiocaz-audit/contracts/TapiocaWrapper.sol:
   9: import "@openzeppelin/contracts/access/Ownable.sol";
 26: contract TapiocaWrapper is Ownable {
```


|  |         Type        |       Bases      |                  |
|:----------:|:-------------------:|:----------------:|:---------------:|
| |  **Function Name**  |  **Visibility**  |    **Modifiers**  |
|||||
| | Implementation | Ownable ||
| | executeTOFT | External ❗️ |   onlyOwner |
| | executeCalls | External ❗️ |    onlyOwner |
| | createTOFT | External ❗️ |    onlyOwner |


### Recommended Mitigation Steps

The solution to this is to overide and disable the renounceOwnership() function as implemented in many contracts in his project, it is important to include this code in the contract


```solidity
 function renounceOwnership() public payable override onlyOwner {
        revert("Cannot renounce ownership");
    }
```

### [Low-14] `ERC20Permit.sol` OZ contract import path is incorrect

`ERC20Permit.sol` OpenZeppelin contract import path is wrong, it is no longer called draft, so it needs to be updated, in which OZ ^4.9.0 should be used

```solidity
5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```

https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3793

### Recommended Mitigation Step

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol

```diff
tapioca-bar-audit/contracts/markets/MarketERC20.sol:
- 5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
+ 5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";

tap-token-audit/package.json:
  16:   "devDependencies": {
- 25:     "@openzeppelin/contracts": "^4.8.2",
+ 25:     "@openzeppelin/contracts": "^4.9.0",

```

### [Low-15] There is a `receive()` function, but there is no `withdraw` mechanism to withdraw Ether from the contract



```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  644:     receive() external payable {}
```

### [Low-16] A security-risk compilation version of Vyper is used in the code of the project.

Vyper versions 0.2.15, 0.2.16 and 0.3.0 are vulnerable to malfunctioning reentrancy locks. 
$47M lost due to reentrancy vulnerability

[Vyper Vulnerability](https://twitter.com/vyperlang/status/1685692973051498497?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1685693202722848768%7Ctwgr%5E979ecb88d5bc54af4a0c93b1d807255756d55bca%7Ctwcon%5Es3_&ref_url=https%3A%2F%2Fcointelegraph.com%2Fnews%2Fcurve-finance-pools-exploited-over-24-reentrancy-vulnerability)



```js
2 results - 2 files

tapioca-bar-audit/hardhat.export.ts:
  88      },
  89:     vyper: {
  90:         compilers: [{ version: '0.2.16' }],
  91:     },
  92      namedAccounts: {

tapioca-yieldbox-strategies-audit/hardhat.export.ts:
  112      },
  113:     vyper: {
  114:         compilers: [{ version: '0.2.16' }],
  115:     },
  116 

```

### [Non Critical-17] Unused Import

Some imports aren’t used inside the project

```solidity
tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:
  4: import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
  5: import "@boringcrypto/boring-solidity/contracts/ERC20.sol";

tapioca-bar-audit/contracts/markets/Market.sol:
  5: import "@boringcrypto/boring-solidity/contracts/libraries/BoringRebase.sol";

tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol:
  8: import "tapioca-periph/contracts/interfaces/ISwapper.sol";
  9: import "tapioca-periph/contracts/interfaces/IPenrose.sol";
```



### [Non Critical-18] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity
59 results - 17 files

tap-token-audit/contracts/twAML.sol:
  21:         assembly {
  33:             assembly {
  50:         assembly {
  54:         assembly {
  64:         assembly {
  69:         assembly {
  75:         assembly {

tap-token-audit/contracts/governance/twTAP.sol:
  573:         assembly {

tapioca-bar-audit/contracts/Penrose.sol:
  478:         assembly {

tapioca-bar-audit/contracts/markets/Market.sol:
  378:         assembly {

tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:
  93:         assembly {

tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:
  1082:         assembly {

tapioca-periph-audit/contracts/Multicall/Multicall3.sol:
  98:         assembly {

tapioca-periph-audit/contracts/oracle/external/FullMath.sol:
  31:         assembly {
  40:             assembly {
  57:         assembly {
  61:         assembly {
  71:         assembly {
  76:         assembly {
  82:         assembly {

tapioca-periph-audit/contracts/Swapper/libraries/FullMath.sol:
  26:         assembly {
  35:             assembly {
  52:         assembly {
  56:         assembly {
  66:         assembly {
  71:         assembly {
  77:         assembly {

tapioca-periph-audit/contracts/Swapper/libraries/TickMath.sol:
  102:         assembly {
  107:         assembly {
  112:         assembly {
  117:         assembly {
  122:         assembly {
  127:         assembly {
  132:         assembly {
  137:         assembly {
  147:         assembly {
  153:         assembly {
  159:         assembly {
  165:         assembly {
  171:         assembly {
  177:         assembly {
  183:         assembly {
  189:         assembly {
  195:         assembly {
  201:         assembly {
  207:         assembly {
  213:         assembly {
  219:         assembly {
  225:         assembly {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/contracts-upgradable/lzApp/LzAppUpgradeable.sol:
  57:         assembly {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/contracts-upgradable/token/OFT/OFTCoreUpgradeable.sol:
  42:         assembly {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/contracts-upgradable/token/ONFT721/ONFT721CoreUpgradeable.sol:
  58:         assembly {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/libraries/LzLib.sol:
  49:         assembly {
  57:         assembly {
  65:             assembly {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/lzApp/LzApp.sol:
  65:         assembly {

tapiocaz-audit/tapioca-periph/contracts/TapiocaDeployer/TapiocaDeployer.sol:
  40:         assembly {
  73:         assembly {

YieldBox/certora/harness/DummyBoringAddress.sol:
  11:         assembly {

```

### [Non Critical-19] Does not `event-emit` during significant parameter changes

The following 3 parameter changes update important states, off-chain apps need to emit emits to track them, add emit to these functions

```solidity
tap-token-audit/contracts/governance/twTAP.sol:
  455:     function addRewardToken(IERC20 token) external onlyOwner returns (uint256) {
  456:         uint256 i = rewardTokens.length;
  457:         rewardTokens.push(token);
  458:         rewardTokenIndex[token] = i;
  459:         return i;
  460:     }

tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:
  318:     function setPhase2MerkleRoots(
  319:         bytes32[4] calldata _merkleRoots
  320:     ) external onlyOwner {
  321:         phase2MerkleRoots = _merkleRoots;
  322:     }
  
  324:     function registerUserForPhase(
  325:         uint256 _phase,
  326:         address[] calldata _users,
  327:         uint256[] calldata _amounts
  328:     ) external onlyOwner {
  329:         require(_users.length == _amounts.length, "adb: invalid input");
  330: 
  331:         if (_phase == 1) {
  332:             for (uint256 i = 0; i < _users.length; i++) {
  333:                 phase1Users[_users[i]] = _amounts[i];
  334:             }
  335:         } else if (_phase == 4) {
  336:             for (uint256 i = 0; i < _users.length; i++) {
  337:                 phase4Users[_users[i]] = _amounts[i];
  338:             }
  339:         }
  340:     }


tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:
  357:     function setPaymentTokenBeneficiary(
  358:         address _paymentTokenBeneficiary
  359:     ) external onlyOwner {
  360:         paymentTokenBeneficiary = _paymentTokenBeneficiary;
  361:     }

tap-token-audit/contracts/options/TapiocaOptionBroker.sol:
  471:     function setPaymentTokenBeneficiary(
  472:         address _paymentTokenBeneficiary
  473:     ) external onlyOwner {
  474:         paymentTokenBeneficiary = _paymentTokenBeneficiary;
  475:     }

```
### [Non Critical-20] Remove the unused `_executeViewModule` private function.

The `_executeView Module` private function is not used anywhere in the project.

```solidity
tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:
  631:     function _executeViewModule(
  632:         Module _module,
  633:         bytes memory _data
  634:     ) private view returns (bytes memory returnData) {
  635:         bool success = true;
  636:         address module = _extractModule(_module);
  637: 
  638:         (success, returnData) = module.staticcall(_data);
  639:         if (!success) {
  640:             revert(_getRevertMsg(returnData));
  641:         }
  642:     }
```

### [Non Critical-21] Test suites do not test for attack vectors, especially re-entrancy

Test teams are testing many functions and variables, but recently, due to the vulnerability in the Vyper Compiler, the hacking of the projects using certain Vyper compiler and losing 50 million $ has revealed the security weakness here.

Attack vectors, especially re-entrancy, seem untested and trusted

```solidity
42 results - 18 files

tap-token-audit/contracts/Vesting.sol:
  110:     function claim() external nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:
  234:     function _deposited(uint256 amount) internal override nonReentrant {
  253:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:
  138:     function _deposited(uint256 amount) internal override nonReentrant {
  194:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:
  123:     function _deposited(uint256 amount) internal override nonReentrant {
  139:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:
  300:     function _deposited(uint256 amount) internal override nonReentrant {
  323:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPGetter.sol:
  131:     ) external nonReentrant returns (uint256) {
  145:     ) external nonReentrant returns (uint256) {
  155:     ) external nonReentrant returns (uint256) {
  169:     ) external nonReentrant returns (uint256) {
  179:     ) external nonReentrant returns (uint256) {
  193:     ) external nonReentrant returns (uint256) {

tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:
  218:     function _deposited(uint256 amount) internal override nonReentrant {
  232:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:
  205:     function _deposited(uint256 amount) internal override nonReentrant {
  219:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:
  128:     function _deposited(uint256 amount) internal override nonReentrant {
  144:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:
  223:     function _deposited(uint256 amount) internal override nonReentrant {
  244:     ) internal override nonReentrant {

tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:
  121:     function _deposited(uint256 amount) internal override nonReentrant {
  135:     ) internal override nonReentrant {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/mocks/LZEndpointMock.sol:
  113:     function send(uint16 _chainId, bytes memory _path, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes memory _adapterParams) external payable override sendNonReentrant {
  153:     function receivePayload(uint16 _srcChainId, bytes calldata _path, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external override receiveNonReentrant {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/stargate/WidgetSwap.sol:
  45:     ) external override nonReentrant payable {
  70:     ) external override nonReentrant payable {

tapiocaz-audit/gitsub_tapioca-sdk/src/contracts/token/oft/extension/NativeOFT.sol:
  37:     function withdraw(uint _amount) public nonReentrant {

tapiocaz-audit/tapioca-periph/contracts/Swapper/UniswapV2Swapper.sol:
  115:         nonReentrant



```

### [Non Critical-22] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

This can be done by adding the 'pause' architecture, which is included in many projects, to critical functions, and by authorizing the existing onlyOwner.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [Non Critical-23] Missing Event for  initialize

Most of them do not use event-emit when setting the startup items in the project's constructor.



```solidity
186 results - 180 files
```

### [S-01] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

