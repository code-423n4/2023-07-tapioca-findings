# Summary
|        | Issue | Instances | Gas Saved |
|--------|-------|-----------|-----------|
|[G-01]|Correction/Addition to  automated findings [G-09]|7|-16 164|
|[G-02|Use `calldata` instead of `memory`|23|-8 334|
|[G-03|Change compiler version + [G01] automated finding criticism|-|-7329|
|[G-04|Change the optimizer runs number|-|-|

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
These instances do not seem to have been identified.
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
Using `calldata` instead of `memory` for function parameters can save gas if the argument is only read in the function
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
The number of setup runs in the optmizer mean how many times the contracts are meant to be used. A huge number of runs improves the running cost, but penalizes the deployment cost, and inversely. 
This depends on the deploier's vision and can therefore be widely discussed. It's not 100% relevant to optimize this through gas report, as it doesn't always represent a normal use of contracts. However, it is the only readily available metric and will therefore be used. With this in mind, the purpose of this optimization is simply to show the possible variations and thus highlight the importance of correctly setting up the number of runs.

To shorten the report, only the percentage of total variations (always excluding certain contracts) are shown.
*Changes are made using hardhat configuration files*
**tapioca-bar-audit** 
By default, it is set to 10, and it evolves in the following way:
|Runs:| 1| 100| 200| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.003%| -0.120%| -0.153%| -0.194%| +0.131%| +1.453%| +1.445%| +2.490%| +2.372%| +2.369%|
|Deployment:| 0.000%| +1.030%| +1.844%| +3.237%| +6.613%| +10.516%| +12.525%| +16.654%| +12.400%| +13.408%|

500 runs seems like a good compromise.
**tapioca-periph-audit** 
By default, it is set to 300, and it evolves in the following way:
|Runs:| 1| 10| 100| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.105%| +0.105%| +0.070%| 0.000%| -0.010%| -0.031%| -0.050%| -0.056%| -0.107%| -0.107%|
|Deployment:| -1.688%| -1.686%| -1.319%| +0.430%| +3.555%| +10.929%| +15.589%| +19.578%| +24.416%| +25.947%|

Change doesn't seem necessary

**tapioca-yieldbox-strategies-audit** 
By default, it is set to 200, and it evolves in the following way:
|Runs:| 1| 10| 100| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.157%| +0.157%| +0.058%| -0.049%| -0.063%| -0.133%| -0.177%| -0.182%| -0.201%| -0.207%|
|Deployment:| -0.888%| -0.888%| -0.517%| +0.658%| +4.437%| +11.935%| +17.561%| +20.758%| +23.568%| +29.543%|

50000 runs seems like a good compromise.
**tapiocaz-audit** 
By default, it is set to 300, and it evolves in the following way:
|Runs:| 1| 10| 100| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.108%| +0.108%| +0.052%| -0.004%| -0.149%| -0.151%| -0.151%| -0.201%| -0.317%| -0.317%|
|Deployment:| -2.286%| -2.289%| -1.624%| +0.288%| +5.179%| +7.830%| +9.296%| +12.447%| +21.049%| +21.049%|

100000 runs seems suitable.
**tap-token-audit** 
By default, it is set to 100, and it evolves in the following way:
|Runs:| 1| 10| 200| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.105%| +0.091%| -0.036%| -0.058%| -0.111%| -0.141%| -0.158%| -0.225%| -0.234%| -0.236%|
|Deployment:| -1.197%| -1.128%| +1.123%| +2.428%| +6.038%| +10.122%| +13.213%| +19.267%| +24.477%| +28.270%|

20000 runs seems like a good compromise.

**YieldBox** 
By default, it is set to 200, and it evolves in the following way:
|Runs:| 1| 10| 100| 500| 1000| 5000| 10000| 20000| 50000| 100000|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Usage:| +0.691%| +0.691%| +0.113%| -0.042%| -0.055%| -0.105%| -0.155%| -0.163%| -0.276%| -0.276%|
|Deployment:| -1.509%| -1.509%| -1.183%| +2.180%| +5.039%| +11.199%| +14.833%| +17.594%| +20.734%| +20.733%|

100000 runs seems suitable.

**This optimisations is only intended to show the potential for better cases.** Because the gas evolution it enables vastly surpass all other conceivable optimizations.