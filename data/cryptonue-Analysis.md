# Overview

Tapioca DAO, Omnichain Money Market and Overcollateralized USD stablecoin. An interesting project due to, it's the first Omnichain Lending Borrowing via LayerZero, and also it introduce Omnichain stablecoin. With its large codebase, and long duration of audit (compared to other audit contest in Code4rena), plus the 2nd largest contest in terms of awards, definitely will attract best auditor in space to help protect this protocol.

Followings are my analysis report, start from my Audit Approach, Codebase Analysis, Architecture Design, Centralization risks and finally Notes, Additional Information and Suggestions.

# Audit Approach

In short:

- Research into project description, purpose, and its architecture design
- If any, read previous audit reports
- Read forks or quite similar project's audit reports
- Smart Contract manual code review and walkthrough

_I skipped using any automated audit tools, as every protocol I assume will perform this action before delivering for audit contest / review._

---

First step for this audit contest, is to understand what is Tapioca DAO, how does it works, why does exist, what this protocol offering compared to similar exisiting protocol. This is to make sure we look at the right place while auditing, after skim reading at its [docs](https://docs.tapioca.xyz/tapioca/).

Next is to read and verify Tapioca DAO's [previous audit](https://docs.tapioca.xyz/tapioca/information/audits-and-partners), and make sure any issues from it was cleared. The previous audit report from Tapioca are from Certora, focusing on Formal Verification of YieldBox.

1. [Certora Formal Verification of YieldBox 2022](https://3014726245-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FfBay3bZwWmLdUX02P7Qc%2Fuploads%2FZ0lrDxhdqWHMX5UdMrtC%2FTapioca_Certora_Audit.pdf?alt=media&token=9fbc7bd3-cccd-42a1-b6d3-22c36e2b795f)
2. [Certora Formal Verification of YieldBox (Appendix) 2022](<https://3014726245-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FfBay3bZwWmLdUX02P7Qc%2Fuploads%2FPIVXvEmuf0FzFZi1poyS%2FTapioca_Certora_Audit%20(Appendix).pdf?alt=media&token=49ad2755-e67c-4fb7-9d6b-3fea9081be12>)

_The second audit is this code4rena contest._

By reading the previous audit from Certora, we need to make sure every issues or findings already covered on this current audit codebase.

Another path for reading some resources are finding some related project to Tapioca DAO. As Tapioca DAO key point:

- Omnichain via LayerZero network: we can look other project using LZ, for a reference where and what are some common issues.
- Introducing Omnichain stablecoin, USDO, Omnidollar: We can look up project for stablecoin which is based on over-collateral minting with protocol owned liquidity, quite similar with OlympusDAO
- Lending and Borrowing platform: A broad general DeFi protocol exist that we can choose to focus on

# Codebase Analysis

The codebase of this audit consist of 5 repositories, a quite big codebase but with a long span time for audit, (plus being the 2nd largest contest on Code4rena). The repositories are:

1. Tapioca-bar: Main Repos, contains USDO stablecoin, Bigbang, and Singularity
2. Tapiocaz: contains a wrapper named TOFT, which is used to wrap gas tokens and transfer allow their usage through the LayerZero network.
3. Tapioca-periph: helper that reduce the number of user taken actions/transactions.
4. YieldBox: Vault (BentoBox v2) for yield strategies to be applied on the asset.
5. YieldBox Strategies: Yield strategies that will be used by a YieldBox asset.

# Architecture Design

Tapioca only needs to deploy small proxy contracts that send and receive messages to Tapioca’s smart contracts on Arbitrum.

- $TAP: Acts as the backbone of the protocol. TAP can be compared to Curve’s CRV in that its true power (and juicy yields) are not unleashed till locked. TAP is not emitted at all – no liquidity mining, rewards, nothing. Therefore, TAP will have very strong value retention with this entirely new innovative token economy that requires everyone to buy in – TAP is never issued for free.
- twTAP: An innovative escrowed token model which utilises Tapioca’s Time Weighted Average Magnitude Lock (twAML) system and is an omnichain NFT which means, it’s transferable! The best way to explain twTAP is if users controlled the value of time with veCRV, while twTAP also doesn’t decay over the course of the lock like veCRV.
- oTAP: An Omnichain NFT that lenders receive each week after locking their lent capital for a period of time. oTAP is simply an American call option on TAP, or the right (but not an obligation) to purchase TAP below its market value.
- USD0: Tapioca’s non-algorithmic and over-collateralized CDP (Collateralized Debt Position) stablecoin which could be compared to Abracadabra’s MIM due to both platforms using Bentobox & Kashi.

What I'm really interest to explore on this audit are:

1. TAP token design and its related ERC20 (oTAP, twTAP, lTAP, aoTAP, TapOFT)
2. Singularity vs Big Bang
3. Oracle Design
4. LayerZero Implementation

## TAP token

The Tapioca DAO (TAP) token supply is capped at 100,000,000 (one hundred million) tokens, and there are no liquidity mining programs. The only way new TAP tokens can enter circulation is through the DSO Incentive Program, where participants exercise call options. The oTAP tokens allow lenders to purchase TAP tokens over-the-counter (OTC) at prices below the market value by exercising their oTAP call option shares during their locked weekly epochs. Unredeemed oTAP call options are visible on-chain, providing transparency and helping users estimate the TAP token's price floor with ease. The system ensures controlled token distribution and encourages active participation in the DAO's incentive program.

![TAP Distribution](https://3014726245-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FfBay3bZwWmLdUX02P7Qc%2Fuploads%2FrFW99I8occOwNO8ympMh%2FTap_Token_Distribution_v3.png?alt=media&token=e1912f33-ecfc-4f78-a093-daa6da39925c)

The TAP token itself minted for several allocation as we can see from above diagram. This audit focus on how TAP token can limit the issuance according to the [inflation schedule](https://docs.tapioca.xyz/tapioca/token-economy/tap-distribution-and-issuance).

Among those distribution and allocation, what I focus on, (and maybe other auditors) are:

1. [Airdrop (Option)](https://docs.tapioca.xyz/tapioca/launch/option-airdrop)
   This airdrop allocation will be held in 4 phase, and it's explicitly described in `AirdropBroker.sol` contract. The airdrop receiver will get Airdrop Option TAP, an ERC721 token that represents an option in AirdropBroker which is defined in `aoTAP.sol`
2. [DSO Incentives](https://docs.tapioca.xyz/tapioca/token-economy/otap-dso)
   This is where the most TAP token will be minted. In order to mint TAP, one need to have oTAP (Option TAP) which users can get it through locking SGL (Singularity Lending Position). This oTAP will be distributed weekly, and user can redeem (mint) during that weekly mint or else the oTAP will expired. I think this is one of the Core contract auditor need to focus on.

## Singularity and Big Bang

In short, Singularity and Big Bang are both adaptations of BoringCrypto's Kashi Lending Engine. However, they serve different purposes: Big Bang facilitates the minting of USDO by providing an accepted collateral asset, while Singularity enables users to borrow, leverage up, and participate in lending with Elastic Interest Rates.

Singularity introduces the concept of Elastic Interest Rates, which means that interest rates can adjust based on market conditions and user demand. This mechanism allows for more flexible and dynamic interest rates, enhancing the efficiency and responsiveness of the lending protocol.

On the other hand, Big Bang focuses on the issuance of USDO stablecoin by allowing users to lock in a collateral asset and receive USDO tokens in return. This minting process ensures the stability of USDO and its peg to the target value.

While BigBang is consist of only one single `BigBang.sol` contract, Singularity consists of several modules:

- SGLBorrow
- SGLCollateral
- SGLLending
- SGLLeverage
- SGLLiquidation
- and other common modules

## Oracle Design

The Oracle contract is part of Tapioca Periph repository. Tapioca's Oracle is using a combination of Chainlink and Uniswap V3 TWAP oracles with a 10-minute time window. Whenever there is a need for an oracle value, the protocol chooses between the output of Uniswap V3 TWAP and Chainlink Price Feed which is most at the advantage of the protocol. For assets that do not have either a Uniswap V3 TWAP Oracle or Chainlink Price Feed, the price feed available will be utilized.

For the Oracle contract, one contract is deployed per collateral/stablecoin pair, so it's not a one oracle contract for all pairs.

Other than having two oracles, there are also single and multiple pool configuration for Chainlink oracle. Seeing how Tapioca prepare this oracle, and also include asset or pair which doesn't have Uniswap V3 TWAP Oracle or Chainlink Price Feed, such as GMX GLP or Curve TriCrypto, I think the Oracle contracts is complete enough.

## LayerZero

LayerZero, while not the primary focus of most auditors, is an essential and robust omnichain protocol with contracts that are beyond the scope of this audit. Nevertheless, I was personally intrigued by how Tapioca effectively utilizes LayerZero for lending, borrowing, and stablecoin minting purposes. Although no related issues were identified with LayerZero implementation during this audit, the experience has been immensely informative and educational.

Understanding how Tapioca leverages the capabilities of LayerZero has broadened my knowledge of decentralized finance and the complexities of integrating various protocols into a seamless ecosystem. The insights gained from this process contribute to my overall understanding of smart contract auditing and the importance of thorough assessments when dealing with multi-layered DeFi projects like Tapioca.

# Centralization risks

The Tapioca DAO, as 'DAO' stands for, decentralized autonomous organization, which is a open management structure to automate some aspects of voting and transaction processing, already prepared everything. The distributed [governance structure](https://docs.tapioca.xyz/tapioca/the-tapioca-dao/governance) is well designed, it even introduce [Tapioca Improvement Proposals](https://docs.tapioca.xyz/tapioca/the-tapioca-dao/tapioca-improvement-proposals-tips).

Same with other governance DAO structure which is intially based on initial token allocation (TAP) to be locked as TwTAP for governance power, the real issue is initial number of parties that control the protocol issuing the token and the number of holders. A concentrated control of the protocol by a few entities might pose governance risks, potentially leading to centralization issues. Similarly, a limited number of token holders may impact the governance process, raising questions about the inclusivity and decentralization of decision-making within the ecosystem.

I am not really sure about the specific procedure for deploying all the contracts within the Tapioca DAO codebase. It seems that the initial deployment lacks sufficient DAO governance, and there are contracts that require an admin to configure settings. I also could not find any part in the contract where ownership is renounced or transfered to DAO, indicating a transition from core developer control to governance DAO might be planned later. For instance, Singularity and BigBang contracts are managed by the Penrose contract, and the Penrose admin holds significant power to modify configurations in both contracts. However, the actual process of transitioning from this initial admin to distributed DAO governance is not within the scope of this audit.

# Notes, Additional Information and Suggestions

Tapioca DAO is a large codebase, but I only have short time to analyze it due to other contest I participated in. But reading Tapioca docs, it clearly a potential omnichain project for lending and borrowing, plus it also introduce Omnichain stablecoin, USDO which I think will have a good position in the next bull run. There are several notes I want to mention:

## USDO and FlashLoan

In Tapioca stablecoin contract, the USDO, it does exist a flashLoan function which I think should be separated. While it seems the flashloan will not make any issues to the USDO, but in my opinion, combining the USDO contract with flashloan function could introduce potential security risks and complexity to the overall system. It is generally considered a best practice to keep different functionalities and concerns separated in smart contract design. By doing so, each component can be independently audited, maintained, and upgraded without affecting the others.

It is recommended to keep the flashLoan function in a separate contract. By creating a dedicated contract for flash loans, it can be thoroughly tested, audited, and optimized for its specific purpose. Furthermore, it enables the developers to follow the principle of separation of concerns, which promotes a cleaner and more secure design.

## Better to Split Audit due to Large Scope

During my auditing experience, I've observed that some protocols opt for a phased approach, conducting partial or iterative audits where multiple rounds of review are performed by the same audit platform. I believe this iterative audit process offers several advantages compared to auditing a large codebase in a single instance.

Regarding the Tapioca DAO, I came across its first audit in Code4rena, accompanied by a substantial reward. This caught my attention, prompting me to question the reason behind such an approach. Furthermore, it's worth noting that the initial audit focused on Certora Formal Verification of YieldBox, rather than conducting a comprehensive audit of the entire architecture.

In my opinion, phased audits can be beneficial as they allow for more focused assessments, addressing specific components or functionalities in each round. This approach provides developers with early feedback and the opportunity to address potential issues before proceeding with subsequent audits. It also enhances the overall audit quality by ensuring thorough evaluations and facilitating incremental improvements over time. However, I am still curious to see how the Tapioca DAO's auditing journey unfolds and how they plan to address the broader aspects of their architecture in subsequent audits.


### Time spent:
40 hours