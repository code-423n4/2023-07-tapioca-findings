# Analysis - Tapioca DAO Protocol
# Summary

| List |Head |Details|
|:--|:----------------|:------|
|1 | Overview of Tapioca DAO protocol| overview of the key components and features of Tapioca DAO  |
|2 |Audit approach | Process and steps i followed  |
|3 |Learnings | Learnings from this protocol|
|4 |Possible Systemic Risks | The possible systemic risks based on my analysis |
|5 |Code Commentary | Suggestions for existing code base |
|6 | Centralization risks | Concerns associated with centralized systems |
|7 |Gas Optimizations | Details about my gas optimizations findings and gas savings  |
|8 |Risks as per Analysis | Possible risks |
|9 |Non-functional aspects | General suggestions |
|10 |Time spent on analysis  | The Over all time spend for this reports |

## Overview
``TapiocaDAO`` is a decentralized autonomous organization (DAO) Cayman Islands foundation company, building the first-ever ``omnichain money`` market across preeminent ``EVM & non-EVM`` networks via leveraging the LayerZero generalized messaging network.

The Tapioca protocol is built with a lot of different smart contracts.It's an ``Omnichain protocol`` working the ``LayerZero messaging layer``. At its core, Tapioca ``ERC20/ERC721`` contracts uses the LayerZero ``OFTv2`` and ``ONFT721`` contracts.

##### Omnichain 

``Omnichain`` composability enables the ``Tapioca protocol`` to become a first of its kind ``chain agnostic decentralized liquidity layer`` which empowers DeFi users on dozens of networks to harness their liquidity without borders. Through the ``LayerZero messaging network``, Tapioca is able to offer seamless interoperability across many disparate networks which streamlines the currently disjointed DeFi user experience, while also achieving previously inaccessible levels of capital efficiency through ``liquidity defragmentation``.

##### USDO

``USDO``, or the ``Omnidollar``, is the first decentralized, ``over-collateralized & non-algorithmic``, ``censorship resistant omnichain stablecoin`` that is programmatically aligned to the U.S. Dollar, built to conquer the stablecoin trilemma of price stability, censorship resistance, and scalability

##### The Tapioca Decentral Bank

Allows users to ``lend``, ``leverage up``, and ``borrow assets`` across many disparate chains through a trust minimized decentralized non-custodial liquidity protocol, thereby reducing the friction of currently siloed liquidity in the broader DeFi ecosystem to offer an important building block in the omnichain future of DeFi

##### Token Economy - Tapioca DAO Token [TAP]

- The ``TAP`` token is the backbone of the Tapioca DAO token economy. TAP was carefully crafted with a unique and ``novel approach`` to ``tokenomics`` design to be sustainably distributed over a ``long-term horizon`` with economic growth and value retention as the ``foremost doctrines``.

- The ``TAP`` token utilizes the LayerZero ``OFT20 V2`` (Omnichain Fungible Token) token super-standard.TAP is able to be transferred to any ``EVM or non-EVM network`` via ``minting & burning`` directly via its token contract- ``no bridges`` needed.

- New TAP tokens are only injected into circulation through redemptions of ``oTAP`` distributed in the DAO Share Options (DSO) incentive program

### PROTOCOL CORE FUNCTIONS

#### Minting
``USDO`` is minted through a collateralized debt mechanism ``(CDP)`` where users take out debt that is denominated in USDO. Users deposit accepted tokenized assets as collateral into ``Big Bang``, to mint a certain amount of USDO from their deposit, depending on market parameters like LTV.
   
