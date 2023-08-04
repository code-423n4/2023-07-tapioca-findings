# [A-01] Approach taken in evaluating the codebase

This codebase is quite extensive and intricate, making it a challenging task, especially for someone exploring such complex projects for the first time. The Tapioca protocol encompasses multiple smart contracts spread across five different repositories, each serving a crucial role in its ecosystem. As a result, comprehending the interconnections and interactions among these contracts demands a methodical approach and a keen eye for detail.

For a first-time large codebase evaluation, it's natural to encounter difficulties while navigating through the various contracts, understanding their functionalities, and identifying potential vulnerabilities or optimizations. However, despite the initial challenges, this process offers an excellent opportunity for learning and gaining hands-on experience in analyzing large-scale DeFi projects.

## Approach

I approached this task with a methodical and systematic strategy to ensure a comprehensive assessment of the entire codebase.

1. **Core Protocol Contracts**: I began by immersing myself in the main contracts housed within the tapioca-bar repository, including USDO, BigBang, and Singularity. This step allowed me to grasp the fundamental functionalities and interactions of the Tapioca protocol.

2. **Documentation Review**: To gain additional insights into the design and user flow of the protocol, I meticulously reviewed the provided documentation available at [https://docs.tapioca.xyz/tapioca/](https://docs.tapioca.xyz/tapioca/).

3. **Ecosystem Support Contracts**: Moving beyond the core contracts, I analyzed supporting contracts found in repositories such as tap-token, tapiocaz, tapioca-periph, tapioca-yieldbox-strategies, and YieldBox. This helped me understand how tokenomics and ecosystem features are intricately woven into the protocol.

4. **Understanding the Ecosystem**:

   - **Tap-Token Repository**: I carefully reviewed the contracts and functionalities present in the tap-token repository. This repository contains contracts related to tokenomics and is linked to tapioca-bar in an asymmetric way. By analyzing these contracts, I gained insights into the token-related aspects of the protocol, such as token issuance, redemption, and governance mechanisms.

   - **Tapiocaz Repository**: In the tapiocaz repository, I focused on contracts containing a wrapper named TOFT. This wrapper is used to wrap gas tokens and facilitate their usage through the LayerZero network. Understanding this functionality was crucial to comprehend the gas optimization mechanisms employed by the protocol.

   - **Tapioca-Periph Repository**: This repository houses periphery contracts, with the main contract being MagnetarV2. This contract acts as a helper, reducing the number of user actions/transactions required. I carefully analyzed this contract to understand its role in streamlining user interactions with the protocol.

   - **YieldBox Repository**: YieldBox acts as a "BentoBox v2" and functions as a vault that allows yield strategies to be applied to the asset. I reviewed the contracts related to yield strategies (tapioca-yieldbox-strategies) that will be used by a YieldBox asset. This allowed me to understand the yield generation mechanisms and how users can participate in yield farming activities.

   - **Tapioca-Userflow**: I paid attention to the user flow diagrams and process descriptions available in the repository. This helped me understand the sequence of interactions and user experience when engaging with the Tapioca protocol.

5. **Utilizing Static Analysis Tools**: As part of my comprehensive evaluation process, I incorporated the use of various static analysis tools to assess the code's quality and detect potential vulnerabilities within the Tapioca protocol.

6. **Test Coverage Evaluation**: I aimed to evaluate the test coverage of the protocol to ensure a robust test suite was in place to validate the correctness of the codebase. However, due to the complexity of the Tapioca protocol and the size of the codebase, achieving comprehensive test coverage became challenging. Some parts of the code were not adequately covered, leaving potential vulnerabilities undetected.

# [A-02] Architecture recommendations

The following architecture improvements and feedback could be considered:

### [2.1] Enhance Tokenomics Design

- Continuously monitor and optimize the TAP tokenomics to ensure sustainable distribution, economic growth, and value retention. Consider periodic token burns or buybacks to manage token supply and demand.

1. **Strategic Token Burns and Buybacks**
   A well-defined program for periodic token burns and buybacks shall be instituted. Allocating a portion of protocol revenue to repurchase TAP from the open market and subsequently burning the acquired tokens will effectively reduce circulating supply, bolster scarcity, and potentially foster price appreciation.

### [2.2] Improve twTAP Functionality

- Enhance the Time-Weighted Escrowed TAP (twTAP) mechanism to provide users with more control over their escrow duration. Consider implementing additional options for locking periods to cater to diverse user preferences.

  1.  **More Flexible Locking Periods**
      Consider implementing additional options for locking periods to cater to diverse user preferences. Currently, users can select a lock duration in number of epochs (weeks), with a minimum lock time of one epoch. Providing more choices, such as shorter or longer lock durations, will give users greater control over their escrowed TAP.

  2.  **Dynamic AML Adjustments**
      To further improve the efficiency of the twTAP mechanism, dynamically modulate the multiplier ratio or output of twTAP according to the current Average Magnitude Lock (AML). This approach allows the free market to control the measuring stick (AML), which dictates the multiplier ratio. It can lead to higher allocative efficiency and better user experience compared to static fixed time lock tiers.

  3.  **Periodic twTAP Yield Streams**
      Implement periodic tETH (Tapioca Omnichain ETH) rewards for twTAP lockers. These rewards can come from various sources, including protocol revenue and Arrakis Vault(s) trading fees. Distributing rewards every weekly epoch will incentivize users to participate in twTAP and the Tapioca DAO governance.

  4.  **Improve User Interface for twTAP**
      Enhance the user interface for locking TAP and managing twTAP positions. A user-friendly and secure interface will encourage more users to engage with twTAP and take advantage of its benefits, such as protocol revenue sharing.

  5.  **Secondary Markets for twTAP**
      Explore the possibility of creating secondary markets, such as "twPro," to allow users to swap twTAP lock positions and oTAP NFTs. These secondary markets can provide users with additional functionalities and opportunities for liquidity provision.

### [2.3] Incentive Program Optimization

- Review and optimize the oTAP (call option) incentive program to better incentivize economic growth and participation within the Tapioca ecosystem. Analyze and adjust reward structures based on user engagement and feedback.

  1.  **Decentralized Governance Involvement**
      To ensure the sustainable growth of the ecosystem, involve the Tapioca community in the governance process related to the oTAP DSO program. Allow community members to propose ideas and provide feedback on the program's structure and parameters. Use governance voting mechanisms to determine the discount levels and strike prices of oTAP call options.

  2.  **Integration with twPro**
      Collaborate with twPro, a potential Tapioca ecosystem protocol, to offer extended functionalities to oTAP and twTAP positions. Explore features such as peer-to-peer trading, fractionalization, automated settlement, and clearinghouse functionality to enhance the utility of oTAP tokens.

  3.  **Transparent Distribution Process**
      Ensure a transparent and fair distribution process for oTAP shares. Distribute oTAP shares every Thursday at randomized times to prevent market manipulation. Always distribute oTAP shares to new lock positions in the following epoch to maintain consistency.

### [2.4] Architecture of Big Bang - The OmniDollar Creation Engine

Big Bang is the first natively interoperable decentralized stablecoin and is a heavily modified version of BoringCrypto's Kashi Lending Engine. It allows users to mint USDO, the stablecoin, by supplying accepted collateral assets. Big Bang also supports yield-bearing liquid staking derivatives, such as Lido's stETH, allowing users to earn yields on their supplied collateral and effectively reduce the interest on their borrowed positions to the point where the loan can self-repay.

1.  **Layer 2 Integration**
    Integrate with Layer 2 scaling solutions, such as Optimistic Rollups or zk-rollups, to reduce transaction fees and increase scalability. By moving some of the operations off-chain, Big Bang can handle a larger number of transactions with lower costs and faster confirmation times.

2.  **Decentralized Oracles with Data Aggregation**
    Implement a robust decentralized oracle solution with data aggregation to ensure accurate and reliable price feeds for all collateral assets. Employing multiple reputable data sources will enhance resilience against price manipulation and oracle failures. Data aggregation ensures the most accurate market prices are used for calculating the Collateral Debt Ratio (CDR), safeguarding the stability of USDO.

3.  **Fungibility of Collateral**
    Introduce a mechanism to ensure fungibility of collateral assets. This enables users to use different tokens of the same type as collateral without causing complications or price discrepancies.

### [2.5] Robustness of Bentobox V2

- Conduct rigorous security audits and continuous monitoring of Bentobox V2 to ensure the safety of user funds. Implement multi-signature controls and consider adding support for more asset types.

### [2.6] Governance Enhancements

- Implement a user-friendly and secure governance interface to encourage active participation from the community. Consider introducing off-chain voting options to reduce gas costs and improve user experience.

1. **Proportional Voting with twTAP**
   Implement proportional voting based on users' time-weighted escrowed TAP (twTAP) balances. The longer users lock their TAP, the higher their voting power, rewarding long-term commitment to the DAO. This mechanism incentivizes community members to actively engage in governance and make thoughtful decisions.

2. **Snapshot Voting for Proposals**
   Utilize Snapshot Voting for preliminary discussion and signaling of proposals before they are submitted to the Tapioca Commonwealth forum. This allows for community feedback and gauges sentiment on potential proposals, enhancing decision-making during the formal governance process.

3. **Governance Signers and Roles**
   Clearly define the roles and responsibilities of the multisig signers who govern behind the 72-hour time-lock. Ensure the signers are elected through a transparent and community-driven process to represent the interests of all stakeholders. Roles could include managing the DAO treasury, deploying smart contracts, and executing passed TIPs.

4. **Community-Driven Development Grants**
   Establish a Tapioca Development Fund for granting opportunities to developers and community members. The grants program aims to foster the growth of the Tapioca ecosystem by incentivizing community-driven development. Grant recipients get a say in the protocol's future, promoting a collaborative and inclusive approach.

5. **Special Governance Procedures**
   Clarify the roles and procedures for the Conservator to handle emergency situations, such as implementing Peg Protection actions. These actions can pause or modify markets in response to potential risks to the stability of USDO.

### [2.7] Architecture Improvement through Tapioca Improvement Proposals (TIPs)

1. **Incentivize Participation**
   Introduce incentives for community members to participate in the governance process and provide feedback on TIPs. This could include rewarding active contributors with governance tokens or other forms of recognition for their valuable input.

2. **Transparent Voting Process**
   Enhance transparency in the voting process by utilizing blockchain-based voting systems, such as Snapshot, to conduct TIP voting. Publish the finalized proposal on Snapshot and link it to the Tapioca Commonwealth Forum to facilitate the voting process.

3. **Dynamic Voting Thresholds**
   Implement dynamic voting thresholds based on the significance of the proposal. For critical structural changes, set higher quorum and support thresholds to ensure substantial community consensus. For less impactful proposals, lower thresholds can be employed to streamline decision-making.

4. **Clear Voting Power Calculation**
   Clearly define the calculation of voting power for TAP holders. Ensure that twTAP balances are accurately considered at the blocktime the proposal was submitted. Prevent any twTAP acquired after proposal submission from being used for voting on the same proposal.

5. **Decay Mechanism for twTAP Voting Power**
   Incorporate a decay mechanism for twTAP voting power to discourage malicious last-minute swing votes. This will help maintain a more stable and representative voting process over time.

### [2.8] Fees & Treasury

1. **Treasury Allocation Strategy**

Develop a clear and comprehensive treasury allocation strategy that outlines the intended uses of the DAO Treasury. This strategy should include the allocation of funds to Protocol Owned Liquidity (POL), Reverse LBP Fund, Reserve Fund, and other potential future use cases.

2. **Decentralized Treasury Governance**
   Implement decentralized treasury governance to ensure that all uses of treasury funds are subject to community approval. This can be achieved through Snapshot voting, where community members holding twTAP can cast votes on proposed treasury allocations.

3. **Protocol Owned Liquidity (POL) Management**
   Define a clear plan for the use of POL, with the primary focus on building up liquidity in Tapioca's DAO-owned Arrakis Vaults. This will deepen liquidity for TAP and USDO on supported networks and ensure seamless trading even during adverse market conditions.

4. **Reverse Liquidity Bootstrapping Pool (rLBP) Fund**
   Allocate a portion of the treasury to fund Reverse LBP(s) that will be used to buy back TAP from the market. The acquired TAP will be used to strengthen the treasury or reward the Pearl Club DAO Treasury.

5. **Reserve Fund**
   Establish a Reserve Fund to be used in emergency situations, such as addressing bad debt or stabilizing USDO's peg to the US Dollar. The reserve fund should only be deployed when necessary and in accordance with the DAO's collective decision.

6. **Protocol Fees and Distribution**
   Clearly define the protocol fees and their distribution mechanism. Ensure that all fees are distributed to twTAP lockers in the form of tETH every epoch. Consider implementing a dynamic fee structure that can be adjusted by governance to respond to changing market conditions and demand.

7. **Arrakis Vaults Management**
   Utilize Arrakis Vaults for efficient management of DAO-owned LP and yield generation. Designate a portion of the manager fees from Arrakis Vaults to be distributed to twTAP lockers, incentivizing participation in governance and the protocol.

8. **TAP Holdings and Inflation Schedule**
   Clearly communicate the intended use of TAP holdings and ensure transparency in the inflation schedule. Emphasize that Tapioca has no liquidity mining programs and the inflation schedule is centered around oTAP redemptions each weekly epoch.

# [A-03] Codebase quality analysis

Upon reviewing the codebase, I must commend the development team for their exceptional efforts in maintaining a well-organized and modular codebase. While my analysis may not have delved deeply into every aspect of the codebase due to its size, I can confidently state that the codebase is well-structured.

The strategic use of inheritance to share common functionality across contracts showcases a keen understanding of code reusability and maintainability. This thoughtful approach ensures that changes and updates can be made efficiently without compromising the integrity of the entire system.

### Tapioca Bar Contracts:

- SGLCollateral.sol: Implements the Singularity collateral module.
- SGLBorrow.sol: Implements the Singularity borrowing module.
- BaseUSDOStorage.sol: Provides the storage layout for the Base USDO contract.
- USDO.sol: Implements the USDO stablecoin.
- SGLLendingCommon.sol: Contains common functions for Singularity contracts.
- SGLStorage.sol: Defines the storage layout for Singularity contracts.
- SGLLeverage.sol: Implements the Singularity module for leverage.
- MarketERC20.sol: Serves as the base contract for Market.sol.

### Tapiocaz Contracts:

- TapiocaOFT.sol: Represents an OFTv2 compliant wrapped token with custom functions.
- BaseTOFTStorage.sol: Provides the storage layout for the Base TOFT contract.
- mTapiocaOFT.sol: A special TOFT implementation that can balance its supply.
- TapiocaWrapper.sol: The TOFT create2 deployer.
- BaseTOFTStrategyModule.sol: Base TOFT YieldBox module.
- Balancer.sol: Used to balance out the mTapiocaOFT supply.
- BaseTOFTMarketModule.sol: Base TOFT Singularity market module.
- BaseTOFTOptionsModule.sol: Base TOFT TapiocaOptionBroker market module.
- BaseTOFTLeverageModule.sol: Base TOFT leverage module.
- BaseTOFT.sol: Represents the base TOFT contract.

### Tap Token Contracts:

- LTap.sol: An ERC20 aoTAP 1:1 redeemer.
- oTAP.sol: An ERC721 Option meta contract.
- aoTAP.sol: A forked version of oTAP.
- Vesting.sol: A vesting contract.
- TapOFT.sol: The Tapioca protocol token.
- TapiocaOptionLiquidityProvision.sol: A Singularity ERC20 receipt token vault.
- AirdropBroker.sol: A smaller version of TapiocaOptionBroker to mint & exercise LTAP.
- twTAP.sol: An ONFT721 governance token.
- TapiocaOptionBroker.sol: Used to mint & exercise oTAP.

### Tapioca Peripherals Contracts:

- GLPOracle.sol: Represents a GLP Oracle.
- TapiocaDeployer.sol: The Tapioca contract deployer.
- SGOracle.sol: A Stargate finance oracle.
- Seer.sol: An oracle contract that uses the best of ChainLink/UniV3 price feed.
- Multicall3.sol: A multicall contract.
- ARBTriCryptoOracle.sol: Represents a TriCrypto oracle.
- CurveSwapper.sol: A Curve swapper contract.
- UniswapV2Swapper.sol: A UniV2 swapper contract.
- UniswapV3Swapper.sol: A UniV3 swapper contract.
- MagnetarV2Storage.sol: Provides the storage layout for the Magnetar contract.
- MagnetarMarketModule.sol: A Magnetar Singularity module.
- MagnetarV2.sol: A helper contract that interacts with Singularity, BigBang, TapiocaOptionBroker.
- BaseSwapper.sol: Represents the base swapper contract for other swapper contracts.

### Tapioca YieldBox Strategies Contracts:

- YearnStrategy.sol: A Yearn strategy.
- CompoundStrategy.sol: A Compound strategy.
- LidoEthStrategy.sol: A TriCrypto LP strategy.
- TricryptoNativeStrategy.sol: A TriCrypto native strategy.
- TricryptoLPStrategy.sol: A TriCrypto LP strategy.
- StargateStrategy.sol: A Stargate strategy.
- AaveStrategy.sol: An Aave strategy.
- BalancerStrategy.sol: A Balancer strategy.
- GlpStrategy.sol: A GLP strategy.
- ConvexTricryptoStrategy.sol: A TriCrypto strategy.

### YieldBox Contracts:

- NativeTokenFactory.sol: Creates ERC1155 tokens.
- YieldBoxURIBuilder.sol: Inherited by YieldBox.
- YieldBox.sol: The main Yieldbox contract.

### Codebase Quality Analysis:

Based on the codebase quality analysis, while the codebase exhibits good organization and extensive documentation, there are some identified issues that should be addressed to mitigate potential risks and improve the overall quality of the project.

Security Issues: There are instances of unsafe use of transfer() and transferFrom() functions, which can lead to potential loss of funds if not properly checked. It is crucial to implement proper checks and use safe versions of these functions, such as SafeERC20, to ensure the safety of user funds.

`Code Duplication`: Some code duplication has been identified in different contracts. Code duplication can make the codebase harder to maintain and increase the risk of inconsistencies. Refactoring and consolidating duplicated code can improve maintainability and reduce the chances of introducing bugs.

`Address(0x0) Assignments`: State variables assigned to the zero address (address(0x0)) should be handled with proper checks to prevent potential issues. Assigning state variables to the zero address can lead to unexpected behavior or even contract vulnerabilities, such as allowing anyone to take control of the contract. To mitigate this, it is essential to add condition checks before making assignments to ensure that valid addresses are used

`Fee and Rate Capping`: Fees and rates charged by smart contracts should be capped to protect users from excessive charges. Unbounded fees could potentially lead to significant financial losses for users or create barriers to using the protocol. By implementing upper limits on fees and rates, the contract can ensure that users are not subjected to unfair or prohibitive costs

`Security Token Standards`: Smart contracts dealing with tokens should strictly adhere to standard ERC-20 or ERC-721 specifications. Failing to comply with the standard may lead to compatibility issues with existing wallets, exchanges, or DeFi platforms. Additionally, non-compliant tokens may not be fully interoperable with other contracts, limiting their potential use cases. It is crucial to ensure that token contracts correctly implement the required functions and events as per the respective standard.

`Lack of Slippage Handling`: Slippage refers to the difference between the expected price of a trade and the actual executed price due to market fluctuations. Calculations involving slippage should be carefully validated to ensure accurate results. Proper handling of slippage is particularly critical in decentralized exchanges and trading platforms to prevent front-running and ensure fair trade execution. Implementing algorithms to account for slippage and considering price impact will enhance the reliability and fairness of trades.

# [A-04] Centralization risks

Centralization risks in a codebase refer centralized control or authority over the system, potentially undermining the principles of decentralization and blockchain's core value propositions.

Here are some centralization risks in the codebase:

- **Owner-Based Controls**: The codebase contains multiple instances where certain functions or critical decisions are controlled by the contract owner. This concentration of power in the hands of a single entity may lead to centralization concerns. For example, the "onlyOwner" modifier is used in various contracts to restrict access to certain functions solely to the contract owner. While this can be necessary for administrative purposes, it may also create a single point of failure if the owner's private key is compromised or misused.

- **Fixed Addresses and Oracles**: The codebase includes contracts that rely on specific addresses for crucial functionalities, such as oracles or fee recipients. If these addresses are controlled by a single entity or a centralized party, it can pose a centralization risk to the entire system. Such designs should consider decentralized governance models to mitigate centralization risks.

- **Lack of Upgradeability Mechanism**: Some contracts in the codebase may not have upgradeability mechanisms, making it difficult to improve or modify the contract without deploying a new version. If a centralized upgrade process is followed, it may raise concerns about who has the authority to implement upgrades and whether it aligns with the principles of decentralization.

- **Governance Token Distribution**: The presence of governance tokens (e.g., twTAP) indicates that certain decisions may be subject to token holder voting. The distribution of these tokens can have implications on the decentralization of decision-making power. If a significant proportion of tokens are concentrated in the hands of a few entities, it could lead to centralization of governance control.

- **Limited Validators or Signers**: In systems that rely on multiple validators or signers to achieve consensus, having a limited number of trusted validators can introduce centralization risks. If a majority of these validators are controlled by a single entity or group, it could lead to control over the system's operation.

- **Single Point of Failure**: If any critical component or contract in the codebase acts as a single point of failure, it may create centralization risks. For example, if a contract is responsible for managing user funds, its failure or vulnerability could lead to significant financial losses.

- The **governance** model relies on a 4-of-7 multisig approach, where seven elected members govern the DAO with a 72-hour time-lock. While this approach provides some decentralization, the concentration of power among a small group of signers may still pose centralization risks. Ensuring broad community representation and participation is essential to avoid undue influence by a few individuals.

To mitigate these centralization risks, it is essential to follow best practices for decentralized application development,
including:

- Implementing decentralized governance mechanisms for key decisions.
- Ensuring proper token distribution to avoid concentration of power.
- Utilizing upgradeability mechanisms that involve community consensus.
- Reducing reliance on single trusted entities wherever possible.

# [A-05] Mechanism review

The codebase incorporates a diverse range of mechanisms and functionalities through smart contracts. Here is an in-depth overview of some key mechanisms present in the codebase:

- **Singularity Collateral Module**:
  The Singularity Collateral Module, embodied by the contract SGLCollateral.sol, governs the management of collateral within the Singularity protocol. This module allows users to deposit and withdraw collateral, enabling their participation in the borrowing and lending mechanisms of Singularity.

- **Singularity Borrowing Module**:
  The Singularity Borrowing Module, represented by the contract SGLBorrow.sol, facilitates the borrowing of funds against deposited collateral. By adhering to predefined borrowing limits and debt ratios, users can securely borrow assets based on the assessed value of their collateral.

- **Tapioca Wrapped Tokens (TOFTs)**:
  The codebase introduces an array of contracts related to Tapioca Wrapped Tokens (TOFTs). These TOFTs act as wrapped versions of underlying assets and play a vital role in Tapioca's liquidity provision and trading mechanisms.

- **Tapioca Option Broker**:
  The TapiocaOptionBroker.sol contract serves as a fundamental mechanism for minting and exercising options (oTAP tokens) within the Tapioca protocol. Users can seamlessly create, sell, and exercise options through this well-structured contract.

- **YieldBox Strategies**:
  Multiple yield farming strategies are implemented as separate contracts, such as YearnStrategy.sol, CompoundStrategy.sol, LidoEthStrategy.sol, TricryptoNativeStrategy.sol, and more. Each strategy is thoughtfully designed to optimize yield generation for specific assets and protocols, enhancing the efficiency of the yield farming process.

- **Tapioca Governance (twTAP)**:
  The twTAP.sol contract represents the governance token of the Tapioca protocol. Empowering token holders to actively participate in protocol governance decisions, including voting on proposed changes or upgrades, underscores the commitment to decentralized governance.

- **Swapper Contracts**:
  The codebase incorporates a variety of swapper contracts, including CurveSwapper.sol, UniswapV2Swapper.sol, and UniswapV3Swapper.sol. These contracts skillfully facilitate token swaps across different decentralized exchanges, providing vital liquidity support to the Tapioca protocol.

- **GLP Oracle and Seer Oracle**:
  GLPOracle.sol and Seer.sol contracts function as oracles, delivering crucial external price data to the protocol. With oracles playing an essential role in determining accurate asset prices for various operations, these well-implemented contracts contribute to the overall reliability of the system.

- **YieldBox**:
  The YieldBox.sol contract stands as the central mechanism for managing user funds and yield farming strategies. Users can seamlessly deposit assets into the YieldBox, which automatically allocates them to different strategies to maximize yield generation efficiently.

- **Airdrop Broker**:
  The AirdropBroker.sol contract specializes in minting and exercising LTAP tokens, serving as a condensed version of the TapiocaOptionBroker tailored for airdrop operations.

# [A-06] Systemic risks

- **Security Risks in Omnichain Money Market**: The TapiocaDAO aims to build the first-ever omnichain money market, which involves handling large amounts of liquidity across various networks. Managing decentralized liquidity protocols on multiple chains can be complex and may expose the protocol to security risks, such as smart contract bugs, oracle exploits, or governance attacks. Ensuring the security and robustness of the protocol across different chains is crucial to prevent potential losses for users.

- **Stability Risks of USDO Stablecoin**: USDO, the omnichain stablecoin, claims to address the stablecoin trilemma by being over-collateralized and non-algorithmic. However, any stablecoin pegged to a specific value, such as the U.S. Dollar, faces stability risks. Maintaining the peg in a decentralized and trust-minimized manner can be challenging, especially during periods of extreme market volatility.

- **Scalability Challenges**: The Tapioca protocol's ambition to become a chain agnostic decentralized liquidity layer on multiple networks requires handling significant transaction volumes and scaling challenges. Ensuring high throughput and low transaction fees across multiple chains is a technical challenge that needs to be addressed to provide a seamless user experience.

- **Voting Power Decay**: The voting power decay mechanism over the 72-hour voting period aims to discourage last-minute swing votes. However, the rate of decay and its effectiveness in preventing manipulative voting strategies should be carefully evaluated to ensure a fair and robust governance process.

- **Token Vesting and Voting Power**: The decision to exclude voting power for TAP held within financial contributors' vesting contracts aligns with decentralization principles. However, this may also affect the voting power distribution and participation of certain stakeholders. Balancing long-term commitment incentives with broader governance participation is essential.

- **Short Expiry Period**: The aoTAP call options have a short expiry period of 48 hours. This may create time pressure for recipients to make decisions, and some participants might not be able to exercise their options in time, resulting in unclaimed aoTAP. The protocol should consider the impact of unclaimed tokens and the potential consequences on token supply and governance.

- **Complexity**: The option airdrop mechanism introduces a novel approach, which may require participants to understand the concept of call options and their implications fully. Ensuring clear and comprehensive educational materials and communications about the airdrop process can help prevent confusion and improve participants' decision-making.

- **Sybil Attacks and Airdrop Trilemma**: While the option airdrop addresses the Sybil attack concern, there may still be challenges related to retention and long-term engagement. The protocol should consider mechanisms to incentivize token holders to remain active participants in the ecosystem and actively engage in governance.

- **Liquidity Concentration**: Locking TAP or USDO in twTAP, oTAP, or Singularity markets may lead to liquidity concentration. Users who choose to lock their assets for extended periods could result in reduced circulating supply, affecting market liquidity and potentially leading to price volatility.

- **Market Risk**: Locking USDO in Singularity markets exposes users to market risk, as the value of oTAP rewards is subject to fluctuations in the price of TAP. Users should carefully consider market conditions and potential price changes before locking USDO.

- **Long-Term Sustainability**: The sustainability of the locking mechanisms relies on user participation and engagement. TapiocaDAO should continually assess the effectiveness of the mechanisms and make adjustments if needed to encourage ongoing participation.

- **Looping Yield**: While looping may increase yields, it also introduces complexities and risks. Strategies that involve multiple yield-generating protocols may expose users to additional risks and potential losses.

- **External Strategy Risks**: Strategies can be created permissionlessly by anyone, and strategies not developed and released by TapiocaDAO carry no implied warranty. Users should be cautious when depositing funds into strategies created by third parties and conduct their own due diligence.

- **Differentiating Big Bang and Singularity**: The Tapioca DAO applies different levels of scrutiny for assets onboarded to Big Bang and Singularity. Big Bang collaterals directly impact USDO's price peg, so they must be highly decentralized and liquid. Singularity collaterals also require careful evaluation as they can impact users of that market, but they do not affect the USDO peg directly.

- **On-Chain Liquidity**: The depth of liquidity on decentralized exchanges is crucial for liquidation efficiency and the stability of the protocol. Deep liquidity on DEXs is preferred for collateral assets.

- **Circular Financing**: Assets directly related to Tapioca's native assets (TAP, twTAP, oTAP, USDO, LP, or derivatives) cannot be used as collateral to avoid circular financing and potential death spirals.

- **Snapshot**: A Snapshot is created to elect the initial signers of the multisig. Anyone can be elected, and the multisig makeup will consist of contributors from different categories such as Pearl Labs, Pillars of the TapiocaDAO Community, Financial Supporters, and Unaffiliated DAO Core Contributors. twTAP lockers can propose Tapioca Improvement Proposals (TIPs) through the Snapshot mechanism.


### Time spent:
68 hours