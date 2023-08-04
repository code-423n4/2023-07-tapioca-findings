# Tapioca Dao Analysis

## Table of Contents

-   [Tapioca Dao Analysis](#tapioca-dao-analysis)
    -   [Table of Contents](#table-of-contents)
    -   [Introduction](#introduction)
-   [YieldBox](#yieldbox)
    -   [Summary](#summary)
-   [YieldBox Strategies](#yieldbox-strategies)
    -   [Aave](#aave)
    -   [Balancer](#balancer)
    -   [Compound](#compound)
    -   [Convex](#convex)
    -   [Curve](#curve)
    -   [GLP](#glp)
    -   [Lido](#lido)
    -   [Stargate](#stargate)
    -   [Yearn](#yearn)
    -   [Summary](#summary-1)
-   [USDO](#usdo)
    -   [Summary](#summary-2)
-   [BigBang](#bigbang)
    -   [Summary](#summary-3)
-   [Singularity](#singularity)
-   [Magnetar](#magnetar)
-   [Periphery](#periphery)

## Introduction

This report covers the various modules of the Tapioca Dao ecosystem. The table of contents above can be used to navigate to the various sections of the report and jump to relevant sections. A number of issues were discovered during the audit and reports were submitted explaining them in detail. this report is meant to be a primer to get a high level overview of the system, and does not discuss the reported issues in detail

The audit contest was held over a period of 31 days and I dedicated approximately 120 hours to this project.

# YieldBox

The core token handler of the ecosystem is the YieldBox. This is a wrapper contract which holds tokens in different strategies, earning yield as well as providing a common interface to handle them. These include ERC20/ERC721/ERC1155 tokens as well as native gas tokens, all of which are wrapped into ERC1155 tokens.

Tokens to be handled by the YieldBopx must be registered with the YieldBox using the `registerAsset` for ERC20/ERC721/ERC1155 tokens and the `createToken` function for native tokens. A strategy address must be also provided, and a unique `assetId` is generated for each token-strategy combination. While the registration functionality is available for all, a whitelisted set of these `assetId`s are tracked by the system and used throughout.

Interactions with the YieldBox are mainly done through the `depositAsset` and `withdraw` functions. Other normal ERC1155 functions like `transferFrom` are useful for transferring wrapped token positions without having to move the actual token. This helps since different tokens (gas/erc20/erc721) have different transfer functions and the ERC1155 interface provides a common interface for all of them.

When `depositAsset` is called, the YieldBox receives the tokens, identified by the passed `assetId`, and then forwards them to the relevant strategy contract. The flow of tokens are shown in the diagram below.

<!-- Diagram yieldbox assets -->

![YieldBox diagram](https://gist.githubusercontent.com/carrotsmuggler/164394ad91b2c0a6d93c3da047203b10/raw/0128e8d81ce4d2bf66687cb6adec7677aaa01c3f/yieldbox.png)

The tokens are moved into the strategy contract of the `assetId` passed, and the user is handed out an ERC1155 token representing the position. This ERC1155 can be transferred without ever actually touching the position in the yield generating protocol at the end. The tapioca dao ecosystem implements a number of strategies, each of which is discussed in detail in the following sections. The `withdraw` function is used to redeem the position and receive the underlying tokens back and has a similar flow with the arrows reversed.

The YieldBox also has an ERC4626 like vault mechanism which allows it to keep track of assets and shares in the strategies. Thus if the yield generating strategy generates tokens, the end user gets those tokens based on the fraction of shares they have. These calculations are done in the `toAmount` and `toShares` functions, and also has an in-built mechanism to prevent inflation attacks.

## Summary

1. Codebase Analysis
    - Unique: The contract provides a common way of handling multiple tokens and strategies with the same interface. This allows a large amount of composability with other defi platforms.
    - Existing patterns: The contract behaves like an ERC4626 vault, and has a similar interface as such. There is more functionality underneath, but the user is never made aware of that.
2. Architecture feedback
    - Since the contract uses the well-established ERC4626 vault pattern, there are few pitfalls in the system. The contract already provides protection from the inflation attack, which is one of the famous attack vectors for such systems. It builds on top of that system to also provide the smae service for ERC721 and ERC1155 tokens, as well as native gas tokens.
3. Centralization risks
    - Since each end user can control their own tokens, and the owner/deployer of the system has no special privileges, there are no centralization risks in the system.
4. Systemic Risks
    - The whole system is dependent on the strategy contracts. If they are broken or suffer losses, then that particular strategy can stop functioning, and that particular assetId can become worthless. However, the other assets are not affected by this.
    - This system does not handle fee on transfer tokens and ERC777 tokens. Since ERC777 tokens are not ERC20 tokens, this is not an issue since the system never promises to handle them. However fee on transfer tokens can cause accounting errors in the system, and should be avoided.

A uniform way of handling a large variety of tokens is implemented. Strategies are also implemented to generate yield on these tokens using the same interface for variety of products.

# YieldBox Strategies

The Yieldbox stores tokens in various defi protocols, to generate yields. The strategy contracts are interfaces to these protocols, and receive the tokens from the YieldBox, and are responsible for proper deposit, handling and compounding to generate the yield. The tapioca ecosystem implements a number of strategies, each of which are discussed further.

Strategies list:

1. [Aave](#aave)
2. [Balancer](#balancer)
3. [Compound](#compound)
4. [Convex](#convex)
5. [Curve](#curve)
6. [GLP](#glp)
7. [Lido](#lido)
8. [Stargate](#stargate)
9. [Yearn](#yearn)

## Aave

WETH tokens received by the aave strategy are deposited into the Aave lending protocol. The lending protocol accrues interest from users who take loans from the system, and a rebasing mechanism automatically increases the balance in the strategy's account. Yields can be boosted by staking Aave tokens and getting rewards in the form of Aave tokens. The compound function market sells these received tokens for WETH and deposits them into the lending protocol. Staked Aave tokens can be unstaked for a time window of 2 days after a cooldown of 20 days. The protocol also allows this cooldown functionality but mistakenly uses a window of 12 days instead of 22 days, which discussed in a submitted report in detail.

## Balancer

WETH tokens received by the strategy are used toprovide single-sided liquidity to the Balancer protocol. Balancer is an AMM protocol which holds 80-20 BAL-WETH tokens in this implementation. Thus some WETH tokens are converted to BAL tokens automatically by the Balancer contract in order to provide the liquidity. When required, withdrawals also withdraw tokens only in WETH, and the blaancer protocol does automatic swaps to swap the BAL tokens back to WETH.

The issue with this strategy is that is does a lot of swaps in the Balancer pools during deposits and withdrawals. Attackers can sandwich these swaps during deposits and withdrawals to profit from these swaps. This is discussed in detail in a submitted report.

## Compound

Similar to the Aave strategy, received WETH tokens are deposited into the compound lending protocol. No reward tokens are handled, and only the interest rate earned forms the yield generated by this strategy.

## Convex

Convex protocol sits on top of the curve protocol, and uses curve LP tokens to push more rewards towards relevant liquidity pools. This sits on top of the curve protocol, and thus inherits all the issues in the curve strategy described below. The convex strategy takes the deposited WETH and adds it to curve, and then stakes the received LP tokens for more yield. Rewards can be collected in multiple tokens, all of which are swapped back to WETH. The `compound` function in this contract does not sanitize the user input and can be misused, which is described in a submitted report.

## Curve

The curve protocol is also an AMM. The strategy deposits single-sided WETH liquidity to a curve pool. Similar to balancer, single-sided deposits and withdrawals mean that the protocol itself, in this case curve, does a swap to change part of the deposit to the other assets, in this case WBTC and USDT. Since swaps are done, this is also susceptible to sandwich attacks by the user. This is discussed in detail in a submitted report.

The curve strategy also implements a handler for LP tokens instead of WETH. This second strategy is meant to hold tricrypto LP tokens and generate yield from staking those tokens in LP gauges.

## GLP

The GLP protocol allows users to provide liquidity and take the opposite side of traders on the GLp protocol. The strategy contract takes the WETH tokens and uses the GLP router to buy GLP tokens. These tokens are then staked in the protocol to earn rewards in other tokens, which are regularly compounded by selling them all for WETH.

Again, since swaps are involved, the strategy is susceptible to sandwich attacks.

## Lido

The lido strategy takes WETH tokens and swaps them for stETH tokens via curve. stETH are ETH tokens staked on the beacon chain, and generates yield from block rewards. When withdrawing, the stETH is converted back to WETH. Since swaps are involved, this strategy is also susceptible to sandwich attacks.

## Stargate

The stargate strategy provides liquidity to the stargate protocol, which is a bridging protocol. The strategy takes the WETH tokens and submits them to the stargate bridge, and earns STG tokens as reward on top of the bridging fees. These reward tokens are then swapped back to WETH and compounded. Since this interfaces with a bridge, the common issue of bridges being honeypots for hackers is applicable here.

## Yearn

Yearn vaults deposit the asset in other strategies to generate yield. Thus the yearn strategy deposits WETH to the yearn vault, which in turn deposits that WETH to other strategies. The WETH vault on mainnet generates yield from Aave, Lido, Curve and compound, essentially being a mix of the above mentioned strategies.

## Summary

1. Codebase Analysis
    - Unique: A high amount of composability with other defi protocols is demonstrated by the system. The system also handles a large variety of tokens, and is able to generate yield on them.
    - Existing patterns: The strategies behave similarly to beefy vaults. Tokens are deposited to a strategy to generate yield and compounded. Some compounding protocols like YieldYak on Avalanche also gives the caller a cut of the compounded amount, incentivizing regular compounding. This is absent in this system.
2. Architecture feedback
    - There are some inconsistencies in the implemetations of the various strategies. Effort should be made to have every strategy follow the same set pattern. An example is the Aave strategy, which forgets to give approvals to a new swapper if added, and is discussed in a submitted report.
3. Centralization risks
    - The owner is allwed to withdraw tokens out of the underlying protocols, as well as determine the threshold above which tokens will be deposited to the underlying protocol. If set very high, the strategy will hold onto al tokens and never interact with the underlying protocol at all. The owner also can control the address of the swappers, and if broken, can halt all deposits and withdrawals. An example is the Aave strategy, which depends of a proper swapper to swap out the reward tokens for WETH. If broken, the strategy will be unable to compound and thus withdraw.
4. Systemic Risks
    - The strategy contracts are all susceptible to the inherent risks of the underlying protocol. On top of that, there are the risks of sandwich attacks whenever swaps are involved. This is partly due to an improper implementation of slippage protection on the swapper contracts which is discussed in detail in submitted reports
5. Other Recommendations
    - Due to the inherent MEV risks as well as price fluctuation risks involved in swaps, it is recommended to stick to protocols which dont do swaps. POCs have been developed using tenderly to show how to manipulate stETH and ETH pools for profit using this system for minimal costs in submitted reports.

# USDO

The USDO token is the stablecoin at the heart of the system. This token is minted by the CDP protocol BigBang, against a set of curated whitelisted high valued assets. The USDO token is omnichain using layerzero technology, and has a lot of cross chain operations. Tokens can not only be sent ot other chains, but also pulled from and simultaneously sent and swapped and then lent on a different chain. Overall, the system is very powerful and is close to being a chain agnostic system, allowing users to control operations on any chain from any chain.

The multichain operations are done via the layerzero system. This sytem depends on a distributed oracle network to submit the block header of a cross chain transaction, and an **independent** layerzero `relayer` to submit the proof of the transaction included in that block. It is crucial for the operation that these two parts remain independent. Cross chain messages are sent to an endpoint address, and are received from an endpoint address in the destination chain.

Due to the mutlichain nature, the USDO contract has two parts. One responsible for sending messages to the endpoint, and another responsible for receiving messages from an endpoint and carrying out the operations. This can be explained diagrammatically as shown below.

<!-- USDO diagram -->

![USDO diagram](https://gist.githubusercontent.com/carrotsmuggler/164394ad91b2c0a6d93c3da047203b10/raw/818070bb64fb4de4cf8e4bce732dd91c5fe5f4e2/USDO.png)

In function calls to the contract, the top half/ red lines are used. The user calls a function, and it gets delegated to a particular module. Modules are used to limit the size of the contract. Delegatecalls are used to preserve the context and only utilize the target's execution logic. The module manager is responsible for calling the correct module, and the final function sends off a message to the endpoint. This happens in the source chain.

In the receive chain, the bottom half/green lines are relevant. The endpoint calls the contract and is reecived by the `_lznonblockingreceive` function. This receiver also has a module manager which delegates functionality to the correct module. This module then calls the actual target function, for exmaple the BigBang market to lend tokens or do other operations. This happens on the destination chain.

Even though it is shown in the same diagram, the contract calls in red are done in the source chain, and the ones in green are done in the destination chain. This makes the USDO contract very powerful and able to make complex cross-chain calls. Debiting/crediting accounts are handled in the relevant functions inside modules.

The operations are controlled by codes, which are loaded onto the message sent to the lz endpoints. As an exmaple, the `PT_SEND_FROM` constant stores the code 778, and when this code is received in the destination, the contract knows which module to delegate the call to. Different codes are used for different operations: cross chain send, cross chain receive, exercising options, lending, repaying. All interactions at the end are done wither with the USDO contract itself on the destination chain, or the BigBang or Singularity market contracts.

## Summary

A novel cross chain service is introduced which handles a multitude of operations, and is chain agnostic. This lets users control their tokens from any chain.

1. Codebase Analysis
    - Unique: Personally, the cross chain operations were quite new and took a while for me to understand completely. Previous experience with layerzero was limited to token transfers, which only had the `PT_SEND` operation.
    - Existing patterns: The contract derives heavily from the OFT standard set by layerzero, standardising cross chain operations. While the OFT token only uses the `PT_SEND` operation, the protocol here extends the functionality to other operations as well.
2. Architecture feedback
    - The protocol implements a lot of `pull` type functionality. For example the `PT_SEND_FROM` operation allows users to pull tokens from a different chain to the current chain. Thus there is an inherent assumption that the owner of the wallet calling these functions on every chain is the same. This however is not true for contract wallets. We can draw reference to the Wintermute incident, where the wallet holders on the Optimism chain and on mainnet were different due to how gnosis deployed wallets using beacon proxies. The current implementation of Gnosis wallets fix this using create2 opecode, but many wallets created in the old method still remain. This poses a serious risk and must be adsressed, since owner on chain A can pull tokens form the owner on chain B while being different sets of people.
3. Centralization risks
    - The owner is allowed to set minter and burner addresses for the USDO token. This gives the owner the ability to burn any tokens or mint infinite tokens. Thus, unless the ownership is revoked, the system is heavily centralized.
4. Systemic Risks
    - The system is dependent on layer-zero. Layer zero assumes that the relayer and the oracle are independent and cannot collude. However if the same party does get to control both systems, they can mint any number of tokens make the USDO system worthless.
    - The risk of contract wallets have been discussed above, as well as in a submitted report.
    - There is a lack of approval checks in the contracts. This allows users to drian the wallets and manipulate market positions of other users. This has been reported in detail in a submitted report.

# BigBang

THe BigBang market creates collateralized debt positions (CDPs) using trustworthy tokens as collateral. This allows the system to have a healthy collateral set to mint USDO tokens against. The interest rates users must pay are determined by the current debt positions against various assets. Debts against ETH is determined `safest`, and all other rates are determined by the ratio of the debt against that token to the debt against ETH. While the codebase is novel and built to be modular to reuse large parts of the code for the Singularity market, it behaves similarly to other CDP protocols, like Abracadabra or Makerdao.

The collateral tokens are deposited in yieldboxes which generate yield for the user. The system also uses a soft liquidation mechanism, where users are liwuidated only upto the point of making their health factor 1, and no more. This provides protection to the users, and also provides protection against market manipulations, since the entire position cannot be liquidated by manipulating the price by a little amount.

Liquidators are given an incentive, and are encouraged to liquidate as early as possible. The liquidation incentive starts at 10%, and drops down as the position becomes more and more unsafe. Users can also set operators who can operate accounts on their behalf. This is due to the Magnetar contract, which is designed to be a helper contract to manage BigBang and Singularity markets.

Like other CDPs, users can deposit collateral and borrow USDO. Interest and fees are tracked using rebase mathematics similar to ERC4626 vaults. The system also allows users to leverage up in a single click, by allowing a user to borrow USDO, swap them for more collateral, and then deposit them in their position again in the same transaction. The system disallows others from depositing collateral into a user's position, unless specifically allowed by the user.

## Summary

1. Codebase Analysis
    - Unique: The variable debt rate system is quite unique and allows for finer risk control.
    - Existing patterns: The protocol is similar to other CDP protocols. Qi DAO vaults, similar to BigBang, also allow users to deposit collateral tokens to generate yield and has soft liquidation mechanics.
2. Architecture feedback
    - The architecture is slightly different from other CDP protocols. In conjunction the Singularity markets, end users can borrow tokens against any collateral, but the separate BigBang and Singularity markets allow for finer risk control. This is a good design choice.
3. Centralization risks
    - The penrose contract is made the owner of the BigBang market. The penrose contract itself has an owner, who thus becomes the owner of the BigBang market. The owner is allowed to change fee rates of the protocol at will. The owner is also allowed to set the oracle address and thus control the oracle price. This canbe used to unfairly liquidate positions as well as borrow all USDO in the market. Thus the system is considered to be centralized. However owners cannot blacklist addresses, and thus cannot prevent users from using the system.
4. Systemic Risks
    - A lot of mathematics related issues have been reported in separate reports. The system, like all other CDPs, is heavily reliant on liquidators and thus any mathematical issues related to that mechanism need to be fixed.
    - The system is also reliant on the yieldbox strategies. This is because the collateral balance is not the actual number of tokens, but what the yieldbox strategy thinks is the balance. Some strategies use single sided liquidity, whose price can be manipulated. Thus collateral values can be manipulated and users can be liquidated. This has been reported in a separate reports but is actually a risk of the strategy itself. The system can be made more robust by using strategies which do not use single sided deposits.

# Singularity

Singularity is a system of isolated lending markets built on top of BigBang. The purpose of this is to create markets with only two tokens: USDO and some other token as collateral. Lenders are allowed to deposit USDO tokens and borrowers can deposit the other token as collateral and borrow USDO against their position. This allows end-users to borrow USDO against any tokens. Thus BigBang allows users to borrow USDO against a set of tokens, while Singularity allows users to borrow USDO against any token. This is a good design choice to limit risk.

Lenders in the singularity markets take the other side of the borrowers. Thus in case of a situation where the collateral token suddenly drops in value, only the lenders in that particular market are affected and the systemic risk is contained.

Since both protocols have the same base code, the Singularity protocol inherits a lot of the issues of the BigBang system. For example, liquidations can be halted since the amount to be liquidated depends on the raw amount borrowed, and does not take into account the interest accrued on that position. Thus a position can have health factor lower than 1, but not undergo liquidation as long as the interest-free value of the borrow is above the allowed borrow limit. This has been reported in a separate report along with other mathematical issues.

Singularity utilizes non-linear interest rates, allowing steady rates over a range of utilization ratios. This incentivizes users to maintain the system at healthy levels. Similar to BigBang markets, the system also allows users to leverage with a single click.

1. Codebase Analysis
    - Unique: The protocol allows crosschain leveraging. Users can borrow assets, send them to another chain to swap them and then bring them back. This is a unique feature.
    - Existing patterns: The protocol is heavily inspired by the kashi lending system. Similar isolated lending markets and interest rate models are used.
2. Architecture feedback
    - The architecture is similar to kashi lending markets with some cross chain operations added in. Since this isolates risk from the rest of the system, this is a good design choice.
3. Centralization risks
    - Similar ot the BigBang markets, the owner can control the oracle and thus drain the protocol. Thus this is a centralized system. However the system is still permissionless.
4. Systemic Risks
    - Similar to BigBang, the system is reliant on liquidation maths and yieldbox strategies. These have been reported in separate reports.
    - Insolvent positions cannot be removed from the system. The admin or the treasury cannot choose to use their own funds to pay off a bad loan. This is because the protocol expects the collateral after swap to be enough to cover the loan. This can be a design choice of the system.

# Magnetar

Magnetar is a helper contract to control user positions on Singularity/BigBang markets. It mainly bundles together multiple operations in the same transaction for more efficient gas usage. Thus it inherits all the features and issues of the Singularity and BigBang markets.

Users are expected to give permission to the Magnetar contract to operate on their behalf by setting it as a valid operator in the BigBang/Singularity market.

The most critical issue with this design is that there are no approval checks on the entire contract, and thus any user A can call a function on user B and have it pass, since the Market contracts check the approval of the Magnetar contract, and not the user A. This is a critical issue and has been reported in a separate report.

Since this is just a helper contract, it does not have any centralization or architectural risks on its own.

# Periphery

Other periphery contracts are the swapper contracts, which perform swaps on Uniswap V2, V3 and curve. The slippage protection in these contracts have issues that are reported in separate reports. Each contract has an implementation of a price expectation, which allows the caller to calculate how many tokens to expect from a swap. This calculation however is done on chain in a lot of cases and can thus return manipulated values.

Other important periphery contracts are the oracle contracts. They mainly use chainlink to get the price of the tokens. One implementation interacts with the trictypto pool on Arbitrum, which the curve developers have suggested to withdraw out of and stop interacting with after the recent slew of curve hacks due to vulnerable vyper compiler versions. This is a risk to the system.


### Time spent:
120 hours