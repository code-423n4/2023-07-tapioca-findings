NOTE: report is hosted as gist in case any issues w/ images happen through c4 submission at https://gist.github.com/0xreentrant/7cf0b42d399cface6b5f5d59bee5efcb

- [Architectural Review and Feedback](#architectural-review-and-feedback)
- [Risks to be considered with use of Third Party Services](#risks-to-be-considered-with-use-of-third-party-services)
- [Smart Contract Risks and Challenges to Auditing](#smart-contract-risks-and-challenges-to-auditing)
# Introduction

This analysis report delves into various sections and modules of the Tapioca DAO protocol that the RED-LOTUS team audited, specific sections covered are listed within the table of contents above.

Our approach included a thorough examination and testing of the codebase, research on wider security implications, economic risks applicable to the protocol and expanded discussion of risks associated with the use of cross-chain messaging services by Tapioca DAO. 

A number of potential attack vectors were identified related to theoretical weaknesses of game theory and inherent systemic risks associated with specific functionality of the protocol. We supplied feedback on specific sections of protocol architecture (twAML and Yieldbox) and give other recommendations as relevant.

Throughout our analysis, we aimed to provide a comprehensive understanding of the codebase, suggesting areas for possible improvement. To validate our insights, we supplemented our explanations with wider industry research, demonstrating robust comprehension of Tapioca's internal code functionality and interaction with LayerZero services.

Over the course of the 30-day contest, we dedicated approximately 380+ hours to auditing the codebase. Our ultimate goal is to provide a report that will give the project team wider perspective and value from our research conducted to strengthen the security posture, usability and efficiency of the protocol.

This analysis report is free to be shared with other parties as the project team see fit.

# Architectural Review and Feedback

- [Tapioca Ecosystem Analysis](#tapioca-ecosystem-analysis)
- [****YIΞLDBOX****](#yieldbox)
  * [Users](#users)
  * [YIELDBOX in Action](#yieldbox-in-action)
  * [Token Vault Exploits](#token-vault-exploits)

# Tapioca Ecosystem Analysis

### twAML - Time Weighted Average Magnitude Lock / Call Option - oTAP Token

**Time Weighted Escrowing**, and **Average Magnitude Lock**, colloquially together known as **twAML** is a novel Decentralized Finance economic system created by TapiocaDAO for promoting ****sustainable economic growth while maintaining economic alignment, that can react to economic activity to reach maximal allocative efficiency.

The Call Option TAP (oTAP) token is distributed as an in-the-money call option that is redeemable for an equivalent amount of TAP. Users can exercise their oTAP call option(s) to buy TAP for less than its current market price**.** This offers a user redeeming oTAP the ability to capture guaranteed profits ****by selling the redeemed TAP at the current market price.

### oTap Game Theory

As oTAP is based on twAML, discussion around twAML-related game theory will also be relevant to oTAP critical analysis.

twAML is based upon Rubinstein’s Bargaining Model, a fundamental concept in game theory that models the process of bargaining between two players.

### Key Concepts of Rubenstein’s Bargaining Model:

1. **Alternating Offers**: The model assumes that two players alternate in making offers to each other. The process starts with one player making an offer, and if the other player rejects it, they make a counteroffer, and so on.
2. **Time Preference**: Players are assumed to have a preference for reaching an agreement sooner rather than later. This is often represented by a discount factor, which reduces the value of future payoffs. The longer it takes to reach an agreement, the less valuable the agreement is to both players.
3. **Perfect Information**: Both players have perfect information about each other's payoffs and strategies. There are no secrets or hidden information in the game.
4. **Infinite Horizon**: The game is assumed to continue indefinitely until an agreement is reached. This means that players can make an infinite number of offers and counteroffers.
5. **Subgame Perfect Equilibrium**: The solution to the Rubinstein Bargaining Model is a subgame perfect equilibrium, which means that players' strategies constitute a Nash equilibrium in every subgame of the original game. In other words, no player can unilaterally deviate from their strategy at any point in the game and improve their payoff.

In the context of the model, the players' goal is to maximize their own payoff, taking into account the fact that the other player is trying to do the same. The model predicts that players will reach an agreement quickly, and the agreement will be efficient (i.e., it maximizes the sum of the players' payoffs). The exact division of the payoff depends on the players' discount factors: the player who values the future less (i.e., has a higher discount factor) will receive a larger share of the payoff.

### Potential Attack Vectors on the basis of using Rubenstein’s Bargaining Model:

1. **Sybil Attacks**: In a Sybil attack, an attacker creates multiple identities to gain a disproportionate influence on the network. If the bargaining model is used in a system where influence is tied to the number of identities (such as voting), a Sybil attack could manipulate the outcome.
2. **Front-Running**: In blockchain-based systems, miners can see transactions before they are confirmed and can potentially manipulate the order of transactions for their benefit. If a bargaining model is used in such a system (e.g., for a decentralized exchange), a miner could use this information to their advantage.
3. **Collusion**: If a group of participants collude, they can potentially manipulate the bargaining process to their advantage, at the expense of other participants. This could distort the intended outcomes of the model.
4. **Manipulation of Time Preference**: If an attacker can manipulate the perceived time preference (e.g., by artificially delaying transactions or information flow), they could potentially influence the bargaining process.
5. **Imperfect Information**: The model assumes perfect information, but in reality, information asymmetry can exist. If some participants have more or better information than others, they can use this to their advantage.
6. **Rationality Assumption**: The model assumes all participants are perfectly rational, but this is not always the case in real-world systems. Irrational behavior or bounded rationality can lead to unexpected outcomes.

We recommend to the project team that these hypothetical attack vectors are considered and tested when possible. With a wider timeframe and available project resources it would be possible to create robust tests for these scenarios, we recommend that the project do so to validate that the game theory deployed cannot be abused and result in exploitation of the protocol.

### Has use of Rubensteins Bargaining Model effectively solved the Liquidity Incentive Trilemma?

It remains to be seen once the Tapioca protocol is operational if indeed the project team has solved the Liquidity Incentive Trilemma and is able to retain economic interest in the protocol. With advanced economic and market modelling techniques, again it could be possible to test these assumptions - however, this will be out of scope for this analysis.

# YIELDBOX

**Resources have partly been sourced from Tapioca Documentation and Boring_Crypto** 

![Screenshot 2023-08-03 103724.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106504407855104/Screenshot_2023-08-03_103724.png)

### Bentobox V2: The Vault of the Tapioca Ecosystem

Yieldbox (or Bentobox V2) is a token vault created by BoringCrypto and the core contributors of TapiocaDAO. Bentobox V1 is currently in use by Sushiswap and Abracadabra (Degenbox). Yieldbox acts as the central token vault for the entire Tapioca ecosystem.

## Users

![Screenshot 2023-08-04 093706.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106504646918265/Screenshot_2023-08-04_093706.png)

One of the key features of YIΞLDBOX is its approach to yield farming and strategy selection. Currently, if a user wishes to participate in a yield farming strategy, they must first navigate to the website of the specific strategy they wish to join. This can be a cumbersome process, as users often need to become familiarized with each website, which sometimes have clunky UIs. Additionally, users must approve their tokens for each website individually, which can be a time-consuming and error-prone process.

## YIELDBOX in Action

Let us assume that I have some ETH and I want to deposit it on a lending platform to borrow USDC. I also want to use the same ETH to participate in a yield farming strategy. In order to do this, I would need to deposit my ETH into the strategy, which would give me a tokenized representation of my position. I could then deposit this tokenized position onto the lending platform to borrow USDC. Alternatively, I could swap the USDC for ETH and then loop the process again, effectively increasing my leverage.

Unfortunately, as of today, the tokenized positions that are issued by yield farming strategies cannot be used on lending platforms. This is because lending platforms typically require users to deposit the underlying asset, such as ETH, rather than a tokenized representation of the asset.

### YIELDBOX Solution

![Screenshot 2023-08-04 103818.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106505016033340/Screenshot_2023-08-04_103818.png)

With YIΞLDBOX, you only need to approve your tokens to the YIΞLDBOX, then you can assign it to any strategy you want, and can move those tokens between strategies. 

The first benefit is that every YIΞLDBOX asset is a token that creates an asset - with a strategy or without strategy - that behaves like ETH, so you can then take that if a lending platform is actually built on top of YIΞLDBOX, you can then just borrow USDC against it, so now you can have your cake and eat it, too, so you get the yield from the strategy, but also the benefit of the protocol.

One of the key benefits of YIΞLDBOX is that it allows users to "have their cake and eat it too." This means that users can participate in yield farming strategies and also borrow against their assets on lending platforms.

This is possible because YIΞLDBOX assets are tokenized representations of underlying assets, such as ETH. This means that YIΞLDBOX assets can be used on lending platforms that would otherwise only accept the underlying asset.

For example, if a user deposits ETH into a YIΞLDBOX strategy, they will receive a YIΞLDBOX asset in return. This YIΞLDBOX asset can then be used on a lending platform to borrow USDC. This allows users to earn yield from the strategy while also having access to liquidity.

Another benefit for users of YIΞLDBOX is that it simplifies the token approval process. With YIΞLDBOX, users only need to approve their tokens to the YIΞLDBOX contract once. After being approved, users can then access all of the strategies that are built on top of YIΞLDBOX without having to approve their tokens again.

![Screenshot 2023-08-04 104901.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106505531928637/Screenshot_2023-08-04_104901.png)

### Developers

![Screenshot 2023-08-04 111617.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106505779380294/Screenshot_2023-08-04_111617.png)

Most protocols in DeFi have to deal with tokens, such as ERC20 tokens and ERC721 NFTs. However, many protocols do not support ERC1155 tokens, which are a newer type of token that can represent multiple assets.

YIΞLDBOX supports all of these types of tokens, which means that developers can create yield farming strategies that support a wider range of assets. This can make it easier for developers to attract users and generate yield.

**Another challenge that most protocols face is dealing with rebasing tokens.** Rebasing tokens are tokens that increase or decrease in value over time, based on a formula. This can make it difficult for protocols to track the value of these tokens, and to calculate the yield that users are earning.

**YIΞLDBOX addresses this challenge by providing a built-in token factory.** This token factory allows protocols to create new tokens that are pegged to rebasing tokens. This means that the value of the new token will always be equal to the value of the rebasing token, regardless of how the rebasing token changes in value. This makes it much easier for protocols to track the value of rebasing tokens, and to calculate the yield that users are earning.

![Screenshot 2023-08-04 111726.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106506031054848/Screenshot_2023-08-04_111726.png)

 Additionally, the token factory is permissionless, which means that anyone can create new tokens. This can help to increase the liquidity of rebasing tokens, and to make it easier for users to trade them.

### Contract Stack

The core of the YIΞLDBOX contract is a standard ERC1155 implementation. This means that it provides the basic functionality that is needed for any ERC1155 token, such as minting, transferring, and burning tokens.

![Screenshot 2023-08-04 111902.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106506312069170/Screenshot_2023-08-04_111902.png)

The YIΞLDBOX contract also includes an asset register, which allows users to combine tokens with strategies. This means that users can earn yield on a wider range of assets, including ERC721 NFTs and rebasing tokens.

![Screenshot 2023-08-04 112306.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106506567909488/Screenshot_2023-08-04_112306.png)

Additionally, the YIΞLDBOX contract includes a native token factory, which allows protocols to create new tokens that are pegged to other tokens. This can be useful for protocols that want to create new tokens that are more liquid or that have different features than the underlying token. 

It also includes a boring factory, which is a simple clone creator. This allows protocols to create multiple copies of a contract, which can be useful for creating different versions of a strategy or for testing new features.

![Screenshot 2023-08-04 112441.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106506853138505/Screenshot_2023-08-04_112441.png)

Finally, the YIΞLDBOX contract includes a boring_crypto “batchable” function, which allows users to batch multiple calls to the YIΞLDBOX contract into a single transaction. This can save gas fees and improve the performance of the contract.

![Screenshot 2023-08-04 112612.png](https://cdn.discordapp.com/attachments/1137104015096811551/1137106507125764116/Screenshot_2023-08-04_112612.png)

## Token Vault Exploits

### Yearn Finance**:**

On April 13, 2023, Yearn Finance on the Ethereum chain was attacked due to a misconfiguration in the yUSDT vault. The attackers exploited this vulnerability and stole approximately $11.54 million.

### The Root Cause

The vulnerability was caused by a bug in the misconfigured yUSDT vault. The contract's fulcrum used the iUSDC token instead of the iUSDT token, which led to a mistaken dependency on the pool's underlying token. This vulnerability was exploited to mint a large amount of yUSDT tokens. The misconfiguration was present at the time of deployment and went unnoticed for approximately 1,000 days.

### **Attack Process**

The attacker initially funded their wallet using Tornado Cash, they then took a flash loan of 5 million DAI, 5 million USDC, and 2 million USDT. They deposited these funds into the yUSDT contract, which mints tokens that represent USDT deposits in Yearn Finance.

The attacker then redeemed yUSDT and withdrew all assets from the Aave V1 vault. They then minted bZxUSDC and sent it to the yUSDT contract, which increased the price of each share. After that the hacker triggered a rebalance, which led to the redemption of bZxUSDC into USDC. This effectively reduced the value of each yUSDT to zero. The hacker then deposited 1 wei of USDT to the yUSDT contract, allowing them to mint over 1 quadrillion yUSDT tokens.

The yUSDT was then swapped for USDT, USDC, and DAI in Curve pools. After paying back the borrowed flash loan, the hacker kept most of the stolen funds, which were worth about 11.54 million dollars.

### How the attack could have been prevented

The attack could have been prevented if proper validation and confirmation of the Fulcrum address had been performed before deployment. The deployer should have provided the correct address for the iUSDT token instead of iUSDC in the constructor. If this had been done, the attack would not have been possible.

Moreover, this demonstrates the importance of conducting a security review of deployment scripts to verify that all deployment parameters are correctly configured.

### Can YIΞLDBOX suffer a similar exploit? ****

We have checked the configuration of the YIΞLDBOX vault and concluded that it is not susceptible to the same type of attack as the previous one. This is because there are no critical inputs on the deployment script. However, further unit testing can still be implemented to ensure similar attacks like this are not viable.


# Risks to be considered with use of Third Party Services

  * [Cross-Chain Messaging Service](#cross-chain-messaging-service)
  * [LayerZero](#layerzero)
    + [Selection of LayerZero](#selection-of-layerzero)
    + [Risk Assessment Methodology (Used by Uniswap Foundation)](#risk-assessment-methodology--used-by-uniswap-foundation-)
    + [Have the Risks identified by the Uniswap Foundation been adequately mitigated?](#have-the-risks-identified-by-the-uniswap-foundation-been-adequately-mitigated-)
    + [Responsibility of TapiocaDAO in the event of a LayerZero Exploit](#responsibility-of-tapiocadao-in-the-event-of-a-layerzero-exploit)

Integrating third-party services into the Tapioca protocol introduces several risks that must be considered, a non-exhaustive list of these risks that should be considered and will be selectively analyzed are as follows:

1. **Smart Contract Risk**: Third-party services may contain bugs or vulnerabilities that can be exploited, potentially leading to the loss of funds or unauthorized access to sensitive data.
2. **Dependency Risks**: Relying on external services can create dependencies that may lead to failure points. If a third-party service becomes unavailable or changes its behavior, it can disrupt the entire system.
3. **Privacy Concerns**: Third-party services might have access to sensitive information, and their data handling practices may not align with the privacy requirements of the smart contract or its users.
4. **Economic Risks**: Costs associated with third-party services might fluctuate, leading to unexpected financial burdens. Additionally, malicious actors within third-party services could manipulate data to influence economic outcomes within the smart contract.
5. **Reputational Risk**: Any failure or negative incident associated with a third-party service can reflect poorly on the reputation of the smart contract, potentially eroding trust and user adoption.
6. **Governance and Control Issues**: Integrating third-party services may lead to a loss of control over certain aspects of the smart contract, potentially conflicting with the decentralized ethos of Web3 and creating governance challenges.

## Cross-Chain Messaging Service

We will specifically examine the use of Cross-Chain Messaging Services (CCMS) as it is an extremely topical discussion area within the Web3 Security Industry at the moment. The future of Web3 is certainly ‘Omnichain’ based, allowing seamless operations between otherwise segregated blockchains. The use of CCMS is a necessity in the Omnichain world but as recent exploits and security incidents have demonstrated, the risk associated with using CCMS must be thoroughly considered and understood by both the project team and end-users.

## LayerZero

This section will consider the risks associated with the use and architectural implementation of the LayerZero protocol to enable Omnichain functionality for TapiocaDAO. In light of the recent Multichain exploit in which lockup assets worth over $226 million USD were drained, security risks associated with the use of cross-chain messaging protocols should be robustly analyzed and key mitigating controls identified.

The RED-LOTUS team previously audited codebases that implement cross-chain messaging protocols to enable Omnichain functionality. We will use this valuable experience of identifying security vulnerabilities related to these implementations to inform our critical analysis of Tapioca DAO’s implementation of LayerZero.

In addition, we heavily referenced and included key takeaways from Uniswap’s Cross-Bridge Assessment Report ([https://uniswap.notion.site/Bridge-Assessment-Report-0c8477afadce425abac9c0bd175ca382](https://www.notion.so/Bridge-Assessment-Report-0c8477afadce425abac9c0bd175ca382?pvs=21)) which included LayerZero as a potential candidate for Uniswap V3’s expansion to the BNB chain. 

We put forward this critical analysis and discussion for use by the project team to inform future architectural decision making. It may also be used as a source of input for material that can be used to inform end-users of the risks associated with cross-chain messaging protocols and specifically the use of LayerZero within TapiocaDAO.

### Selection of LayerZero

We will critically analyze the selection  by the Tapioca project team to utilize LayerZero as their cross-chain messaging solution. The use of LayerZero has been previously mired in controversy due to allegations of serious security vulnerabilities. During the period when Uniswap V3 held a voting snapshot, it was alleged by staff at Nomad Bridge that a backdoor existed allowing the LayerZero team to bypass security controls to pass data without anyone’s permission. Detection of this vulnerability will be examined and in addition recommended mitigation techniques.

The new security feature that LayerZero has implemented called Pre-Crime will also be examined. It is referenced in the Tapioca documentation as one of the core considerations when Tapioca selected LayerZero. However, the project team has communicated to us that Pre-Crime is not currently implemented in the current commit of the codebase and will not be available when Tapioca first goes into production.

### Risk Assessment Methodology (Used by Uniswap Foundation)

The Uniswap Foundation utilized a risk assessment framework assessing four broad risk categories that follows below:

- **Protocol Architecture Risk:** Encompasses the risks stemming from the design of a protocol, including its primary validation mechanism and other architecturally significant components that impact the fundamental security properties, assumptions, and trust model associated with the protocol.
- **Protocol Implementation Risk:** Includes the risks associated with the implementation of a protocol’s specification. Building cross-chain protocols involves creating complex on-chain and off-chain components while accounting for the peculiarities and pitfalls of different programming languages, frameworks, and execution environments.
- **Protocol Operational Risk:** Refers to the various risks that arise from the operational security and management of different components, often by different actors with different trust assumptions and incentives. This could include upgrading and managing bridge smart contracts, as well as operating off-chain systems such as external validator nodes.
- **Network Risks:** Cross-chain protocols rely on certain assumptions about the safety and liveness guarantees of underlying networks. Consequently, these protocols have fundamental limitations in the guarantees they can offer. This category of risk encompasses how cross-chain protocols handle violations to these assumptions in any given network and mitigate the impact on other connected networks and applications.

For the purposes of this analysis we will also leverage this assessment framework.

The summary of the Uniswap Foundation’s risk assessment examining the four broad risk categories listed above was as follows:

“After assessing the current version of the LayerZero protocol, the Committee has concluded that it does not currently satisfy the full breadth of the requirements of the Uniswap DAO's cross-chain governance use case as outlined in the assessment framework, but is on a path to do so. LayerZero employs a combination of two types of validators to secure the protocol: Oracles and Relayers. However, currently, the available options for Oracles and Relayers do not offer the level of decentralization and security required for Uniswap's use case. LayerZero has a planned upgrade to its oracle and relayer set that would likely address these concerns.
A cursory analysis of the planned updates is promising. The Committee acknowledges that LayerZero is being used for the Avalanche deployment. The Committee encourages swift action from the LayerZero team in implementing this upgrade, and recommends that the protocol be reassessed once the new configuration has been in operation for period of at least three months and as attained sufficient usage, given that the performance of the validator set is a key factor in the assurances the protocol can offer around safety and liveness.
**NB**: The assessment of LayerZero was conducted prior to its [recent update](https://twitter.com/LayerZero_Labs/status/1663899945936756738?s=20) which employs a ZK Client as an additional validation option. This update may address some of the key concerns identified by the Committee but has not yet been assessed.”

Clearly this raises concerns about the suitability of LayerZero as an appropriate cross-chain messaging solution. The next section examines if the risks identified have been appropriately mitigated.

### Have the Risks identified by the Uniswap Foundation been adequately mitigated?

In the case of LayerZero, a significant advantage is that the application can select respective sets of relayers and oracles, and in case of an attack, only the respective combinations are affected. However, the number of parties needed to collude is small, a fact that especially needs to be considered when using its standard configuration of relayer and oracle. In this standard setup, the team and its investors wield a lot of influence.

********************************************************Tapioca Custom Configuration********************************************************

Tapioca utilizes a custom configuration largely mitigating the aforementioned risk of using the standard setup. The configuration uses Chainlink to move a requested block header from source to destination chain. As collusion would be required between Chainlink’s Oracle and Tapioca’s ‘Pearlayer’ (Relayer) for an integrity breach, the likelihood of an incident is certainly reduced - but not completely impossible.

As previously mentioned, it was alleged by staff at Nomad Bridge that a backdoor existed allowing the LayerZero team to bypass security controls to pass data without anyone’s permission. The use of custom configuration mitigates this risk, as the identified vulnerability relies on the application in question using standard LayerZero configuration.

The following testing steps can be taken to ensure that the vulnerability does not exist within the Tapioca protocol.

**Critical Vulnerability 1**

[In the endpoint contract,](https://etherscan.io/address/0x66A71Dcef29A0fFBDBE3c6a460a3B5BC225Cd675#readContract) call *uaConfigLookup* with the address of the application. If the receivingLibrary is set to 0x0, the application is vulnerable. 

**Critical Vulnerability 2**

Crit 2 occurs on a chain-by-chain basis. [In the ULNv2 contract](https://etherscan.io/address/0x4D73AdB72bC3DD368966edD0f0b2148401A178E2#readContract) call *appConfig* with the address of the application, and the relevant [chain id.](https://layerzero.gitbook.io/docs/technical-reference/mainnet/supported-chain-ids) If the *inboundProofLibraryVersion* returned is 0, the application is vulnerable

******************ZK Client******************

![Untitled](https://cdn.discordapp.com/attachments/1137104015096811551/1137105218841096212/Untitled.png)

Polyhedra’s zkLightClient technology, built on LayerZero, harnesses the compression of ZKP technology reduces the on-chain verification tremendously with lower latency by using efficient ZKP protocols. In addition, multiple transaction verifications can be batched into a single zero-knowledge proof.

Indeed, as promised by the LayerZero project team, Polyhedra’s zkLightClients have now been integrated on LayerZero. Now any dApp leveraging LayerZero’s tech stack can access the zkLightClients with configuration updates. Significantly increasing the security protections available and reducing risk exposure.

LayerZero’s ULNv2 validation library relies on two parties, the Oracle and Relayer, to transfer messages between on-chain endpoints. When LayerZero sends a message from chain A to chain B, the message is routed through the endpoint on chain A to the ULNv2 validation library. The ULNv2 library notifies the Oracle and Relayer of the message and its destination chain. The Oracle forwards the packet hash to the endpoint on chain B, and the Relayer submits the packet to be verified on- chain against the hash and delivers the message.

On-chain light clients allow for the source chain’s validator set to attest to something that occurred on their chain to a destination chain. Light clients, in conjunction with other libraries, add a layer of security on top of the LayerZero messaging protocol. On-chain transaction verification has been cost-prohibitive to the tune of $50m-$100m/day per pair-wise chain connected to Ethereum due to the presence of extensive transaction logs, which are necessary for the proof but not for the application itself.

However, we understand that the Tapioca team has not configured the protocol to use the light clients. Our risk analysis concludes that not utilising the enhanced protections available by use of light clients is a missed opportunity for end user protection and also enhanced marketing opportunities of the protocols security features.

******************************Circuit Breaker / Lessons learned from Multichain Breach******************************

Use of a circuit breaker has been widely discussed as an effective tool to prevent potenially exploit related transactions leaving the protocol. EIP: 7265 was proposed outlining a smart contract interface for exactly that, a Circuit Breaker triggering a temporary halt on protocol-wide token outflows when a threshold is exceeded for a predefined metric. For example, why would you ever let 100% of your TVL leave in 10 seconds or five blocks? A security control such as this, if implemented, would have largely prevented the recent Multichain breach.

Although not a ‘circuit breaker’, the Tapioca team have implemented what they call a ‘Conservator’ which gives the ability to pause markets, disable minting of USDO on a per market basis, among other narrow emergency-use-only actions. The ‘Conservator’ is part of the Tapioca DAO multisig, in a 4-of-7 Gnosis configuration. 

It is noted that the ‘Conservator’ still requires human intervention to identify an issue and then pause and resume markets. Although the intention is to be able to rapidly act to emergent unforeseen events, if required multisig members are unavailable for a wider number of reasons, execution of emergency actions will not be available.

There are projects such as ‘Zerem’ ([https://github.com/hananbeer/zerem/tree/667a41b577647f0d95591a5f9928a43b976b8e25](https://github.com/hananbeer/zerem/tree/667a41b577647f0d95591a5f9928a43b976b8e25)) which although still under development, implement a true circuit breaker process within the code to prevent massive loss of funds. It is suggested that the project team review such code implementations that could protect the protocol in such emergency scenarios.

### Responsibility of TapiocaDAO in the event of a LayerZero Exploit

Indeed, if there was a protocol exploit with the root cause emanating from security vulnerabilities within CCMS, we view this as materialization of a business critical risk that would effectively cause the viability and continued operations of the protocol, catastrophic failure. As such, we suggest that the risks presented within this analysis are carefully considered and decisions by accountable persons within the project team are taken accordingly.


# Smart Contract Risks and Challenges to Auditing

  * [Inconsistent, Floating versions of OpenZeppelin Contracts in `package.json`](#inconsistent--floating-versions-of-openzeppelin-contracts-in--packagejson-)
  * [Dead Code and Deprecated Code paths Create Potential Attack Vectors](#dead-code-and-deprecated-code-paths-create-potential-attack-vectors)
  * [Error Messages are unclear](#error-messages-are-unclear)
  * [Magic Numbers in Logic](#magic-numbers-in-logic)
  * [Inconsistent Use of NatSpec](#inconsistent-use-of-natspec)
  * [Test Coverage Covers Only the Golden Path](#test-coverage-covers-only-the-golden-path)
  * [Excessive Use of Inheritence](#excessive-use-of-inheritence)
  * [Project Structure Breaks Common Workflows](#project-structure-breaks-common-workflows)
  * [Issues with Comments](#issues-with-comments)


## Inconsistent, Floating versions of OpenZeppelin Contracts in `package.json`

Similar to the dangers of using floating solidity pragmas, using floating versions of 3rd-party libraries in the build process can introduce vulnerabilities to the protocol code. Similarly, while one developer might install a version of the libraries at one point, another developer on a team might install another version, which can cause one developer to have separate expectations as to the library’s invariants.

- Latest is 4.9.3 (as of July 28, 2023), latest at time of audit was 4.9.0 (as of May 23, 2023)
- Listed version dependency in package.json and version installed by Yarn at time of writing:
    - `tap-token-audit` : "@openzeppelin/contracts": "^4.8.2”, installed: 4.8.2
    - `tapioca-bar-audit` : "@openzeppelin/contracts": "^4.8.2", installed 4.8.2
    - `tapioca-periph-audit` : "@openzeppelin/contracts": "^4.5.0", installed 4.8.3
    - `tapioca-yieldbox-strategies-audit` : "@openzeppelin/contracts": "^4.5.0", installed 4.9.3
    - `tapiocaz-audit` : "@openzeppelin/contracts": "^4.5.0", installed 4.8.2

### Risks

1. Older versions of OpenZeppelin contracts might not have fixes for known security vulnerabilities.
2. Older versions might contain features or functionality that have been deprecated in recent versions. 
3. Newer versions often come with new features, optimizations, and improvements that are not available in older versions. 
4. The newer versions of OpenZeppelin contracts are designed to work with newer versions of the Solidity compiler. For instance, version 4.9.0 of OpenZeppelin contracts introduced support for user-defined value types, which were released in Solidity version 0.8.8.
5. Older versions might not receive the same level of support or maintenance as the newer versions. 

### Recommendation

Lock all 3rd-party libraries to a single version in `package.json` .  Upgrade the version when updates with security updates are released.

## Dead Code and Deprecated Code paths Create Potential Attack Vectors

Dead/deprecated code is usually the case when feature sets or developer intentions shift during the project’s lifetime.  In the case of smart contract programming, there is a risk that dead code can become an attack vector for malicious actors to exploit.  

There is dead/deprecated code in Balancer.sol that is related to mTapiocaOFT.sol.  While discussing with the developers available as C4 wardens, it was revealed that the protocol has chosen to use mTOFT only for native gas tokens.  While the decision was made after the bulk of the development for Balancer.sol, there still exists code (and related execution paths) for non-gas token related code. While not revealing explicit attack vectors from directly calling dead code, there will need to be heavy oversight as to which tokens are wrapped as an OFT to prevent malicious tokens from exploiting dead code paths.

### Risks

1. Increased oversight is necessary to prevent malicious actors from exploiting dead code

### Recommendation

Remove all dead or deprecated code as long as the protocol intends only to support a subset of the originally intended feature set.

## Error Messages are unclear

Meaningful error messages give better handles to test against. While building a test suite, edge cases can become numerous, and providing descriptive error messages allows not just for the project developers to write better tests, it helps developers of 3rd-party integrations and forks (other protocols, token projects, etc) to understand the intentions and reasoning of the parent project. 

In the Tapioca codebase, there are multiple places were the same custom reverts are used where the error cases are materially different. Some examples include:

- Ex. Balancer.sol

```solidity
bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
if (_isNative) {
    if (msg.value <= _amount) revert FeeAmountNotSet();
    _sendNative(_srcOft, _amount, _dstChainId, _slippage);
} else {
    if (msg.value == 0) revert FeeAmountNotSet();
    _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
}
```

- Ex. Balancer.sol:

Error message does not reflect the actual error condition

```solidity
if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
    revert RebalanceAmountNotSet();
```

### Risks

1. Project developers get the wrong understanding of why a case is erroneous.
2. Writing tests for specific error types becomes difficult because of a lack of unique errors provided by the executed code.
3. 3rd-party developers (forks, integrations) can have difficulties understanding developer intentions.

### Recommendation

Use more descriptive handler names and error messages and limit reusing error types for categorically-similar-yet-different errors.

## Magic Numbers in Logic

A big challenge when writing any code is communicating intent to other developers.  It’s been argued that the biggest concern for a project’s life cycle is the code quality, since the ability to understand, upgrade, and fix code is dependent on the humans involved, and those involved may change across the span of the project’s lifetime, may use different conventions, and event speak different languages.

A subset of this problem includes the ability to communicate intent through values used in logic, specifically the use of “magic numbers”.  Magic numbers are values that have no discernible meaning without reading documentation, introspecting in a system, or rely on arcane conventions.

In the Tapioca codebase there are only a few instances of magic numbers being used, and those include the use of `address(0)` to refer to the native gas token (ex. ETH).

### Risk

1. Developers reading the code may misunderstand the intention and code against a faulty mental model.

### Recommendation

Replace all uses of magic numbers with a named constant, ex. `NATIVE_TOKEN`

## Inconsistent Use of NatSpec

NatSpec is a boon to all Solidity developers.  Not only does it provide a structure for developers to document their code within the code itself, it encourages the practice of documenting code.  When future developers read code documented with NatSpec, they are able to increase their capacity to understand, upgrade, and fix code. Without code documented with NatSpec, that capacity is hindered.

The Tapioca codebase does have a high level of documentation with NatSpec. However there are numerous instances of functions missing NatSpec.

### Risks

1. Lack of understanding of code without adequate documentation.
2. Difficulty in understanding, upgrading, and fixing code without documentation.

### Recommendation

1. Practice consistent use of NatSpec on all parts of the project codebase. 
2. Include the need for NatSpec comments for code reviews to pass.

## Test Coverage Covers Only the Golden Path

The vast majority of exploits in DeFi don’t happen when expected code paths happen. Those are what some would call “golden path” cases, where everything is initialized, called, and executes correctly.  When a malicious attacker takes the time to understand a codebase, it’s the unexpected code paths that cause the damage.  

In the Tapioca codebase, there is a test suite of the major functionality, however there are few cases that would be considered outside the golden path.  

### Risks

1. Low test coverage means a notable portion of the codebase is not verified. This increases the possibility of undiscovered bugs and vulnerabilities in the smart contract's code.

### Recommendation

Increase the test coverage of the execution space by including tests that involve edge cases including: 

- Failures in external calls
- Possible breaks in invariants
- Misordered calls in multi-step processes

## Excessive Use of Inheritence

In the history of computer language development, the rise and fall of Object Oriented Programming (OOP) has been seen as a study in paradigms pushed through popularity rather than applicability.  A major one is the capacity to create deep hierarchies of inheritance.  That includes the Solidity language, which inherits, itself, many of the faults of OOP. 

Examples of the pains caused by inheritance hierarchies include 

- Obfuscation: some objects used in child contracts are imported (but not used) in parent contracts themselves.
- Excessive indirection: tracing the origin and use of code can become difficult, and understanding the context of entire blocks of code can an unnecessary challenge.
- Difficult to maintain: refactoring a deep inheritance hierarchy can make maintaining larger codebases a study in masochism.  Refactoring can become impossible in cases without re-architecting when the coupling between modules becomes tight.

Examples include: 

- ERC20Permit in BaseTOFT is inherited from BaseTOFTStorage.sol, however BaseTOFTStorage.sol itself does not use ERC20Permit.
- The hierarchy of the TOFT/mTOFT and the calls made to LayerZero’s OFT code make tracing the origins of certain calls (ex. `send()`) pathological. To illustrate, the related hierarchy can be modeled as:

```solidity
- TapiocaOFT.sol, mTapiocaOFT.sol <- Coordinated by TapiocaWrapper.sol,
    - [ERC20Permit.sol]
        - [ERC20]
        - [EIP712]
    - BaseTOFT.sol
        - BaseTOFTStorage.sol
            - OFTV2
                - BaseOFTV2
                    - OFTCoreV2
                        - NonblockingLZApp.sol
                            - LzApp <- actual place where LZ `send()` is called
                                - [Ownable.sol]
```

### Recommendation

Reduce the amount of inheritance in smart contracts.  Avoid 3rd-party libraries that use excessive inheritance.

## Project Structure Breaks Common Workflows

Auditing a project is majority a research exercise.  Being able to search and understand the intent and origin of various objects in a codebase is paramount, and when difficulties arise with those aims there is a risk of losing sight of the correct mental model intended by the projects developers.  Developing the project itself has very similar aims, making the auditor and the newly-onboarded developer “cousins” to the experience.

The biggest challenge in onboarding to a new codebase can be increased when the associated tools commonly used to navigate, search, and store the code have difficulties with the structure of the files and the repository they live within.

For the Tapioca codebase, two examples are apparent:

- Currently the repo structure makes following execution paths and the origin of certain objects difficult. Specifically, searching in files returns multiple duplicate results due to submodules.  Below is an example from the very popular IDE, VSCode, and the search results from crawling the top level of the contest repo’s codebase:

![Untitled](https://cdn.discordapp.com/attachments/1137104015096811551/1137104068096049222/Untitled.png)

- Another popular use of VSCode is for tracking changes and updating project repos, essentially acting as a frontend to GIT.  In its UI, the use of submodules causes the UI to show multiple duplicates of separate repos whenever they are embedded in the project codebase.

![Untitled](https://cdn.discordapp.com/attachments/1137104015096811551/1137104068368662629/Untitled_1.png)

While it’s clear that during development, the Tapioca team uses a different workflow, the audit process becomes difficult.  

### Risks

1. Auditing the codebase becomes more challenging, potentially causing vulnerabilities to go unnoticed when the mistakes results for false-positives.

### Recommendation

This team suspects the polyrepo structure to have originated from the need to use previous versions of internal modules.  While useful on a multi-team project, it’s recommended that the audited repository be presented as a single git repository, without submodules. Another suspicion is the desire to use aliases in import strings.  While it is convenient, aliases themselves can obfuscate the origin of objects in a codebase. 

- Structure the project repository as a monorepo.
- Use relative paths for imports to allow for internal libraries to be accessible from a single location, rather than as submodules

## Issues with Comments

As a final compilation, the team noticed the following issues with comments:

### Uninformative Comments

Ex: [https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L29](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L29)

```solidity
/// @notice returns the leverage module
BaseTOFTLeverageModule public leverageModule;
```

**Recommendation**

Make sure comments provide context, and don’t “state the obvious”.

### Stale / Unclear Comments

Ex. in mTapiocaOFT.sol

Incorrect: only can wrap on host chain

```json
/// @notice mtOFT wrapper contract
/// @dev transforms a normal ERC20 or the native gas token into an OFTV2 type contract
///      - wrapping & unwrapping of the ERC20/the gas token can happen on multiple chains defined by the `connectedChains` mappin
```

Ex. in TapiocaOFT.sol

Unclear if original intention was to reference OP or make an example

```solidity
/// @dev Since it can be executed only on the main chain, if an address exists on the OP chain it will not allowed to wrap.
```

Ex. in Balancer.sol

Incorrect: copied, wrong description

```json
/// @param _dstChainId the destination LayerZero id
/// @param _slippage the destination LayerZero id
```

**Recommendation**

Ensure that comments are up-to-date.

### Time spent:
380 hours