#### Borrowing
A potential primary use case of Tapioca, being an ``Omnichain protocol``, would be to  borrow against assets on one chain while utilizing the borrowed assets on another. This allows users to retain their exposure to an asset on another chain which opens up many unique opportunities.
 
   - Use an asset as collateral instead of selling the asset, keeping exposure to it
   - Take a leveraged loan to increase exposure to the collateral asset (and it's yield, if applicable)
   - Keeping your capital productive while it's earning yield

#### Lending
Lenders share the interests paid by borrowers corresponding to the utilization rate of the ``Singularity market``. The higher the utilization of the market, the higher the borrowing interest rate will be, and thus- the higher the real ``yield`` will be for ``lenders``. The interest rate of ``Singularity market``s increases with ``utilization``.

When USDO that is supplied to a ``Singularity market`` is locked, the lender will receive base yields from borrowers interest, and yield ``oTAP`` call options

#### Liquidations
``Liquidation`` is a process that occurs when a user's collateral value does not properly cover the position’s borrowed amount.

Each collateral market in ``Big Bang`` and ``Singularity`` will have different liquidation thresholds, defined by the maximum ``Loan-to-Value Ratio (LTV)``. Once a ``Collateralized Debt Position (CDP)`` reaches it's liquidation threshold, it will become eligible for liquidation. Liquidators then will be able to repay a position's debt in exchange for a portion of the user's collateral.

#### Teleport

``Teleporting`` is very different from a typical bridge in ``DeFi``. Bridging is commonly defined as a ``bidirectional`` liquidity transport protocol that houses liquidity pools on both sides (each respective network) for each asset. 

  - Generally, one class of this type of bridge will employ wrapped intermediary assets to transfer an asset, like Multichain's ``ANY token``, which are not fungible with any other assets ``(for example: anyUSDC vs. native USDC)``. 
  - The second bridging model employs ``intermediary monolithic`` consensus layers as observed with ``Osmosis`` and ``Axelar``. 
  - The third class of this type of bridge which is the most centralized will employ a ``multisig``, like ``Axie Infinity's Ronin`` or ``Harmony Horizon bridges``, both of which were exploited via theft of the ``multisig's private keys``.

#### Locking
Tapioca DAO was initially slated to ``employ Curve's Vote Escrowing (ve)`` mechanism with ``veTAP``. Many users of DeFi applications have become very familiar with various protocols employing ``veTKN's``, such as ``veYFI (Yearn)``, ``veBAL (Balancer)``, and many others. Once core contributors of Tapioca conceptualized ``twAML``, or "Time Weighted Average Magnitude Lock," it became obvious that this mechanism could be reapplied to create a superior token locking model to ``vote escrowing``.

The ``Tapioca protocol`` is built with a lot of different smart contracts, ``scattered across 5 repositories``

 - ``tap-token`` Contracts related to the tokenemics, is linked to ``tapioca-bar`` in an asymmetric way.
 - ``tapiocaz`` Contracts that contains a wrapper named ``TOFT``, which is used to wrap gas tokens and transfer allow their usage through the LayerZero network.
 - ``tapioca-periph`` Periphery contracts. The main contract is ``MagnetarV2``, acts as a helper that reduce the number of user taken actions/transactions.
 - ``YieldBox`` A "BentoBox v2". Acts as a vault, that allow for yield strategies to be applied on the asset.
 - ``Yieldbox-strategies`` Yield strategies that will be used by a YieldBox asset.

## Audit approach

I followed below ``steps`` while ``analyzing`` and auditing the code base.

1. Read the contest ``Readme.md`` and took the required notes.

  - TapiocaDAO Protocol uses 
   - Inheritance
   - use Chainlink or UniV3
   - timelock function
   - Fork of Kashi lending & borrowing
   - Multi-chain

2. Analyzed the over all codebase one iterations ``very fast``.Covered as much as contracts because there are lots contracts scope in this protocol 

3. Study of ``documentation`` to understand each contract purposes, its ``functionality``, how it is connected with other contracts, etc. Took required ``notes`` for future references 

4. Then i read ``old audit reports`` and ``already known findings``. Then go through the ``bot races findings`` 

5. Then setup my ``testing environment`` things. Run the tests to checks all test passed. I used ``hardhat``  to test ``TapiocaDAO protocol``.
  - Commands Used
    - ``npx hardhat compile`` - To compile the contracts 
    - ``npx hardhat test``    - To run all tests

6. Then i ensured all tests passed or not

6. Finally, I started with the auditing the code base in depth way I started understanding line by line code and took the necessary notes to ask some questions to sponsors.

## Stages of audit

- ``The first stage of the audit``

During the initial stage of the audit for TapiocaDAO Protocol, the primary focus was on analyzing gas usage and quality assurance (QA) aspects. This phase of the audit aimed to ensure the efficiency of gas consumption and verify the robustness of the platform.

   - I found more ``GAS OPTIMIZATIONS`` nearly saved ``70000 GAS`` [REPORT](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=129)
   - I found 30 LOW FINDINGS [LOW REPORT](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=130)

- ``The second stage of the audit``

In the second stage of the audit for TapiocaDAO Protocol, the focus shifted towards understanding the protocol usage in more detail. This involved identifying and analyzing the important contracts and functions within the system. By examining these key components, the audit aimed to gain a comprehensive understanding of the protocol's functionality and potential risks. 

- ``The third stage of the audit``

During the third stage of the audit for TapiocaDAO Protocol, the focus was on thoroughly examining and marking any doubtful or vulnerable areas within the protocol. This stage involved conducting comprehensive vulnerability assessments and identifying potential weaknesses in the system. Found ``60-70`` ``vulnerable`` and ``weakness`` code parts all marked with ``@audit tags``.

- ``The fourth stage of the audit``

During the fourth stage of the audit for TapiocaDAO Protocol, a comprehensive analysis and testing of the previously identified doubtful and vulnerable areas were conducted. This stage involved diving deeper into these areas, performing in-depth examinations, and subjecting them to rigorous testing, including fuzzing with various inputs. Finally concluded findings after all research's and tests. Then i reported C4 with proper formats 

Finally i Found 3 Medium Risks and reported 

   - [Missing deadline checks allow pending transactions to be maliciously executed](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1408)
   - [No slippage protection . ``YieldBox`` contract may receive less token than actual amount ](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1451)
   - [``block.timestamp <= deadline``  is vulnerable to a  front running timestamp attack ](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=803)


## Learnings
The TapiocaDAO protocol provides valuable insights into the potential of cross-chain DeFi, stablecoin solutions, tokenomics, risk management, and community-driven governance. As the DeFi space continues to evolve, the learnings from TapiocaDAO can inspire and inform the development of future decentralized financial applications and protocols.

TapiocaDAO protocol, here are some key learnings
  
   - ``Omnichain Composability and Interoperability``: TapiocaDAO's focus on omnichain composability highlights the importance of seamless interoperability between different blockchain networks. Interoperability allows DeFi users to access liquidity and assets across various chains, reducing barriers and enhancing the overall user experience.

   - ``Stablecoin Trilemma Solutions``: TapiocaDAO's introduction of USDO, an over-collateralized and censorship-resistant omnichain stablecoin, demonstrates innovative solutions to the stablecoin trilemma. Balancing price stability, censorship resistance, and scalability can lead to more reliable and widely adopted stablecoins in the DeFi ecosystem.

   - ``Decentralized Liquidity and Lending`` : TapiocaDAO's Tapioca Decentral Bank showcases the importance of decentralized liquidity protocols that allow users to lend, borrow, and leverage assets without the need for centralized intermediaries. Trust-minimized and non-custodial solutions can promote a more secure and accessible DeFi environment.

  - ``Novel Tokenomics Design``: TapiocaDAO's TAP token illustrates the significance of novel tokenomics design in creating sustainable and value-retaining token economies. A carefully crafted tokenomics model can foster long-term growth and incentivize active community participation.

  - ``Risk Management and Liquidation``: The inclusion of risk management mechanisms and liquidation procedures in TapiocaDAO highlights the importance of maintaining stability and security in DeFi protocols. Proper collateralization and risk mitigation are crucial to preventing potential financial crises.

  - ``Efficient Cross-Chain Operations``: TapiocaDAO's teleporting feature underscores the value of efficient cross-chain operations. Simplified and decentralized asset transfers between different chains can lead to broader adoption of DeFi applications and broader access to various assets.

  - ``Continuous Innovation in DeFi``: The TapiocaDAO protocol showcases the continuous innovation and development within the DeFi space. Projects are constantly exploring new ideas and solutions to address existing challenges and expand the capabilities of decentralized finance.

  - ``Focus on Community Involvement``: The TapiocaDAO protocol's governance mechanisms and token locking models emphasize the importance of community involvement and decentralized decision-making. Active participation and engagement from the community can drive the success and evolution of a DeFi protocol.

 -  ``Building Blocks for the Future`` : TapiocaDAO's diverse smart contracts and functionalities represent essential building blocks for the future of DeFi. By addressing key challenges like liquidity fragmentation and cross-chain access, TapiocaDAO contributes to the development of a more connected and efficient DeFi ecosystem.

## Possible Systemic Risks

Possible systemic risks for the TapiocaDAO protocol

1. ``Interoperability Risks``: Potential issues in connecting different blockchain networks could lead to errors and security problems.

2. ``Stablecoin Risks`` : Maintaining stability and collateralization of the USDO stablecoin may be challenging.

3. ``Smart Contract Risks`` : Coding errors in smart contracts could lead to hacks or vulnerabilities.

4. ``Cross-Chain Security Risks``: Transferring assets between chains might have unforeseen security issues.

5. ``Liquidity Risks``: Insufficient liquidity or imbalances in lending and borrowing could impact stability.

6. ``Tokenomics Risks`` : The unique token distribution model might face uncertainties in market conditions.

7. ``Liquidation Risks`` : Inefficient or unfair liquidation processes could lead to losses for users.

8. ``Governance Risks``: Governance decisions could be influenced by token holders acting against the protocol's interest.

9. ``Oracles and External Dependencies`` : Reliance on external data sources could lead to incorrect decision-making.


## Code Commentary

#### Majority code commentary suggestions is covered with these 10 contracts .Some many comments repeated so other contract comments not included in this reports

### ``Market.sol``

- You can precompute the division factor ``(EXCHANGE_RATE_PRECISION / FEE_PRECISION)`` and reuse it to avoid repeated division.
- If you find similar calculations being performed in different places, consider creating helper functions to consolidate these computations. For example, the decimal scaling calculations in ``_computeMaxAndMinLTVInAsset`` and ``_computeMaxBorrowableAmoun``t could be shared in a helper function.
- Use ``local variables`` to cache values from storage, especially within loops. Reading from storage is more expensive than reading from memory
- Instead of using multiple variables with similar meanings ``(e.g., minLiquidatorReward and maxLiquidatorReward)``, consider using an enumeration or a ``struct`` to group related constants together and make the ``code more maintainable``.
- Use function ``modifiers`` for ``common checks`` or actions to reduce ``code duplication``. For instance, you could create a ``modifier`` that checks ``if the caller is the conservator``.
- Ensure that variable names, function names, and event names follow consistent naming conventions. This improves code readability and maintainability

### ``BigBang.sol``

- Add more ``inline comments`` to explain ``complex logic``, ``calculations``, and ``decisions``
- Implement ``reentrancy protection`` in critical functions like ``addCollateral``, ``removeCollateral``, ``borrow``, ``repay``, and others using the ``nonReentrant`` modifier
- Consider emitting ``events`` with ``more information``, such as the exact amount of assets being transferred or the share amounts involved, to provide more transparency to users and external systems
- Provide ``informative error messages`` in your ``require`` statements. This will help ``users`` and ``developers`` understand ``why`` a certain condition failed
- Instead of using a boolean ``_isEthMarket`` to differentiate between ``ETH`` and ``non-ETH markets``, consider using an ``enum``. ``Enums`` can make your code more ``self-explanatory`` and extensible in case you need to add more market types in the ``future``
- Instead of passing multiple parameters to the ``init`` function, you can consider using a ``struct`` to hold the ``configuration data``. This can improve ``code readability`` and make it easier to manage configuration updates
-  Ensure that any ``input data`` (parameters) received from external sources are properly validated and ``sanitized`` to prevent vulnerabilities like integer overflow or underflow

### ``Singularity.sol``

- In places where you perform ``arithmetic operations`` involving ``constants``, consider ``precomputing`` the results and using the precomputed values to avoid unnecessary ``runtime calculations``
- In the ``_executeModule`` function, where ``delegatecall`` is used, consider using direct function calls (this.function()) for the functions that are directly callable from the contract, as they might be more efficient
- In functions like ``refreshPenroseFees``, where you emit events after a state change, consider moving the event emission to the ``end of the function`` after the ``state changes`` have occurred
- The long ASCII art comment at the top of the contract could be represented using a bytes32 variable instead of a string to save gas
- In functions with multiple conditions, consider combining conditions using logical operators ``(&&, ||) ``to reduce the number of if statements
- Consider using function modifiers to reduce duplicated code, especially for common checks like pausing or permission checks
- In the ``init`` function, you can reuse ``_liquidationModule``, ``_borrowModule``, etc., instead of creating new local variables
- In places where you are using revert to handle invalid conditions, ensure that the error message string is not unnecessarily consuming gas

### ``Penrose.sol``

- Your code is well-written, but it would be helpful to add inline comments to explain what each section of code is doing. This would make your code easier to understand for other developers

### ``BaseUSDO.sol``

- You could create a ``modifier`` for ``ownership`` checks to improve ``code readability`` and reduce ``duplication``.
-  If you have ``common parameters`` that are being passed around in ``function calls``, consider using ``structs`` to group these parameters. This can make your function signatures ``cleaner`` and ``more readable``
- Make sure to ``explicitly`` specify the ``visibility of functions`` and state variables. For example, you can add the public visibility specifier to the decimals function
- When referring to ``external contracts``, use their ``interface types`` instead of ``address`` to provide better type ``safety`` and ``avoid potential mistakes``
- ``Delegate calls`` are used in this contract, which might introduce some ``gas overhead``. Ensure that the use of delegate calls is necessary and optimized for your use case
- In several places, there are ``complex expressions``. Consider breaking them down into`` smaller steps`` or using ``helper functions`` to improve readability
- Ensure that ``error handling`` is robust and provides informative ``error messages``

### ``SGLLiquidation.sol``

- Use more descriptive variable names to improve code readability. For example, instead of ``LQ``, ``bidAvail``, and ``bidAmount``, consider using names like ``liquidationQueue``, ``isBidAvailable``, and ``currentBidAmount``.
- There's some code duplication in the ``_orderBookLiquidation`` and ``_closedLiquidation`` functions. Extract ``common functionality`` into separate ``helper functions`` to reduce ``redundancy`` and ``improve maintainability``
- Consider using data types with fixed sizes to save gas. For example, using ``uint128`` for ``totalBorrow.elastic`` and ``totalBorrow.base`` if the values will fit within that range
- Use gas-efficient math operations like div instead of ``/`` for division operations. Consider using shift operations ``(>> and <<)`` for multiplication and division by powers of 2

### ``USDOLeverageModule.sol``

-  Instead of using magic numbers like ``PT_MARKET_MULTIHOP_BUY`` and ``PT_LEVERAGE_MARKET_UP``, consider using ``enums`` to define these payload types for ``better code clarity``
-  Since you're interacting with external contracts, consider using the ``ReentrancyGuard pattern`` to prevent reentrancy attacks
-  There's some duplicated error handling code in the ``_callApproval`` function. Consider extracting this logic into a separate helper function to avoid repetition

### ``USDOOptionsModule.sol``

- In the exerciseOption and exerciseInternal functions, there is a series of token swaps. Review the token swapping logic and consider optimizing it to minimize gas costs. You could explore using flash loans or different swapping strategies to reduce the overall gas expenditur
-  Implement a ``whitelist`` of ``trusted addresse``s that can call specific functions on ``behalf`` of users. This can provide convenience while ``maintaining security``, as users won't need to ``approve multiple contracts`` for various actions
-  Implement a ``circuit breaker`` pattern to pause certain functions temporarily in case of unexpected issues, vulnerabilities, or changes in network conditions
-  Consider using ``proxy contracts`` to enable ``seamless contract upgrades`` while maintaining ``user balances`` and ``data``
-  In functions where you spend gas, such as sending transactions, check if there are any gas token refunds available that could offset the gas costs
- If your contract involves fees, consider implementing a ``dynamic fee calculation`` mechanism based on ``network congestion`` and other ``relevant factors``

### ``SGLLeverage.sol``

- It's a good practice to provide comments that explain the purpose and conditions of the modifiers you use, such as ``notPaused`` and ``solvent``. This will help other developers understand the intentions behind these modifiers without having to go through the full code
- Ensure that the order of ``modifiers`` in your function declarations is ``consistent`` and ``logical``. For example, you might place access control modifiers ``(like external, public, internal, etc.)`` before other custom modifiers for better readability.
- Keep an eye on the latest Solidity version and update your pragma statement accordingly to benefit from any improvements or fixes introduced in newer versions

### ``BaseTOFT.sol``

- Consider defining an ``enum`` for the ``Module`` names to improve readability. This would help you replace the long if-else chain in ``_extractModule`` with a simple switch case
- Add comments explaining each parameter of the constructor for clarity
- When using ``require``, provide informative error messages that explain why the condition was not met. For example, ``"TOFT_host"`` could be ``"Only callable from the host chain"``
- Ensure that functions are correctly labeled with the appropriate state mutability modifiers ``(pure, view, payable, or none)``.
- Use more descriptive names for modifiers like ``onlyHostChain`` to clearly convey their purpose
- If multiple functions use the same interfaces ``(e.g., ICommonData, IYieldBoxBase, etc.)``, consider consolidating the import statements at the top of the contract for cleaner code
- To enhance readability, place ``modifier`` definitions before ``function definitions``
- Consider using ``internal`` instead of ``private`` for functions that may be overridden in child contracts

#### General Suggestions

1. Use same version of solidity versions for all contracts 
2. Add more NATSPEC comments all over contracts 
3. Used Named imports instead of importing over all contracts
4. Follow the solidity standards to declare state varibales,constants ,modifers,mappings . Group the declarations together
5. Immutables should be upper case letters
6. Follow the event indexed rule. Need minimum 3 parameters should be indexed every events. If less than 3 should indexed all parameters 
7. Don't use named return varibales when function returns 
8. Add more sanity checks when dealing ``uint`` and ``uint256``
9. Some functions return values even not necessary
 
## Centralization risks

A single point of failure is not acceptable for this project Centrality risk is high in the project, the role of ``onlyOwner``detailed below has very critical and important powers

Project and funds may be compromised by a malicious or stolen private key ``onlyOwner`` ``msg.sender``

```solidity
File: contracts/Penrose.sol

256:     function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner {

263:     function setBigBangEthMarket(address _market) external onlyOwner {

281:     function setConservator(address _conservator) external onlyOwner {

291:     function setUsdoToken(address _usdoToken) external onlyOwner {

464:     function setSwapper(ISwapper swapper, bool enable) external onlyOwner {

```

https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-bar-audit/contracts/Penrose.sol#L256-L256

```solidity
File: contracts/markets/Market.sol

142:     function setBorrowOpeningFee(uint256 _val) external onlyOwner {

151:     function setBorrowCap(uint256 _cap) external notPaused onlyOwner {

158      function setMarketConfig(
159          uint256 _borrowOpeningFee,
160          IOracle _oracle,
161          bytes calldata _oracleData,
162          address _conservator,
163          uint256 _callerFee,
164          uint256 _protocolFee,
165          uint256 _liquidationBonusAmount,
166          uint256 _minLiquidatorReward,
167          uint256 _maxLiquidatorReward,
168          uint256 _totalBorrowCap,
169          uint256 _collateralizationRate
170:     ) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-bar-audit/contracts/markets/Market.sol#L142-L142

```solidity
File: contracts/markets/bigBang/BigBang.sol

442      function refreshPenroseFees(
443          address
444:     ) external onlyOwner notPaused returns (uint256 feeShares) {

466      function setBigBangConfig(
467          uint256 _minDebtRate,
468          uint256 _maxDebtRate,
469          uint256 _debtRateAgainstEthMarket,
470          uint256 _liquidationMultiplier
471:     ) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L442-L444

```solidity
File: contracts/usd0/BaseUSDO.sol

88:      function setMaxFlashMintable(uint256 _val) external onlyOwner {

96:      function setFlashMintFee(uint256 _val) external onlyOwner {

105:     function setConservator(address _conservator) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L88-L88

```solidity
File: contracts/Vesting.sol

130:     function registerUser(address _user, uint256 _amount) external onlyOwner {

151:     function init(IERC20 _token, uint256 _seededAmount) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tap-token-audit/contracts/Vesting.sol#L130-L130

```solidity
File: contracts/tokens/TapOFT.sol

140      function setGovernanceChainIdentifier(
141          uint256 _identifier
142:     ) external onlyOwner {

152:     function updatePause(bool val) external onlyOwner {

160:     function setMinter(address _minter) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tap-token-audit/contracts/tokens/TapOFT.sol#L140-L142

```solidity
File: contracts/Swapper/UniswapV3Swapper.sol

61:      function setPoolFee(uint24 _newFee) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol#L61-L61

```solidity

File: contracts/aave/AaveStrategy.sol

122:     function setDepositThreshold(uint256 amount) external onlyOwner {

129:     function setMultiSwapper(address _swapper) external onlyOwner {

209:     function emergencyWithdraw() external onlyOwner returns (uint256 result) {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L122-L122

```solidity
File: contracts/convex/ConvexTricryptoStrategy.sol

148:     function emergencyWithdraw() external onlyOwner returns (uint256 result) {

163:     function setDepositThreshold(uint256 amount) external onlyOwner {

170:     function setMultiSwapper(address _swapper) external onlyOwner {

179:     function setTricryptoLPGetter(address _lpGetter) external onlyOwner {

```
https://github.com/code-423n4/2023-07-tapioca/blob/e6eef060495b31173578570215e80f9e95330b9a/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L148-L148


## Gas Optimizations
This analysis report is meant to accompany my Gas Optimizations Report submission. All insights will be given through the perspective of optimizing the codebase.

### When reviewing this code base I prioritized mitigating unnecessary SSTOREs. The first optimizations

#### State varibales can be packed fewer slots

- In the ``Market.sol`` contract, significant gas savings can be achieved by packing related state variables into fewer storage slots. By grouping variables that are written in the same function or constructor and do not exceed the FEE_PRECISION constant value, separate SSTORE operations can be avoided, resulting in a savings of approximately ``32,000 GAS`` and ``16 storage slots``. For instance, combining variables such as ``yieldBox`` and ``callerFee``, ``penrose`` and ``protocolFee``, ``collateral`` and ``minLiquidatorReward``, asset and ``maxLiquidatorReward``, ``conservator`` and ``liquidationBonusAmount``, ``oracle`` and ``collateralizationRate``, as well as ``totalBorrow``, ``borrowOpeningFee``, and ``liquidationMultiplier`` into single storage slots would contribute to these gas savings.

- In ``Penrose.sol``, merging ``usdoToken`` and ``usdoAssetId`` into a single storage slot results in a gas reduction of around ``2,000 GAS``. This optimization leverages the fact that ``usdoAssetId ``is always ``downcast`` to uint96, making it safe to use this data type instead of ``uint256``.

- In ``TapiocaOptionBroker.sol``, combining ``lastEpochUpdate``, ``epoch``, and ``epochTAPValuation`` into a single storage slot can lead to gas savings of about ``4,000 GAS``. This optimization capitalizes on the fact that ``epochTAPValuation`` remains constant within the contract and can be represented using a narrower data type.

- Similarly, in ``twTAP.sol``, packing ``mintedTWTap`` and ``lastProcessedWeek`` into the same storage slot can save around ``2,000 GAS``. Both variables can be accommodated within a ``uint128`` data type, which is more than sufficient for their purposes.

- In ``Vesting.sol``, grouping related state variables like ``token``, ``start``, ``cliff``, and ``duration`` within a single storage slot can result in gas savings of approximately ``4,000 GAS``. Additionally, utilizing narrower data types such as ``uint96`` for start and ``uint128`` for cliff and duration optimizes gas usage without affecting contract functionality.

- Lastly, in ``BaseTOFTStorage.sol``, converting ``hostChainID`` to a ``uint64`` data type, consistent with other protocols, can save about ``4,000 GAS`` and reduce the storage footprint by ``2 slots``.

#### Structs can be packed into fewer storage slots

- In the ``Vesting.sol`` contract, optimizing struct packing can lead to gas savings. Specifically, by packing the ``latestClaimTimestamp`` and revoked variables into the same storage slot within the ``UserData`` struct, approximately ``2,000 GAS`` and ``1 storage slot`` can be saved. Downsizing latestClaimTimestamp to uint248 while accommodating revoked does not impact the protocol's functionality. Many protocols follow this approach to optimize gas consumption.

- In the ``TapiocaOptionLiquidityProvision.sol`` contract, employing struct packing optimizations can result in gas savings. By changing the data type of ``sglAssetID`` and ``totalDeposited`` within the ``SingularityPool`` struct to uint128, around ``2,000 GAS and 1 storage slot`` can be saved. Additionally, since the LockPosition struct uses uint128 for sglAssetID, consistency is maintained.

#### Using calldata instead of memory for read-only arguments in external functions 

Optimizations in several contracts led to significant gas savings by employing ``calldata`` instead of ``memory`` for read-only arguments in ``external functions``. In the ``Penrose.sol`` contract, updating the data parameter in the ``executeMarketFn`` function resulted in approximately ``282 GAS`` saved. Similarly, ``BaseUSDO.sol`` demonstrated about ``282 GAS`` savings by utilizing calldata for the ``airdropAdapterParams`` parameter in the ``initMultiHopBuy`` function. In ``BaseTOFT.sol``, switching to calldata for the approvals parameter within the ``initMultiSell`` function also yielded gas efficiency gains. Furthermore, in ``Balancer.sol``, refining the`` _ercData`` parameter to use calldata in the ``_sendToken`` function saved around ``564 GAS``. Lastly, in ``TapiocaDeployer.sol``, optimizing the bytecode and contractName parameters using calldata in the deploy function achieved a similar ``564 GAS`` reduction. These modifications enhanced gas efficiency and underscore the benefits of calldata for read-only arguments in external function calls, resulting in notable gas savings across the contracts.

#### Using storage instead of memory for structs/arrays saves gas

The optimization of using ``storage`` instead of ``memory`` for structs and arrays resulted in notable gas savings across several instances. By employing ``storage`` and caching specific fields in stack variables, unnecessary ``Gcoldsload`` operations were avoided. In the ``TapiocaOptionBroker.sol`` contract, the use of storage for the ``PaymentTokenOracle`` instance resulted in approximately ``16800 GAS`` saved. Similarly, in ``AirdropBroker.sol``, the same optimization for the ``PaymentTokenOracle`` instance contributed to significant gas efficiency improvements. Additionally, the ``TapiocaOptionLiquidityProvision.sol`` contract achieved gas savings by changing ``memory`` to ``storage`` for the ``LockPosition`` instances. This optimization strategy effectively reduced gas costs by minimizing expensive ``Gcoldsload`` operations and was applied judiciously across the mentioned contracts to enhance overall gas efficiency. 

#### Don't initialize the default values to state or local variables for saving gas  

Optimizing gas usage involves minimizing unnecessary operations, and one effective approach is avoiding initializing default values for state or local variables. This strategy leads to significant gas savings due to reduced storage consumption and elimination of unnecessary ``SSTORE`` operations.

- In the ``Vesting.sol`` contract, the seeded variable was initialized in the ``init()`` function. However, this initialization proved to be ``redundant`` and a ``waste of gas``, so removing the default value assignment led to savings of approximately ``2206 GAS`` per ``SSTORE`` operation.

- Furthermore, by refraining from initializing default values in ``loops`` across several contracts, substantial gas savings of around ``182 GAS`` were achieved, impacting ``14 instances`` across various contracts such as ``Market.sol``, ``Penrose.sol``, and ``SGLLiquidation.sol``. By avoiding these unnecessary default value assignments, the contracts demonstrated improved gas efficiency and more cost-effective execution.

#### Optimizing gas usage Order your checks correctly

``Reordering`` checks within functions is a ``powerful way`` to optimize gas usage, ensuring efficient execution and cost savings. By prioritizing checks involving constants before those involving state variables, function calls, and calculations, functions can potentially revert sooner and save gas by avoiding unnecessary operations.

- In the ``Singularity.sol`` contract, rearranging the require checks at the beginning of the init function led to substantial savings of around ``12000 GAS``. By verifying critical conditions such as the validity of addresses for ``collateral``, ``asset``, and ``oracle`` pairs first, the function can immediately revert if necessary, before ``incurring`` additional costs like ``storage writes``.

- Similarly, in the ``BigBang.sol`` contract, reordering the checks for ``_collateral`` and ``_oracle`` to be at the top of the function allowed for gas savings of approximately ``4000 GAS``. This reordering ensures that checks involving these addresses are conducted prior to storage writes, preventing costly gas expenditure in case of subsequent reverts.

#### Combine Events

- ``BigBang.sol``: Merged ``LogRemoveCollateral`` and ``LogRepay`` events into a single event called ``LogRemoveCollateralAndRepay``, saving ``Glogtopic (375 gas)``.

- ``Singularity.sol``: Combined ``Transfer`` and ``LogWithdrawFees`` events, as well as ``LogRemoveCollateral`` and ``LogRepay`` events, into single events, saving ``Glogtopic (375 gas)`` and ``Glogtopic (375 gas)`` respectively.

- ``SGLCommon.sol``: Combined ``Transfer`` and ``LogRemoveAsset`` events, as well as ``Transfer`` and ``LogAddAsset`` events, into single events,`` saving Glogtopic (375 gas)`` each.

#### Use Calldata Pointer for Loops

``Multicall3.sol``: Used a ``calldata`` pointer instead of a ``memory`` pointer to read Result struct in the loop, saving gas per iteration.

#### Don't Emit State Variables

``AirdropBroker.sol``: Instead of emitting the ``epochTAPValuation`` state variable, emitted the ``_epochTAPValuation`` parameter, saving ``100 gas`` and ``1 SLOD``.

#### Use Local Variable Cache for Multiple Accesses

Cached ``userCollateralShare[user]`` in ``BigBang.sol``, ``connectedOFTs[_srcOft][_dstChainId]``.rebalanceable in ``Balancer.sol``, and ``aoTAPCalls[_aoTAPTokenID][cachedEpoch]`` in ``AirdropBroker.sol`` for multiple accesses within functions, saving gas and ``SLOD`` per access.

#### Use Addition Instead of Plus-Equals

Replaced ``+=`` and ``-=`` operators with addition and subtraction for state variable assignments in multiple contracts, saving gas and reducing instances of state variable access.


## Risks as per Analysis

- Allowing users to set ``minAmountOut`` to ``0`` can lead to significant losses due to ``manipulative activities``.

- Absence of the ``checks-effects-interactions (CEI) pattern`` in transfer functions can leave the contract vulnerable ``reenrancy attacks``

- The platform should incorporate a ``blacklist function`` to prevent potentially compromised NFTs from being converted into ``liquidity``, ``protecting users`` from ``potential theft``

- Failure to validate the return values of the ``.call`` function can lead to ``unintended behavior`` and ``fund losses``

- Lacking proper checks in ``setter functions`` when assigning values to ``uint256`` variables can create vulnerabilities or unexpected behavior

- Inconsistency in using ``comparison operators`` without clear ``documentation`` can lead to confusion and ``erroneous behavior``

- Restrictions in toggling ``pause`` states might lead to issues when rapid ``pausin``g and ``unpausing`` is needed. Evaluate and modify the pause mechanism as needed to align with intended use cases

- Consider allowing users to ``add collateral`` or ``assets`` even during the ``contract's paused`` state to enable effective position management, without introducing significant risks

- The ``computeTVLInfo()`` function should handle cases where ``collateralAmountInAsset`` is zero

- Unnecessary or ``unused return values`` from functions can add confusion and unnecessary`` complexity to the contract``

- ``Variable shadowing`` can lead to unintended state changes and unexpected behavior

- Generate ``NFT token IDs sequentially`` to prevent users from defining ``arbitrary value``s, thus enhancing the security of the ``NFT functionality``

- The contract inherits the Pausable contract but lacks the implementation of a pause mechanism

- Evaluate the impact of allowing ``users`` to ``add collateral`` or ``assets`` even during the contract's paused state

- The project has NPM dependencies using ``vulnerable versions`` of the ``openzeppelin library``

- Avoid transferring tokens to ``address(0)`` in the ``removeCollateral()`` function to prevent tokens from being effectively lost

- Implement an additional check for ``address(0)`` before comparing the ``signer`` to the ``owner`` in the ``permit`` and ``permitAll`` functions for enhanced security

- Include the chain ID in`` _PERMIT_TYPEHASH`` and ``_PERMIT_ALL_TYPEHASH`` to prevent malicious actors from ``forging signatures`` on other networks

- Add input checks to ensure that setter functions are not called with the ``same value`` as the current value of the variable being set

- Implement reentrancy protection in critical functions like ``addCollateral()``, ``removeCollateral()``, ``borrow()``, and ``repay()``.

- Ensure that the ``address(this).balance >= amount`` check is executed at the time of transaction execution to accurately assess the ``contract's balance``

- Ensure that the ``salt`` used in the ``create2`` assembly is unique to ``prevent collisions``

- Make sure that the ``bytecodeHash`` and ``salt`` values are unique for every call in the ``computeAddress()`` function

## Non-functional aspects

- Aim for high test coverage to validate the contract's behavior and catch potential bugs or vulnerabilities
- The protocol execution flow is not explained in efficient way. 
- Its best to explain over all protocol with architecture is easy to understandings 
- Consider designing the contract to be upgradable or allow for versioning. This can help address issues, introduce new features, or adapt to evolving requirements without disrupting the entire system

## Time spent on analysis 
``100 Hours``

























































































### Time spent:
100 hours