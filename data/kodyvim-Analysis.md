# Tapioca DAO Analysis Report

## Tapioca - An Omnichain Money Market

Tapioca is an innovative omnichain money market that operates across various prominent EVM (Ethereum Virtual Machine) and non-EVM networks. It achieves this through leveraging the LayerZero generalized messaging network.

# Mechanism Review
## TAP Token
The TAP token is the backbone of the Tapioca DAO token economy. It boasts a unique tokenomics design aimed at sustainable distribution and long-term value retention. The primary objective is to achieve widespread distribution of the TAP token, enhancing decentralization within the Tapioca DAO ecosystem. Notably, TAP does not have emissions, and there are no liquidity mining programs within the ecosystem.

## LayerZero OFT20 V2
The TAP token utilizes the LayerZero OFT20 V2 (Omnichain Fungible Token) standard, enabling seamless transfers between both EVM and non-EVM networks without the need for bridges.

## twTAP (Time-Weighted Escrowed TAP)
twTAP is a transferable LayerZero ONFT-721 token created when users escrow TAP for a user-selected duration (in weeks) using a time-weighted average magnitude lock mechanism. Participation in Tapioca DAO governance requires twTAP, and it can also be used to bribe Singularity Option Gauges, aligning incentives with the collective wishes of the DAO.

## Protocol Revenue Distribution
All protocol revenue, 100% of it, is distributed weekly to twTAP lockers in the form of tETH (ETH tokenized on LayerZero).

## Call Option TAP [oTAP]
oTAP is a transferable LayerZero ONFT-721 token used to incentivize economic growth and POL (Protocol Owned Liquidity) capture. Lenders receive oTAP call option incentives by locking their lent liquidity position(s) receipt tokens for a user-selectable number of weeks. oTAP represents an American call option, allowing holders to exercise the option rights at any time before and including the day of expiration.

## OmniDollar [USDO]
USDO is a decentralized, over-collateralized, non-algorithmic, and censorship-resistant stablecoin pegged to the U.S. Dollar. It is programmatically aligned to the dollar and can be instantly transported (minted & burned) across networks without the need for bridges. USDO is collateralized by decentralized and highly liquid network gas tokens such as ETH and AVAX and their LSDs, making it an unstoppable tokenized dollar within the Tapioca ecosystem.

## Codebase quality analysis
| Category | Description |
|--- | --- |
|Access Controls | **Moderate**. Appropriate access controls were in place for performing privileged operations.|
|Arithmetic | **Moderate**. The contracts uses solidity version ^0.8.0 potentially safe from overflows and underflows.|
| Centralization | **Satisfactory**. Most critical functionalities are behind On-chain governance.|
| Code Complexity | **Moderate**. Although most part of the protocol were easy to understand, it maintains a huge codebase. |
|Contract Upgradeability | **Not Applicable**. Contracts are not upgradable.|
| Documentation | **Satisfactory**. High-level documentation and some in-line comments describing the functionality of the protocol were available.|
|Monitoring | **Satisfactory**. Events are emitted on performing critical actions.|
|Testing & Verification | **Moderate**. The repositories included tests for a variety of scenarios. beta testing is highly encouraged to ensure the overall protocol works as intended|

# Architecture Improvement
Access Control: Critical functionalities within the protocols should have sufficient access control to adhere to the principle of least authority. This ensures only authorized parties can withdraw market fees and exit user positions.

Quadratic Voting: Implement quadratic voting for governance proposals to provide more voting power to users who are more actively engaged in the ecosystem. This helps prevent governance capture by large token holders and encourages participation from smaller stakeholders.

Voter Incentives: Introduce incentives for active participation in governance to reward users who actively vote and engage in the decision-making process. This fosters a more decentralized governance ecosystem.

Voter Proxying: Allow users to delegate their voting power to trusted community members or entities through proxy delegates. This ensures that users who might lack the time or expertise can still have their voice heard in governance.

Multi-Chain Governance: Extend governance capabilities to multiple blockchain networks to facilitate broader participation across different ecosystems and mitigate centralization risks.

Community Proposals: Allow community members to submit governance proposals to ensure diverse ideas and perspectives are considered in the decision-making process.

Off-Chain Governance Discussions: Encourage off-chain governance discussions through forums, social media, or other platforms to foster an open and inclusive governance process with thorough deliberations before on-chain voting.

# Centralization Risks
Token Distribution: Unequal distribution of the TAP token among a few individuals or entities may lead to disproportionate influence over governance decisions, risking governance capture.

Governance Mechanisms: Governance mechanisms should encourage broad participation to prevent a minority from exerting undue control and ensure a more decentralized decision-making process.

Development and Upgrades: Transparent and community-driven development and upgrade processes should be in place to prevent centralization of development decisions and foster innovation.

Protocol Fees: Centralization risks exist if only a few participants dominate the distribution of protocol revenue in the form of tETH to twTAP lockers.

Liquidity Providers: Dominance of a few large entities as liquidity providers can lead to centralized control over liquidity pools and impact token prices and trading behavior.

# Systemic Risks
Liquidity Risks: Absence of liquidity mining programs and emissions of TAP may result in insufficient liquidity, leading to higher slippage and hindered efficiency in entering or exiting positions.

Interoperability Risks: LayerZero OFT20 V2 aims for cross-chain compatibility but introduces additional risks related to interoperability between different networks.

Governance Risks: Centralization of twTAP holders may compromise the protocol's decentralization ideals and lead to centralized decision-making in governance.

Market Risks: Market sentiment, speculation, or broader market conditions can significantly influence the value of TAP and associated tokens, causing substantial losses for investors and liquidity providers.

Regulatory Risks: Operating in a regulatory grey area, changes in regulations or legal challenges could impact the ecosystem's operations and compliance.

Oracle Risks: Accuracy and security of price oracles are crucial, as manipulated or compromised oracles can affect valuations and ecosystem functionality.

Economic Risks: The protocol's design and incentives may not always achieve the desired outcomes, potentially leading to economic challenges and instability.

Centralization Risks: Centralized control of certain components or governance structures can hinder the protocol's true decentralization vision.

External Attacks: The protocol could be susceptible to external attacks targeting vulnerabilities in smart contracts or external dependencies, such as flash loan attacks or exploit attempts.

# Approach
Read through the provided documentation.
Skim through the repos provided and take note of relevant points.
Conduct a deep dive into yieldbox, as it is a critical part of the protocol.
Perform a detailed examination of the lending and borrowing implementation.
Investigate the airdrop and option implementation thoroughly.
Proceed with quality assurance (QA) and report writing.


### Time spent:
180 hours