# 🛠️ Analysis - TapiocaDAO Project 
### Summary
| List |Head |Details|
|:--|:----------------|:------|
|a) |The approach I followed when reviewing the code | Stages in my code review and analysis |
|b) |Analysis of the code base | What is unique? How are the existing patterns used? |
|c) |Test analysis | Test scope of the project and quality of tests |
|d) |Centralization risks | How was the risk of centralization handled in the project, what could be alternatives? |
|e) |Systemic risks | Potential systemic risks in the project |
|f) |Competition analysis| What are similar projects? |
|g) |Security Approach of the Project | Audit approach of the Project |
|h) |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
|i) |Gas Optimization | Gas usage approach of the project and alternative solutions to it |
|j) |Project partner-integrators | It is important with whom the project is integrated and with whom it does business, so you can understand the professional structure. |
|k) |Other recommendations | What is unique? How are the existing patterns used? |
|l) |New insights and learning from this audit | Things learned from the project |
|n) |Summary of Vulnerabilities | Audit Results |
|m) |Time spent on analysis | Time detail of individual stages in my code review and analysis |


## a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.
https://github.com/code-423n4/2023-07-tapioca#scoping-details

Accordingly, I analyzed and audited the subject in the following steps;

| Number |Stage |Details|Information|
|:--|:----------------|:------|:------|
|1|Compile and Run Test|[Installation](https://github.com/code-423n4/2023-07-tapioca#quickstart)|Test and installation structure is simple, cleanly designed|
|2|Architecture Review|The [twAML](https://mirror.xyz/tapiocada0.eth/aZanJAiDJOqC0dZWiM_L1Mg2mg-yHz2Nl82ALMUpxC0) explainer provides a high-level overview of the Project system and the [docs](https://docs.tapioca.xyz/tapioca/) describe the core components.|Provides a basic architectural teaching for General Architecture|
|3|Graphical Analysis  |Graphical Analysis with [Solidity-metrics](https://github.com/ConsenSys/solidity-metrics)|A visual view has been made to dominate the general structure of the codes of the project.|
|4|Slither Analysis  | [Slither Report](https://github.com/code-423n4/2023-05-maia#slither)|It is difficult to run slither in this project because the scope is so wide, this detail about it will do the job : https://ethereum.stackexchange.com/a/107492 |
|5|Test Suits|[Tests](https://github.com/code-423n4/2023-07-tapioca#tests)|In this section, the scope and content of the tests of the project are analyzed.|
|6|Manuel Code Review|[Scope](https://github.com/code-423n4/2023-07-tapioca#files-in-scope)|Top-down analysis of codes according to architectural design, IDE used: VsCode|
|7|Project Certora Audit |[Certora Audit Reports](https://docs.tapioca.xyz/tapioca/information/audits-and-partners#audits)|This reports gives us a clue about the topics that should be considered in the current audit|
|8|Infographic|[Figma](https://www.figma.com/)|I made Visual drawings to understand the hard-to-understand mechanisms|
|9|Special focus on Areas of  Concern|[Areas of Concern](https://docs.tapioca.xyz/tapioca/core-technologies/twaml)|Since twAML is a new and complex system, more emphasis will be placed on the design and implementation of twAML in codes.|

## b) Analysis of the code base

The Tapioca protocol is built with a lot of different smart contracts, scattered across 5 repositories. It's an Omnichain protocol working the LayerZero messaging layer. At its core, Tapioca ERC20/ERC721 contracts uses the LayerZero OFTv2 and ONFT721 contracts.

What is Omnichain then?
Omnichain is the principal of a protocol offering the ease of use of transfering hustle-free assets between chains. In order to transfer funds from Ethereum to Solana or vice-versa, for example, you needed to either use a CEx (centralized exchange) or a bridging protocol like Wormhole with high fees and long waiting queues.

<img width="1492" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/b5b4b01a-041e-48e6-af01-c6154171eec8">

<br />
<br />
<br />
<br />
<br />

<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/d56d980d-5827-42bf-9768-37afe9fe4dd6">


|File|Description|
|:-|:-|
|[tapioca-bar-audit/contracts/markets/singularity/SGLCollateral.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCollateral.sol)|Singularity collateral module|
|[tapioca-bar-audit/contracts/markets/singularity/SGLBorrow.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLBorrow.sol)|Singularity borrowing module|
|[tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDOStorage.sol) |Base USDO contract|
|[tapioca-bar-audit/contracts/usd0/USDO.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol)|USDO stablecoin|
|[tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLendingCommon.sol)|Singularity base contract|
|[tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol)|Singularity storage layout| |
|[tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol) |Singularity module for leverage| |
|[tapioca-bar-audit/contracts/markets/MarketERC20.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol) |Base contract for Market.sol| |
|[tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol) |Singularity base contract|
|[tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol) |USDO Module for Singularity|
|[tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol)|USDO Module for TapiocaBrokerOption.sol calls| |
|[tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol)|USDO Module for leverage| |
|[tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol)|Singularity module for liquidations|
|[tapioca-bar-audit/contracts/usd0/BaseUSDO.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol) |Custom LayerZero OFT logic, inherited in USDO|
|[tapioca-bar-audit/contracts/Penrose.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol) |Owner contract for USDO & BB|
|[tapioca-bar-audit/contracts/markets/singularity/Singularity.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol)|Lending & borrowing| |
|[tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol) |Mint and burn USDO through CDP| |
|[tapioca-bar-audit/contracts/markets/Market.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol) |Base contract for BigBang & Singularity| |


<br />
<br />



<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/7d2c230d-4348-4e47-828c-21494b13f4bc">

|File|Description|
|:--------|:-|
|[tap-token-audit/contracts/tokens/LTap.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol) |ERC20 aoTAP 1:1 redeemer| |
|[tap-token-audit/contracts/options/oTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol)|ERC721 Option meta contract| |
|[tap-token-audit/contracts/option-airdrop/aoTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol)|Forked version of oTAP| |
|[tap-token-audit/contracts/Vesting.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol)|Vesting contract||
|[tap-token-audit/contracts/tokens/TapOFT.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol)|Tapioca protocol token| |
|[tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol)|Singularity ERC20 receipt token vault| |
|[tap-token-audit/contracts/option-airdrop/AirdropBroker.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol)|Smaller version of TapiocaOptionBroker to mint & exercise LTAP| |
|[tap-token-audit/contracts/governance/twTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol) |ONFT721 governance token|  |
|[tap-token-audit/contracts/options/TapiocaOptionBroker.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol) |Mint & exercise oTAP||
|[tap-token-audit/contracts/twAML.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol) |Math library||
|[tap-token-audit/contracts/tokens/BaseTapOFT.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol) |Base TapOFT contract| |


<br />
<br />

<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/e59c3e97-d7cc-4ce5-8cde-d0f966d165ea">

|File|Description|
|:-|:-|
|[tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/TapiocaOFT.sol) |OFTv2 compliant wrapped token, with new custom functions||
|[tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFTStorage.sol)|Base TOFT EVM storage layout| |
|[tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol)|Special TOFT implementation that can balance its supply||
|[tapiocaz-audit/contracts/TapiocaWrapper.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol) |TOFT create2 deployer| |
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol) |Base TOFT YieldBox module| |
|[tapiocaz-audit/contracts/Balancer.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol) |Contract that balance out a mTapiocaOFT supply| |
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol)|Base TOFT Singularity market module| |
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol)|Base TOFT TapiocaOptionBroker market module| |
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol) |Base TOFT leverage module| |
|[tapiocaz-audit/contracts/tOFT/BaseTOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol)|Base TOFT contract|

<br />
<br />

<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/f8709314-f7aa-4271-96c9-a480ad7cbace">


|File|Description|
|:-|:-|
|[tapioca-periph-audit/contracts/oracle/implementations/GLPOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/GLPOracle.sol)|GLP Oracle|
|[tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol)|Tapioca contract deployer|
|[tapioca-periph-audit/contracts/oracle/implementations/SGOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/SGOracle.sol)|Stargate finance oracle|
|[tapioca-periph-audit/contracts/oracle/Seer.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/Seer.sol)|Oracle contract, uses best of ChainLink/UniV3 price feed|
|[tapioca-periph-audit/contracts/Multicall/Multicall3.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol)|Multicall contract|
|[tapioca-periph-audit/contracts/oracle/implementations/ARBTriCryptoOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/ARBTriCryptoOracle.sol)|TriCrypto oracle| 
|[tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol)|Curve swapper contract| 
|[tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol)|UniV2 swapper contract|
|[tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol)|UniV3 swapper contract|
|[tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol) |Magnetar storage layout| |
|[tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol) |Magnetar Singularity module| 
|[tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol) |Helper contract that interacts with Singularity, BigBang, TapiocaOptionBroker| 
|[tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol)|Base swapper contract for other swapper contract| 

<br />
<br />
<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/8297bb24-5962-4e38-83bd-86d08f89fb43">

|File|Description|
|:-|:-|
|[YieldBox/contracts/NativeTokenFactory.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol)|Creates ERC1155 tokens|
|[YieldBox/contracts/YieldBoxURIBuilder.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxURIBuilder.sol)|Inherited by YieldBox| 
|[YieldBox/contracts/YieldBox.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol) |Main Yieldbox contract| 
|[YieldBox/contracts/YieldBoxPermit.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol)|EIP-2612 for YieldBox| 
|[YieldBox/contracts/BoringMath.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol)|Simple math lib|
|[YieldBox/contracts/YieldBoxRebase.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol) |Math lib for internal accounting| 

<br />
<br />

<img width="942" alt="image" src="https://github.com/Tapioca-DAO/tap-token-audit/assets/104318932/f31d4cf8-d21f-42f4-b5ec-6bb9d5068280">



|File|Description|
|:-|:-|
|[tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol)|Yearn strat| 
|[tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol)|Compound strat| 
|[tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol) |TriCrypto LP strat| 
|[tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol)|TriCrypto native strat| 
|[tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol)|TriCrypto LP strat| 
|[tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol) |Stargate strat| 
|[tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol)|Stargate strat| 
|[tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol)|Balancer strat| 
|[tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol) |GLP strat| 
|[tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol) |TriCrypto strat|

<img width="942" alt="image" src="https://github.com/alcueca/ERC3156-Wrappers/assets/104318932/f30556b8-d78c-49b8-b06a-b9af2835ceaf">

The `USDO.sol` contract enables the use of USDO, the Tapioca omnichain stablecoin, detail in the contract; Flashloan feature is added in ERC3156 standard,


The `USDO.maxFlashLoan()` function returns the maximum amount of flashLoan that can be borrowed according to eip-3156 standards.

```solidity
tapioca-bar-audit/contracts/usd0/USDO.sol:
   56 /// @notice returns the maximum amount of tokens available for a flash mint
   57: function maxFlashLoan(address) public view override returns (uint256) {
   58: return maxFlashMint;
   59: }

```

The returned `maxFlashMint` value comes as a fixed value in `BaseUSDOStorage.Constructor()` as below, but it does not take into account the token's totalSupply, it will cause a logic error if it has not minted below 100,000 yet or if it drops below 100,000 after the burn.

```solidity

  constructor(
         address _lzEndpoint,
         IYieldBoxBase _yieldBox
     ) OFTV2("USDO", "USDO", 8, _lzEndpoint) {
         uint256 chain = _getChainId();
         allowedMinter[chain][msg.sender] = true;
         allowedBurner[chain][msg.sender] = true;
         flashMintFee = 10; // 0.001%
         maxFlashMint = 100_000 * 1e18; // 100k USDO @audit-issue this value constant

         yieldBox = _yieldBox;
     }
```

This design also contradicts this requirement stated in the ERC-3156 specification.

https://eips.ethereum.org/EIPS/eip-3156
```
The maxFlashLoan function MUST return the maximum loan possible for token. 
If a token is not currently supported maxFlashLoan MUST return 0, instead of reverting.
```


<br />
<br />

<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/2af80f1b-aaab-4f1e-90e7-9d2d7a756596">

The contract is base contract that handles common functionalities for USDO (a stablecoin token) related operations, such as leverage, market, and options trading. 

The contract defines an enumeration called "Module," which represents the different modules of the system (Leverage, Market, and Options).

The contract has several functions that can only be called by the owner, such as setting max flash mintable amount, setting the flash mint fee, setting the Conservator address, updating the pause state of the contract, and setting/unsetting addresses as minters/burners.

<img width="1621" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/840d5005-be20-4329-a33a-56454809989d">

<br />
<br />

<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/a0b37d36-7993-42d9-a87a-cc7762339518">


The MarketERC20.sol contract defines the Market ERC20 capabilitites.
The ERC20Permit.sol import file should be renewed, I recommend using the latest version of OZ.


```diff
tapioca-bar-audit/contracts/markets/MarketERC20.sol:
- 5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
+ 5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";

tap-token-audit/package.json:
  16:   "devDependencies": {
- 25:     "@openzeppelin/contracts": "^4.8.2",
+ 25:     "@openzeppelin/contracts": "^4.9.0",

```


<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/c93478a6-69ba-48f7-b223-cba3b3db1dc8">

 Market contract implemented by Singularity & BigBang;

|  Contract  |         Type        |       Bases      |                  |                 |
|:----------:|:-------------------|:----------------|:----------------|:---------------:|
|     Market.sol     |  **Function Name**  |  **Visibility**  |  **Mutability**  |  **Modifiers**  |
| Market.sol  | setBorrowOpeningFee | External ❗️ | 🛑  | onlyOwner |
| Market.sol  | setBorrowCap | External ❗️ | 🛑  | notPaused onlyOwner |
| Market.sol  | setMarketConfig | External ❗️ | 🛑  | onlyOwner |
| Market.sol  | updatePause | External ❗️ | 🛑  |NO❗️ |
| Market.sol  | computeClosingFactor | Public ❗️ |   |NO❗️ |
| Market.sol  | computeTVLInfo | Public ❗️ |   |NO❗️ |
| Market.sol  | updateExchangeRate | Public ❗️ | 🛑  |NO❗️ |
| Market.sol  | computeLiquidatorReward | Public ❗️ |   |NO❗️ |
| Market.sol  | _accrue | Internal 🔒 | 🛑  | |
| Market.sol  | _getRevertMsg | Internal 🔒 |   | |
| Market.sol  | _computeMaxBorrowableAmount | Internal 🔒 |   | |
| Market.sol  | _isSolvent | Internal 🔒 |   | |
| Market.sol  | _computeMaxAndMinLTVInAsset | Internal 🔒 |   | |
| Market.sol  | _getCallerReward | Internal 🔒 |   | |
| Market.sol  | _computeAllowanceAmountInAsset | Internal 🔒 |   | |
| Market.sol  | _getRatio | Private  🔐 |   | |




|  Symbol  |  Meaning  |
|:--------:|-----------|
|    🛑    | Function can modify state |
|    💵    | Function is payable |
|    🔒    | Internal |
|    🔐    | Private |



<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/2d112546-7ba2-4012-b2da-ab3e6bc1957c">



The `Singularity.execute()` function allows batched call to Singularity. This function, which can run all the functions in the sigularity contract, can also be added to a call that can run the `execute()` function again, this can cause unforeseen problems.Recommendation add to re-entrancy modifier

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

Pause architecture in `Singularity.sol` contract is interesting, pause control is made during 3 functions, but there is no modifier and control during other functions,
Consider adding comprehensive, user-friendly documentation on the 'Pause' mechanism implemented. The documentation should highlight under what conditions the system should or should not be paused, what the specific consequences of pausing are on system mechanics, and who is allowed to trigger this feature. For future versions of the protocol, it is recommended to design and implement mechanisms that support decentralization, allowing users to exit the system before behavior changes.;


|   |         Type        |       Bases      |                  |                 |
|:----------|:-------------------|:----------------|:----------------|:---------------|
|           |  **Function Name**  |  **Visibility**  |**Details**   |  **Modifiers**  |
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



<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/fdb4c3ce-ffe7-421b-9fe5-3b0453898158">

<img width="1483" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/38a0f2eb-61db-451b-9d2d-e6cab35239d6">



<img width="942" alt="image" src="https://github.com/code-423n4/2023-07-tapioca/assets/104318932/27a70b5a-869e-46e2-924b-884d4cdd4055">


## c) Test analysis
What did the project do differently? ;
The project, while designing the tests, modeled it with the "Threat Model", wrote the tests and documented it and presented it to the auditors, which increases the auditability and gives us the project team's view of the threat models specifically for the project.

What could they have done better?;
There are many unit tests in the project, integration tests in which the interaction of contracts with each other are modeled should be increased.

For example , this below codes for Chainlink `LatestRoundData `can be add in test suits

```solidity
pragma solidity ^0.8.0;

import "@forge-std/Test.sol";

import {MockChainlinkOracle} from "@test/mock/MockChainlinkOracle.sol";
import {ChainlinkUtils} from "tapioca-periph-audit/contracts/oracle/utils/ChainlinkUtils.sol";

contract ChainlinkUtilsIntegrationTest is Test {
    ChainlinkUtils public oracle;

    /// @notice multiplier value
    address public constant cbEthEthOracle =
        0xF017fcB346A1885194689bA23Eff2fE6fA5C483b;

    /// @notice eth usd value
    address public constant ethUsdOracle =
        0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419;

    function setUp() public {
        oracle = new ChainlinkUtils(ethUsdOracle, cbEthEthOracle, address(0));
    }

    function testSetup() public {
        assertEq(oracle.base(), ethUsdOracle);
        assertEq(oracle.multiplier(), cbEthEthOracle);
        assertEq(oracle.decimals(), 18);
    }

    function testcbETH_USD_CompositeOracle() public {
        uint256 price = oracle.getDerivedPrice(
            cbEthEthOracle,
            ethUsdOracle,
            18
        );
        assertTrue(price > 0, "Price should be greater than 0");
    }

    function testTestLatestRoundData() public {
        (
            uint80 roundId, /// always 0, value unused in ChainlinkOracle.sol
            int256 answer, /// the composite price
            uint256 startedAt, /// always 0, value unused in ChainlinkOracle.sol
            uint256 updatedAt, /// always block.timestamp
            uint80 answeredInRound /// always 0, value unused in ChainlinkOracle.sol
        ) = oracle.latestRoundData();
        uint256 price = oracle.getDerivedPrice(
            cbEthEthOracle,
            ethUsdOracle,
            18
        );

        assertTrue(answer > 0, "Price should be greater than 0");

        assertEq(price, uint256(answer));
        assertEq(updatedAt, block.timestamp);
        assertEq(roundId, 0);
        assertEq(startedAt, 0);
        assertEq(answeredInRound, 0);
    }
}
```


### Why  integration tests is important?
![image](https://github.com/code-423n4/2023-07-tapioca/assets/104318932/109aa8ae-5754-4c5c-9244-3c22a7b96560)


### Test suites do not test for attack vectors, especially re-entrancy

Test teams are testing many functions and variables, but recently, due to the vulnerability in the Vyper Compiler, the hacking of the projects using certain Vyper compiler and losing 50 million $ has revealed the security weakness here.
https://cointelegraph.com/news/curve-finance-pools-exploited-over-24-reentrancy-vulnerability

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




##  d) Centralization risks 
Having a single EOA as the only owner of contracts is a large centralization risk and a single point of failure;
https://gist.github.com/CloudEllie/eafeb9865761200397c00d70febe5f9d#m01-the-owner-is-a-single-point-of-failure-and-a-centralization-risk
  
##  d) Systemic risks 
According to the nformation of the project, it is stated that it can be used in rollup chains and popular EVM chains.

```js
README.md:
  193: - Is it multi-chain?:  True
```
We can talk about a systemic risk here because there are some Layer2 special cases.
Some conventions in the project are set to Pragma ^0.8.19, allowing the conventions to be compiled with any 0.8.x compiler. The problem with this is that Arbitrum is [Compatible](https://developer.arbitrum.io/solidity-support) with 0.8.20 and newer. Contracts compiled with these versions will result in a non-functional or potentially damaged version that does not behave as expected. The default behavior of the compiler will be to use the latest version which means it will compile with version 0.8.20 which will produce broken code by default.
<br />

Other mportant system risk in the project; Risks arising from the use of Oracle, the project uses Chainlink  oracles, security exploits from oracles have been increasing recently, so using Oracle is a risk in itself and it is important to minimize this risk.

## f) Competition analysis
Tapioca is heavily modified version of Kashi lending & borrowing;
https://medium.com/sushiswap-org/introducing-kashi-lending-margin-trading-on-sushiswaps-bentobox-eb91286f6910


## g) Security Approach of the Project

Successful current security understanding of the project;
1 - First they did the main audit from a reputable auditing organization like Certora and resolved all the security concerns in the report
2- They manage the 2nd audit process with an innovative audit such as Code4rena, in which many auditors examine the codes.
3- After the Code4rena bug bounty, they will set the $ 500,000 ImmuneFi reward


What the project should add in the understanding of Security;
1- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. (This will increase coverage)
2- After the project is published on the mainnet, there should be emergency action plans (not found in the documents)

## h) Other Audit Reports and Automated Findings 

**Automated Findings:**
https://gist.github.com/CloudEllie/eafeb9865761200397c00d70febe5f9d

Especially Medium detections in the Automated Finding Report should be taken into account;

| |Issue|Instances|
|-|:-|:-:|
| [[M&#x2011;01](#m01-the-owner-is-a-single-point-of-failure-and-a-centralization-risk)] | The `owner` is a single point of failure and a centralization risk | 85 | 
| [[M&#x2011;02](#m02-excess-funds-sent-via-msgvalue-not-refunded)] | Excess funds sent via `msg.value` not refunded | 1 | 
| [[M&#x2011;03](#m03-contracts-are-vulnerable-to-fee-on-transfer-accounting-related-issues)] | Contracts are vulnerable to fee-on-transfer accounting-related issues | 4 | 
| [[M&#x2011;04](#m04-unsafe-use-of-transfertransferfrom-with-ierc20)] | Unsafe use of `transfer()`/`transferFrom()` with `IERC20` | 4 | 
| [[M&#x2011;05](#m05-use-of-transferfrom-rather-than-safetransferfrom-for-nfts-in-will-lead-to-the-loss-of-nfts)] | Use of `transferFrom()` rather than `safeTransferFrom()` for NFTs in will lead to the loss of NFTs | 1 | 
| [[M&#x2011;06](#m06-return-values-of-transfertransferfrom-not-checked)] | Return values of `transfer()`/`transferFrom()` not checked | 6 | 
| [[M&#x2011;07](#m07-_safemint-should-be-used-rather-than-_mint-wherever-possible)] | `_safeMint()` should be used rather than `_mint()` wherever possible | 3 | 


**Other Audit Reports (Certora):**

Here is a summary of the issues found in the Certora audit report:
[Audit Report Certora](https://3014726245-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FfBay3bZwWmLdUX02P7Qc%2Fuploads%2FZ0lrDxhdqWHMX5UdMrtC%2FTapioca_Certora_Audit.pdf?alt=media&token=9fbc7bd3-cccd-42a1-b6d3-22c36e2b795f)
[Audit Report Certora Appendix](https://3014726245-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FfBay3bZwWmLdUX02P7Qc%2Fuploads%2FPIVXvEmuf0FzFZi1poyS%2FTapioca_Certora_Audit%20(Appendix).pdf?alt=media&token=49ad2755-e67c-4fb7-9d6b-3fea9081be12)

- Insecure direct object references
- Cross-site scripting (XSS) vulnerabilities
- Insecure cryptographic algorithms
- Insufficient input validation


## i) Gas Optimization
The project is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size

The highest gas expenditure in the project occurs when creating pool instances, if wish the architecture here was design like the singleton architecture in UniswapV4, gas savings will be close to 90%.


##  j) Project partner-integrators
It is important with whom the project is integrating and with whom it does business, so you can understand the professional structure, as can be seen, partnership and integration with important projects and people in the sector are provided, the contribution of this detail to the safety of the project is important.

### Partners & Integrators:
[BoringCrypto](https://github.com/boringcrypto/) (License of Kashi, Yieldbox Development)
[Chaos Labs](https://chaoslabs.xyz/) (Risk Management of TapiocaDAO & USDO)
[Arrakis Finance](https://www.arrakis.finance/) (USDO Uniswap V3 LP management / Financial Supporter)
[Y2K Finance](https://www.y2k.finance/) (USDO insurance/peg volatility trading)
[Redacted Cartel](https://redacted.finance/) (Pirex/Hidden Hand support for oTAP)
[Jones DAO](https://www.jonesdao.io/) (jAssets Collateral / Financial Supporter)
[Lido](https://lido.fi/) & [Rocket Pool](https://rocketpool.net/#header) (Use of Liquid Staking Derivatives)
[LayerZero](https://layerzero.network/) (Cross-Chain Infrastructure / Financial Supporter)
[Gelato Network](https://www.gelato.network/) (Automation)
[Berachain](https://berachain.com/) (Network Support)
[Certora](https://www.certora.com/) (Auditor)
[Code4Arena](https://code4rena.com/) (Auditor)
[ProtocolGuild](https://protocol-guild.readthedocs.io/en/latest/index.html) (Ethereum Core Incentives)


## k) Other recommendations

- Logic similar to singleton pattern similar to Uniswapv4 can be used in a project, this is a gas-optimized and innovative solution
- The use of assembly in project codes is very low, I especially recommend using such useful and gas-optimized code patterns;
https://github.com/dragonfly-xyz/useful-solidity-patterns/tree/main/patterns/assembly-tricks-1
- It is seen that the latest versions of imported important libraries such as Openzeppelin are not used in the project codes, it should be noted.
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/


## l) New insights and learning from this audit 

- I learned how to make a quality airdrop lunch with a mechanic : https://docs.tapioca.xyz/tapioca/launch/option-airdrop
- I have mastered the details of the Eip-3156 Flashloan standard used in the USDO token contract

## n) Summary of Vulnerabilities 

The list and details of all the results that I analyzed the project and shared in Code4rena are shared separately.

 Audit Results
- [x] **Medium  01** → Missing deadline checks allow pending transactions to be maliciously executed
- [x] **Medium  02** → Uniswap V3 oracles do not work properly on Optimism/Base
- [x] **Medium  03** → USDO `maxFlashLoan` function u can call value higher than totalSupply and logic error occurs
- [x] **Medium  04** → Missing Re-Entrancy Guard to `exitPositionAndRemoveCollateral`  function
- [x] **Medium  05** → APR rate lower than `minimumInterestPerSecond` and higher than `maximumInterestPerSecond` can be defined
- [x] **Low 06** → `zroPaymentAddress;` is given twice in the arguments of the `BaseUSDO.triggerSendFrom()` function and it is not checked whether these values are the same
- [x] **Low 07** → The constant ` flashMintFee ` set in `Constructor` is not competitive.
- [x] **Low 08** → There are payable functions in the `BaseUSDO.sol` contract, but there is no withdraw function to withdraw Ethers in the contract
- [x] **Low 09** → The Pause architecture in the `Singularity.sol` contract is missing.
- [x] **Low 10** → There may be problems with the use of Layer2
- [x] **Low 11** → It is safer to define the value of InterestPerSecond with a single variable instead of 2 separate variables
- [x] **Low 12**→ `Market.sol` contract has inconsistent `callerFee` fee definition value
- [x] **Low 13**→ Considering the `USDO` as a fixed $1 causes problems.
- [x] **Low 14**→ `setMarketConfig()` function design is unsafe 
- [x] **Low 15**→ The `execute()` function can be re-entranted
- [x] **Low 16**→ Use a more recent version of OpenZeppelin dependencies
- [x] **Low 17**→ `execute()` function is not payable and does not process `msg.value` but subaccount is payable and wants to process `msg.value`, this makes the function unusable
- [x] **Low 18**→ If onlyOwner runs `renounceOwnership()` in the `TapiocaWrapper` contract, the contract may become unavailable
- [x] **Low 19**→ `ERC20Permit.sol` OZ contract import path is incorrect
- [x] **Low 20**→ There is a `receive()` function, but there is no `withdraw` mechanism to withdraw Ether from the contract
- [x] **Low 21**→ A security-risk compilation version of Vyper is used in the code of the project.
- [x] **Non-Critical  22**→ Unused Import
- [x] **Non-Critical  23**→ Assembly Codes Specific – Should Have Comments
- [x] **Non-Critical  24**→ Does not `event-emit` during significant parameter changes
- [x] **Non-Critical  25**→ Remove the unused `_executeViewModule` private function.
- [x] **Non-Critical  26**→ Test suites do not test for attack vectors, especially re-entrancy
- [x] **Non-Critical  27**→ Project Upgrade and Stop Scenario should be
- [x] **Non-Critical  28**→ Missing Event for  initialize
- [x] **Suggestion   29**→ Use descriptive names for Contracts and Libraries


## m) Time spent on analysis
I allocated about 9 days to the project, most of this time was spent with architectural examination and analysis.

### Time spent:
43 hours