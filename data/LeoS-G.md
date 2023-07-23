# Summary
|        | Issue | Instances | Gas Saved |
|--------|-------|-----------|-----------|
|[G-01]|Correction/Addition to  automated findings [G-09]|7|-16 164|
|[G-02|Use `calldata` instead of `memory`|23|-8 334|
|[G-03|Change compiler version + [G01] automated finding criticism|-|-7 329|
|[G-04|Change the optimizer runs number|-|-89 552|

The gas saved column simply adds up the evolution in the gas reports, excluding deployments and using the method described in the next section.
# Benchmark
A benchmark is performed on each optimization, using the gas report provided by hardhat. To provide the best possible quality, only optimizations that significantly improve this report are presented. This report is based on tests and therefore does not take into account all functions, this loss is accepted. But it is also subject to intrinsic variance. This means that some functions or deployment costs that are not relevant to the comparison because they vary too much are not taken into account in the presented gas report. These are listed below.
**tapioca-bar-audit**
`ERC20WithSupply: permit`, `MarketERC20: permitBorrow`, `Penrose: setFeeTo`, `Singularity: addAsset`, `BigBang: buyCollateral`, `MarketERC20: approveBorrow`, `Penrose: registerSingularity`, `YieldBox: registerAsset`, `BigBang: addCollateral`, `BigBang: sellCollateral`, `BigBang: borrow`, `BigBang: execute`, `BigBang: liquidate`, `BigBang: removeCollateral`, `BigBang: repay`, `SGLLiquidation: liquidate`, `Singularity: removeAsset`, `USDO: mint`, `YieldBox: depositAsset`, `YieldBox: withdraw`
**tapioca-periph-audit**
`MagnetarV2: depositAddCollateralAndBorrowFromMarket`, `MagnetarV2: depositRepayAndRemoveCollateralFromMarket`, `YieldBox: depositAsset`
**tapiocaz-audit**
`LiquidationQueue: init`, `MagnetarV2: burst`, `MagnetarV2: depositAddCollateralAndBorrowFromMarket`, `MagnetarV2: depositRepayAndRemoveCollateralFromMarket`, `YieldBox: depositAsset`
**tap-token-audit**
`AirdropBroker: participate`, `AirdropBroker: registerUserForPhase`, `OTAP: batch`, `OTAP: permit`, `TapiocaOptionLiquidityProvision: batch`, `TapiocaOptionLiquidityProvision: permit`, `TapOFT: permit`, `ERC20: approve`, `Vesting: registerUser`, `AirdropBroker: exerciseOption`,`Deployment: AirdropBroker`
**YieldBox**
`YieldBox: batch`
# [G-01] Correction/Addition to  automated findings [G-09]
The optimization proposed in the automatic report ([G-09] Using `storage` instead of `memory` for structs/arrays saves gas) can be commented and criticized.

## Additional instance
These instances do not seem to have been identified. Only instances compilable with a simple type swap are considered valid
**tap-token-audit**
*3 instances*
- [TapiocaOptionLiquidityProvision.sol#L349](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L349)
- [twTAP.sol#L172](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L172)
- [twTAP.sol#L526](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L526)

Applying these changes, gas report evolves as follows :
||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|TapiocaOptionBroker: exerciseOption|163739|163458|-281|
|TapiocaOptionBroker: exitPosition|110233|109986|-247|
|TapiocaOptionBroker: participate|290389|290108|-281|
|TapOFT: claimRewards|375305|372567|-2738|
|TapOFT: unlockTwTapPosition|220699|216974|-3725|
|TwTAP: claimRewards|117965|115227|-2738|
|TwTAP: exitPosition|60278|57392|-2886|
|TwTAP: releaseTap|57086|54887|-2199|
|||||
|Deployment: TapiocaOptionLiquidityProvision|2861468|2852596|-8872|
|Deployment: TwTAP|5343876|5321160|-22716|

*Gas saved: -15095*


## False positive instances
Some proposed instances make gas costs worse. This is because this optimization is highly context-dependent. So all instances must be tested individually to observe their impact. It is therefore suggested to delete the following instances
**tapioca-bar-audit**
*1 instances*

- [BigBang.sol#L513](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L513)

If applied the gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|BigBang: accrue|70794|70810|+16|
|||||
|Deployment: BigBang|5115971|5097620|-18351|

*Gas saved: -16 (those could have been wasted)*
This one slightly makes the cost worse. Thus, it depends on what the deployer is willing to pay for the deployment.

**tap-token-audit**
*3 instances*
- [twTAP.sol#L263](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L263)
- [TapiocaOptionBroker.sol#L222](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L222)
- [TapiocaOptionBroker.sol#L312](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L312)

If applied the gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|TapiocaOptionBroker: exerciseOption|163739|163718|-21|
|TapiocaOptionBroker: exitPosition|110233|110262|+29|
|TapiocaOptionBroker: participate|290389|290682|+293|
|TapOFT: lockTwTapPosition|386935|387330|+395|
|TapOFT: unlockTwTapPosition|220699|220687|-12|
|TwTAP: exitPosition|60278|60261|-17|
|TwTAP: participate|300448|300850|+402|
|TwTAP: releaseTap|57086|57067|-19|
|||||
|Deployment: TapiocaOptionBroker|2844098|2753217|-90881|
|Deployment: TwTAP|5343876|5345292|+1416|

*Gas saved: -1053 (those could have been wasted)*
The impact here is not negligible

## Benchmarked
During the verification, some other instances were tested, although there's nothing to say about them. Since the benchmark has been performed, it is provided here as it can still be useful.

**tapioca-bar-audit**
- [SGLLendingCommon.sol#L74](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L74)
- [SGLCommon.sol#L232](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L232)
- [BigBang.sol#L525](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L525)
- [Market.sol#L316](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L316)

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|BigBang: accrue|70794|70687|-107|
|Penrose: withdrawAllMarketFees|317314|317307|-7|
|SGLLeverage: multiHopBuyCollateral|1009039|1007797|-1242|
|SGLLeverage: multiHopSellCollateral|842239|841310|-929|
|||||
|Deployment: BigBang|5115971|5091407|-24564|
|Deployment: SGLBorrow|3514888|3488493|-26395|
|Deployment: SGLCollateral|3272334|3271026|-1308|
|Deployment: SGLLeverage|4407614|4383433|-24181|
|Deployment: SGLLiquidation|4339490|4338194|-1296|
|Deployment: Singularity|5248515|5228184|-20331|

*Gas: not taken into account in the final addition*
**tap-token-audit**
- [TapiocaOptionLiquidityProvision.sol#L131](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L131)
- [TapiocaOptionLiquidityProvision.sol#L304](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L304)
- [twTAP.sol#L536](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L536)
- [TapiocaOptionBroker.sol#L308](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L308)

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|TapiocaOptionBroker: exitPosition|110233|110070|-163|
|TapiocaOptionBroker: newEpoch|210458|210247|-211|
|TapiocaOptionLiquidityProvision: unregisterSingularity|52191|51994|-197|
|TapOFT: lockTwTapPosition|386935|386919|-16|
|TapOFT: unlockTwTapPosition|220699|220605|-94|
|TwTAP: exitPosition|60278|60161|-117|
|TwTAP: participate|300448|300424|-24|
|TwTAP: releaseTap|57086|56935|-151|
|||||
|Deployment: TapiocaOptionBroker|2844098|2834803|-9295|
|Deployment: TapiocaOptionLiquidityProvision|2861468|2857200|-4268|
|Deployment: TwTAP|5343876|5339103|-4773|

*Gas: not taken into account in the final addition*

# [G-02] Use `calldata` instead of `memory`
Using `calldata` instead of `memory` for function parameters can save gas if the argument is only read in the function. Only instances compilable with a simple type swap are considered valid.
**tapioca-bar-audit**
*16 instances*
- [USDOMarketModule.sol#L137](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L137)
- [USDOMarketModule.sol#L139](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L139)
- [USDOMarketModule.sol#L193](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L193)
- [USDOMarketModule.sol#L194](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L194)
- [USDOMarketModule.sol#L195](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L195)
- [USDOOptionsModule.sol#L103](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L103)
- [USDOOptionsModule.sol#L141](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L141)
 - [USDOLeverageModule.sol#L93](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L93)
  - [USDOLeverageModule.sol#L136](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L136)
   - [USDOLeverageModule.sol#L138](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L138)
  - [USDOLeverageModule.sol#L192](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L136)
  - [USDOLeverageModule.sol#L193](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L136)
- [USDOLeverageModule.sol#L194](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L136)
- [BaseUSDO.sol#L223](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L223)
- [Penrose.sol#L426](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L426)
- [Penrose.sol#L488](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L426)
    
Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|Penrose: executeMarketFn|70163|69598|-565|
|Penrose: withdrawAllMarketFees|317314|316920|-394|
|SGLLeverage: multiHopBuyCollateral|1009039|1006113|-2926|
|SGLLeverage: multiHopSellCollateral|842239|839536|-2703|
|||||
|Deployment: Penrose|3973361|3935455|-37906|
|Deployment: USDO|5603853|5499424|-104429|
|Deployment: USDOLeverageModule|5223036|5134766|-88270|
|Deployment: USDOMarketModule|5289557|5242575|-46982|
|Deployment: USDOOptionsModule|4910948|4909376|-1572|

*Gas saved: -6588*

**tapioca-periph-audit**
*2 instances*

- [CurveSwapper.sol#L98](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L98)
- [MagnetarV2.sol#L740](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L740)

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|CurveSwapper: swap|281471|281285|-186|
|MagnetarV2: withdrawToChain|98692|98405|-287|
|||||
|Deployment: CurveSwapper|1350630|1312166|-38464|
|Deployment: MagnetarV2|5362407|5376465|+14058|

*Gas saved: -473*

**tapioca-yieldbox-strategies-audit**
1 instance

- [BalancerStrategy.sol#L117](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol#L117)

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|YieldBox: withdraw|150249|150247|-2|
|||||
|Deployment: BalancerStrategy|1961887|1960783|-1104|

*Only interesting for deployment*
*Gas saved: -2*

**tapiocaz-audit**
*2 instances*

- [BaseTOFTStrategyModule.sol#L125](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L125)
- [BaseTOFTStrategyModule.sol#L127](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L127)

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|BaseTOFT: sendToStrategy|339476|338516|-960|
|||||
|Deployment: BaseTOFTStrategyModule|4199235|4214180|+14945|

*Gas saved: -960*
**tap-token-audit**
*2 instances*

- [twTAP.sol#L363](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L363)
- [BaseTapOFT.sol#L166](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L166)

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|TapOFT: claimRewards|375305|374994|-311|
|||||
|Deployment: TapiocaOptionBroker|2844098|2844086|-12|
|Deployment: TapOFT|5252762|5231963|-20799|
|Deployment: TwTAP|5343876|5342400|-1476|

*Gas saved: -311*



# [G-03] Change compiler version + [G01] automatic finding criticism
This proposed optimisation overlap the automated one: "[G-01]Reduce gas usage by moving to Solidity 0.8.19 or later". However, it is proposed because automatic operation can be strongly criticized.
Firstly, upgrading the solidity version from 0.8.18 (except, see later) to 0.8.19 or higher has absolutely no significant impact on gas reports. By this I mean that the evolutions are either nil, or less than a hundredth of a percent. In my opinion, this makes the finding invalid. The changes applied by version 0.8.19 or higher are highly context-dependent and don't seem to apply here. Furthermore, it seems to be a bad practice to increase the version without justification.
Secondly, without applying any modifications to the code, YieldBox contracts will not compile on versions higher than 0.8.11.

This is why it is suggested to only pass these contracts from 0.8.9 to 0.8.11 and leaving all the others in 0.8.18. This is the only way to make the gas report evolve positively.

**YieldBox** 
By compiling Yieldbox contracts with 0.8.11 (using the hardhat configuration file), the gas report evolve as follow:
||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|AssetRegister: registerAsset|72676|72484|-192|
|ERC1155Mock: safeBatchTransferFrom|190751|190641|-110|
|ERC1155Mock: safeTransferFrom|125429|125319|-110|
|MasterContractFullCycleMock: run|606991|602280|-4711|
|YieldBox: deposit|154547|154025|-522|
|YieldBox: depositAsset|128097|127841|-256|
|YieldBox: depositETH|202190|201550|-640|
|YieldBox: depositNFTAsset|133159|133031|-128|
|YieldBox: registerAsset|123417|123033|-384|
|YieldBox: withdraw|62079|61793|-286|
|||||
|Deployment: AssetRegister|1490025|1473877|-16148|
|Deployment: ERC1155Mock|1075225|1068763|-6462|
|Deployment: ERC1155ReceiverMock|610357|603895|-6462|
|Deployment: ERC1155StrategyMock|441909|438693|-3216|
|Deployment: ERC20StrategyMock|402256|395793|-6463|
|Deployment: ERC721Mock|1144839|1141623|-3216|
|Deployment: ERC721StrategyMock|352815|349581|-3234|
|Deployment: MasterContractFullCycleMock|789813|751009|-38804|
|Deployment: NativeTokenFactory|2375846|2359667|-16179|
|Deployment: YieldBox|5038198|5012291|-25907|
|Deployment: YieldBoxURIBuilder|1528860|1515946|-12914|

*Gas saved: -7329*

# [G-04] Change the optimizer runs number
The number of setup runs in the optmizer mean how many times the contracts are meant to be used. It can be set individually. A huge number of runs improves the running cost, but penalizes the deployment cost, and inversely. 
This depends on the deploier's vision and can therefore be widely discussed. It's not 100% relevant to optimize this through gas report, as it doesn't always represent a normal use of contracts. However, it is the only readily available metric and will therefore be used. With this in mind, the purpose of this optimization is simply to show the possible variations and thus highlight the importance of correctly setting up the number of runs for the differents contracts

*Changes are made using hardhat configuration files*

The easiest way to adjust is by trial and error. Here some rules are applied:
- Deployment costs are not increased by more than 10%. 
- Contracts do not exceed the deployment size limit. 
- Only contracts clearly visible in the gas report are modified, all others have the default value applied.
- Only in-scope contracts are considered.

**tapioca-bar-audit** 

    default:10 runs
    USDO.sol: 75 runs
    SGLLeverage.sol: 5000 runs
    SGLLiquidation.sol: 10000 runs
    Penrose.sol: 500 runs
    Singularity.sol: 800 runs
    BigBang.sol: 750 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|BigBang: accrue|70794|70333|-461|
|BigBang: addCollateral|119478|119420|-58|
|BigBang: borrow|238423|237833|-590|
|BigBang: buyCollateral|278366|276989|-1377|
|BigBang: execute|237909|237303|-606|
|BigBang: liquidate|499020|496812|-2208|
|BigBang: removeCollateral|118944|118675|-269|
|BigBang: repay|155766|155118|-648|
|BigBang: sellCollateral|243889|242534|-1355|
|BigBang: updateExchangeRate|39544|39522|-22|
|BigBang: updateOperator|47554|47542|-12|
|ERC20WithSupply: permit|78955|78922|-33|
|MarketERC20: approveBorrow|45218|45207|-11|
|MarketERC20: permitBorrow|77579|77558|-21|
|Penrose: executeMarketFn|70163|69873|-290|
|Penrose: registerBigBang|642580|641998|-582|
|Penrose: registerBigBangMasterContract|93519|93405|-114|
|Penrose: registerSingularity|825605|825380|-225|
|Penrose: registerSingularityMasterContract|86800|86686|-114|
|Penrose: setBigBangEthMarket|38557|38551|-6|
|Penrose: setConservator|48610|48583|-27|
|Penrose: setFeeTo|47908|47902|-6|
|Penrose: setSwapper|47839|47833|-6|
|Penrose: setUsdoToken|500985|501671|+686|
|Penrose: updatePause|31794|31785|-9|
|Penrose: withdrawAllMarketFees|317314|316521|-793|
|SGLLeverage: multiHopBuyCollateral|1009039|1007769|-1270|
|SGLLeverage: multiHopSellCollateral|842239|841288|-951|
|SGLLiquidation: liquidate|521910|519982|-1928|
|Singularity: addAsset|167554|166879|-675|
|Singularity: removeAsset|126112|125164|-948|
|USDO: approve|44401|44383|-18|
|USDO: burn|37224|37222|-2|
|USDO: mint|63606|63603|-3|
|USDO: setBurnerStatus|54152|54149|-3|
|USDO: setMinterStatus|51346|51343|-3|
|USDO: setTrustedRemote|95703|95661|-42|
|YieldBox: depositAsset|101076|101051|-25|
|YieldBox: registerAsset|111345|111351|+6|
|||||
|Deployment: BigBang|5115971|5475477|+359506|
|Deployment: Penrose|3973361|4038587|+65226|
|Deployment: SGLLeverage|4407614|4870213|+462599|
|Deployment: SGLLiquidation|4339490|4888947|+549457|
|Deployment: Singularity|5248515|5510432|+261917|
|Deployment: USDO|5603853|5611998|+8145|

*Gas saved: -15598*

**tapioca-periph-audit** 

    default: 300 runs
    CurveSwapper.sol: 5000 runs
    UniswapV2Swapper.sol: 5000 runs
    UniswapV3Swapper.sol: 5000 runs
    MagnetarV2.sol: 500 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|CurveSwapper: swap|281471|281414|-57|
|MagnetarV2: burst|275827|275816|-11|
|UniswapV2Swapper: swap|230085|229975|-110|
|UniswapV3Swapper: swap|225984|225955|-29|
|||||
|Deployment: CurveSwapper|1350630|1492559|+141929|
|Deployment: MagnetarV2|5362407|5364351|+1944|
|Deployment: UniswapV2Swapper|1366250|1550209|+183959|
|Deployment: UniswapV3Swapper|2171082|2335795|+164713|

*Gas saved: -207*

**tapioca-yieldbox-strategies-audit** 

    default: 200 runs
    YearnStrategy.sol: 5000 runs
    CompoundStrategy.sol: 5000 runs
    LidoEthStrategy.sol: 5000 runs
    TricryptoLPStrategy.sol: 5000 runs
    AaveStrategy.sol: 5000 runs
    ConvexTricryptoStrategy.sol: 5000 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|AaveStrategy: compound|164651|164592|-59|
|CompoundStrategy: setDepositThreshold|47143|47131|-12|
|ConvexTricryptoStrategy: compound|699846|699047|-799|
|ConvexTricryptoStrategy: setMultiSwapper|38596|38542|-54|
|ConvexTricryptoStrategy: setTricryptoLPGetter|61450|61396|-54|
|LidoEthStrategy: setDepositThreshold|47140|47128|-12|
|TricryptoLPStrategy: emergencyWithdraw|371296|371172|-124|
|TricryptoLPStrategy: setTricryptoLPGetter|61382|61349|-33|
|YearnStrategy: setDepositThreshold|47143|47131|-12|
|YieldBox: depositAsset|178634|178584|-50|
|YieldBox: registerAsset|111415|111411|-4|
|YieldBox: withdraw|150249|150164|-85|
|||||
|Deployment: AaveStrategy|2746846|3060844|+313998|
|Deployment: CompoundStrategy|1158728|1294060|+135332|
|Deployment: ConvexTricryptoStrategy|2612712|2811562|+198850|
|Deployment: LidoEthStrategy|1219565|1351675|+132110|
|Deployment: TricryptoLPStrategy|2606494|2953777|+347283|
|Deployment: YearnStrategy|1121992|1254641|+132649|

*Gas saved: -1298*

**tapiocaz-audit** 

    default: 300 runs
    TapiocaOFT.sol: 10 runs
    TapiocaWrapper.sol: 10 runs
    Balancer.sol: 1000 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|Balancer: initConnectedOFT|111608|111572|-36|
|Balancer: rebalance|143242|143204|-38|
|BaseTOFT: sendFrom|220527|220773|+246|
|BaseTOFT: sendToStrategy|339476|339632|+156|
|ERC20: approve|46037|46038|+1|
|ERC20: transfer|42938|42967|+29|
|TapiocaOFT: unwrap|58783|58816|+33|
|TapiocaOFT: wrap|93861|93893|+32|
|TapiocaWrapper: createTOFT|5486792|5431196|-55596|
|TapiocaWrapper: executeTOFT|72012|72046|+34|
|||||
|Deployment: Balancer|1006081|1096222|+90141|
|Deployment: TapiocaOFT|5313127|5232067|-81060|
|Deployment: TapiocaWrapper|814960|787868|-27092|

*Gas saved: -55139*

**tap-token-audit** 

    default: 100 runs
    oTAP.sol: 5000 runs
    Vesting.sol: 5000 runs
    TapOFT.sol: 1500 runs
    TapiocaOptionLiquidityProvision.sol: 1000 runs
    AirdropBroker.sol: 1000 runs
    TapiocaOptionBroker.sol: 5000 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|AirdropBroker: aoTAPBrokerClaim|46652|46634|-18|
|AirdropBroker: collectPaymentTokens|56460|56417|-43|
|AirdropBroker: newEpoch|47694|47589|-105|
|AirdropBroker: participate|135616|135735|+119|
|AirdropBroker: registerUserForPhase|631464|621530|-9934|
|AirdropBroker: setPaymentToken|57369|57336|-33|
|AirdropBroker: setTapOracle|71015|70994|-21|
|OTAP: approve|48673|48658|-15|
|OTAP: batch|94499|94379|-120|
|OTAP: permit|76762|76705|-57|
|TapiocaOptionBroker: collectPaymentTokens|56224|56176|-48|
|TapiocaOptionBroker: exerciseOption|163739|163286|-453|
|TapiocaOptionBroker: exitPosition|110233|109687|-546|
|TapiocaOptionBroker: newEpoch|210458|210054|-404|
|TapiocaOptionBroker: oTAPBrokerClaim|46587|46554|-33|
|TapiocaOptionBroker: participate|290389|289696|-693|
|TapiocaOptionBroker: setPaymentToken|59695|59656|-39|
|TapiocaOptionBroker: setPaymentTokenBeneficiary|29397|29382|-15|
|TapiocaOptionBroker: setTapOracle|70960|70921|-39|
|TapiocaOptionLiquidityProvision: batch|94755|94654|-101|
|TapiocaOptionLiquidityProvision: lock|199906|200019|+113|
|TapiocaOptionLiquidityProvision: permit|76852|76786|-66|
|TapiocaOptionLiquidityProvision: registerSingularity|150368|150199|-169|
|TapiocaOptionLiquidityProvision: setSGLPoolWEight|52846|52724|-122|
|TapiocaOptionLiquidityProvision: unlock|92311|92256|-55|
|TapiocaOptionLiquidityProvision: unregisterSingularity|52191|52028|-163|
|TapOFT: claimRewards|375305|374192|-1113|
|TapOFT: extractTAP|65453|65417|-36|
|TapOFT: lockTwTapPosition|386935|386424|-511|
|TapOFT: permit|75719|75712|-7|
|TapOFT: removeTAP|37457|37433|-24|
|TapOFT: setMinter|49209|49200|-9|
|TapOFT: setTrustedRemote|95554|95518|-36|
|TapOFT: setTwTap|47157|47130|-27|
|TapOFT: transfer|52562|52526|-36|
|TapOFT: unlockTwTapPosition|220699|219931|-768|
|TwTAP: distributeReward|86887|86885|-2|
|Vesting: claim|111148|111109|-39|
|Vesting: init|98222|98180|-42|
|Vesting: registerUser|65303|65273|-30|
|||||
|Deployment: OTAP|1854373|2067897|+213524|
|Deployment: TapiocaOptionBroker|2844098|3135305|+291207|
|Deployment: TapiocaOptionLiquidityProvision|2861468|3102873|+241405|
|Deployment: TapOFT|5252762|5546896|+294134|
|Deployment: Vesting|838851|960779|+121928|

*Gas saved: -15740*

**YieldBox** 

    default: 200 runs
    NativeTokenFactory.sol: 3125 runs
    YieldBox.sol: 6250 runs

Applying these changes, gas report evolves as follows :

||avg before|avg after|difference|
|-|:-:|:-:|:-:|
|MasterContractFullCycleMock: run|606991|606538|-453|
|NativeTokenFactory: burn|39544|39534|-10|
|NativeTokenFactory: claimOwnership|29240|29228|-12|
|NativeTokenFactory: createToken|184232|184177|-55|
|NativeTokenFactory: mint|64213|64203|-10|
|NativeTokenFactory: transferOwnership|37680|37624|-56|
|YieldBox: batchTransfer|122280|122202|-78|
|YieldBox: createToken|183427|183295|-132|
|YieldBox: deposit|154547|154466|-81|
|YieldBox: depositAsset|128097|127984|-113|
|YieldBox: depositETH|202190|202091|-99|
|YieldBox: depositNFTAsset|133159|133114|-45|
|YieldBox: mint|71592|71582|-10|
|YieldBox: registerAsset|123417|123387|-30|
|YieldBox: safeBatchTransferFrom|123629|123571|-58|
|YieldBox: safeTransferFrom|56154|56097|-57|
|YieldBox: transfer|55345|55268|-77|
|YieldBox: transferMultiple|109823|109670|-153|
|YieldBox: transferOwnership|27241|27226|-15|
|YieldBox: withdraw|62079|62053|-26|
|||||
|Deployment: NativeTokenFactory|2375846|2549337|+173491|
|Deployment: YieldBox|5038198|5383625|+345427|

*Gas saved: -1570*



**This optimisations is only intended to show the potential for better cases and to provide a starting point as it can be vastly discussed** 