# Analysis of the TapiocaDAO Contest

# Introduction
- The TapiocaDAO Protocol is aiming to solve one of the key downsides that currently exists in the DeFi landscape which is, unifying the liquidity that is dispersed among the different chains into a single omnichain.
- The protocol is using the LayerZero as the messaging layer to enable cross-chain communication, and instead of relying in the old techniques of bridging assets between chains, their proposition is by using LayerZero `OFT` and `ONFT` to handle the liquidity as if it were in a single chain.
- The protocol is aiming to create the first truly decentralized and natively interoperable over-collateralized stablecoin, the USDO, which is solely collateralized by network gas tokens, such as ETH, and liquid staking derivatives such as Lido stETH & Rocket Pool rETH.
- The protocol will have two types of markets:
  - The BigBang markets, which will allow users to mint USDO through the creation of CDPs(collateralized debt positions or loans), the collateral on this Market will be the network gas tokens as well as their liquid staking derivated.
  - The Singularity markets, which will allow USDO minters to generate a yield on their minted USDO, and will allow borrowers to take USDO loans by using as collateral other accepted ERC20 tokens
    - Singularity markets will accept common ERC20 tokens as collateral
  
- I personally like how the protocol planned the lifecycle of Liquidity Providers, to provide liquidity in the Singularity Markets, they first need to mint USDO, and thanks to the yield generated in the Singularity market, the debt position that was taken on BigBang to mint USDO will kinda self-repaid.

- There are a lot of incentives to stake the liquidity, such as oTAP and twTAP, which by having the liquidity stake gives more stability to the protocol.
- A very interesting feature is how the protocol will generate POL (Protocol Owned Liquidity)

## Analysis of the Codebase
- The codebase of this audit included 6 repositories: the `tapioca-bar`, `tapioca-periph`, `tap-token`, `tapioca-yieldbox-strategies`, `tapiocaz` & `YieldBox`
  - The core repository containing most of the core technologies is the `tapioca-bar`
    - `tapioca-bar` contains the contracts for the Singularity and BigBang markets, as well as the USDO stablecoin
  - The `tapioca-periph` contains contracts that act as helpers for the core contracts located in the tapioca-bar repo. This repo `tapioca-periph` contains the MagnetarV2 contract which acts as a helper that reduces the number of user-taken actions/transactions.
  - The `YieldBox` repo contains the logic of the `vaults`, that allow for yield strategies to be applied on the asset.
  - The `yieldbox-strategies` has the contracts of the different strategies that will be used by the YieldBox
  - The `tap-token` has the contracts related to the tokenomics (twTAP, twAML, oTAP, lTAP)
  - The `tapiocaz` repo has the contracts that contain a wrapped named `TOFT`, which is used to wrap gas tokens and transfer and allow their usage through the LayerZero network.

![Relationship Diagram of the interaction among the core components](https://res.cloudinary.com/djt3zbrr3/image/upload/v1691169900/TapiocaDAO/relationship_diagram.png)
- To access the diagram file you need to use the draw.io Google application, this is the link to the file (https://drive.google.com/file/d/1mewVpxf3rXJZMBYtZ_uRo0c8eUCviVag/view?usp=sharing)

## Architecture Feedback, Centralization & Systemic Risks
- The protocol is aiming to unify the liquidity that is distributed among the different chains, the protocol is using LayerZero as the messaging layer.

- The two types of TapiocaMarkets (BigBang & Singularity) are highly interconnected with the Swappers and LiquidationQueue, both, Swappers and LiquidationQueue are the ones that connect the TapiocaMarkets with the Open Markets such as Uniswap and Curve.
  - Thanks to the Swapper and LiquidationQueue, as well as the usage of external Oracles, the price of the assets within the TapiocaMarkets is up-to-date in real-time when any operation is performed (borrowing, liquidating, repaying)
  
![High-Level diagram of TapiocaMarkets connected to the OpenMarkets](https://res.cloudinary.com/djt3zbrr3/image/upload/v1691170990/TapiocaDAO/TapiocaMarkets_and_OpenMarkets.png)
- To access the diagram file you need to use the draw.io Google application, this is the link to the file (https://drive.google.com/file/d/1NByIpdk1e_3qkJhzouSNT8kJakKw-79G/view?usp=sharing)

- About the Singularity Markets, there is a main contract, and some module contracts (because of the large size of the code) which all together enables all the features of the Singularity markets (Borrowing, Repaying, Leveraging, Liquidating, Adding and Removing Assets, Adding and Removing Collateral), the YieldBox vault plays a core role in the Singularity Markets because most of the operations end up either depositing or withdrawing assets from/to the YieldBox contract. In reality, the Singularity contracts don't hold assets, all the assets are handled by the YieldBox which depending on the active strategy the assets are used to generate yield.
  - The below diagram is a representation of the core operations that are made in a Singularity market, it visually represents the flow of each of those operations, all the validations, and state updates that are done when executing each operation.
    - Note: The image may not be well appreciated because it is a big image, please visit the link of the diagram stored in drive to fully appreciate the whole diagram.
![Singularity Markets State Machine Diagram](https://res.cloudinary.com/djt3zbrr3/image/upload/v1691171857/TapiocaDAO/SingularityMarket_StateMachine.png)
- To access the diagram file you need to use the draw.io Google application, this is the link to the file (https://drive.google.com/file/d/1pe_PEk2Kr23DWQ0tBfdy88wEayFLmby8/view?usp=sharing)


## Recommendations
- Based on the issues I caught on the codebase I'd recommend the protocol/devs to always keep in mind that no authorized entity should be allowed to perform any operation on behalf of the users.
  - Due to the system architecture, users can perform operations through intermediary contracts, for example using the Magnetar contract.
    - The users need to grant permissions to the Magnetar contract, but the fact that the Magnetar contract has permissions doesn't mean that anybody can use Magnetar to execute operations on behalf of the users.
      - Only the user or authorized entities by the user should be able to execute operations through the Magnetar contract on behalf of the user.

- Also, make sure to restrict the external contracts that the protocol's contracts are allowed to interact with.
  - Take advantage of the Penrose contract, (which is like a Fabric contract that has the list of valid markets), to validate if the provided external addresses for the Markets are indeed valid markets, otherwise, the protocol can risk the user's assets by allowing interaction with untrusted contracts, which as I have demonstrated in some of my submissions, attackers can deploy fake contracts and exploit the protocol's contracts, and as a result, attackers can steal user's assets.

# Time Spent on the Audit
- I spent around 18 - 20 days on this audit, each day I put in around 7-10 hours of work. In total that would be around 160 - 180 hours.
- 6 days were used to analyze and fully understand the codebase, this involves reading documentation as well as the codebase itself.
- 7 - 8 days were dedicated only to bug hunting, I didn't follow the recognition pattern, I was mostly focused on identifying critical issues caused by incorrect assumptions and bugs in the code that would allow malicious entities to exploit the contracts and steal users' assets.
- The last 3 - 4 days were dedicated to filling out the reports and coding the PoC, as well as writing this analysis and creating the diagrams.

### Time spent:
170 hours