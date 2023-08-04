# gas

# summary

|   no   | issue |  instance  |
|------|-------|------------|
|[G-01]|Use calldata instead of memory for function arguments that do not get mutated|5|
|[G-02]|Can Make The Variable Outside The Loop To Save Gas|15|
|[G-03]|Use assembly to write address storage values|61|
|[G-04]|Use nested if statements instead of &&|10|
|[G-05]|Make 3 event parameters indexed when possible
|[G-06]|Sort Solidity operations using short-circuit mode|2|
|[G-07]|Amounts should be checked for 0 before calling a transfer|28|
|[G-08]|With assembly, .call (bool success) transfer can be done gas-optimized|5|
|[G-09]|Use constants instead of type(uintx).max|24|
|[G-10]|Use ERC721A instead ERC721|5|
|[G-11]|bytes constants are more eficient than string constans|1|
|[G-12]|Use assembly to hash instead of solidity|3|
|[G-13]| Avoid emitting storage values|52|
|[G-14]|Use of emit inside a loop|1|
|[G-15]|Use selfbalance() instead of address(this).balance|15|
|[G-16]|Gas savings can be achieved by changing the model for assigning value to the structure|6|
|[G-17]|Use != 0 instead of > 0 for unsigned integer comparison|110|
|[G-18]|Use Assembly To Check For address(0)|38|
|[G-19]|Multiple accesses of a mapping/array should use a local variable cache|9|
|[G-20]|keccak256() should only need to be called on a specific string literal once|2|


## [G-01] Use calldata instead of memory for function arguments that do not get mutated

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.


```solidity
File: tap-token-audit/tree/master/contracts/governance/twTAP.sol
363        IERC20[] memory _rewardTokens
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L363


```solidity
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
166        address[] memory rewardTokens,
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/Penrose.sol
426        bytes[] memory data,
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
223        ICommonData.IApproval[] memory approvals
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

```solidity
File: tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
163        ICommonData.IApproval[] memory approvals
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol



## [G-02] Can Make The Variable Outside The Loop To Save Gas 

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:



```solidity
File: tap-token-audit/tree/master/contracts/governance/twTAP.sol
235      uint256 net = cur.totalDistPerVote[i] - prev.totalDistPerVote[i];

488      uint256 amount = amounts[i];

508      uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];

509      uint256 amount = amounts[i];
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L235

```solidity
File: tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
566   uint256 currentPoolWeight = sglPools[i].poolWeight;

567   uint256 quotaPerSingularity = muldiv(
                    currentPoolWeight,
                    _epochTAP,
                    totalWeights
                );
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L566

```solidity
File: tapioca-bar-audit/tree/master/contracts/Penrose.sol
565      address mcLocation = array[i].location;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L565

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
216         (bool success, bytes memory result) = address(this).delegatecall(

667         address user = users[i];
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L216

```solidity
File: tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol
108       address user = users[i];

385       address user = users[i];
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L108

```solidity
File: tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
188            (bool success, bytes memory result) = address(this).delegatecall(    
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L188

```solidity
File: tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol
978      (uint128 totalAssetElastic, uint128 totalAssetBase) = sgl //

1008     (uint64 debtRate, uint64 lastAccrued) = bigBang.accrueInfo();

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L978

```solidity
File: tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol
73            uint256 val = calli.value;
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L73





## [G-03] Use assembly to write address storage values

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.




```solidity
File: tap-token-audit/tree/master/contracts/governance/twTAP.sol
125        tapOFT = TapOFT(_tapOFT);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L125

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
105     paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tapOFT = TapOFT(_tapOFT);
        aoTAP = AOTAP(_aoTAP);
        PCNFT = IERC721(_pcnft);

360        paymentTokenBeneficiary = _paymentTokenBeneficiary;
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L105

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
89      paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tOLP = TapiocaOptionLiquidityProvision(_tOLP);
        tapOFT = TapOFT(_tapOFT);
        oTAP = OTAP(_oTAP);

474        paymentTokenBeneficiary = _paymentTokenBeneficiary;        
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L89

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol
74        yieldBox = IYieldBox(_yieldBox);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L74

```solidity
File: tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
327        twTap = TwTAP(_twTap);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L327

```solidity
File:  tapioca-bar-audit/tree/master/contracts/Penrose.sol
264        bigBangEthMarket = _market;

284        conservator = _conservator;

292        usdoToken = IERC20(_usdoToken);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L264

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/Market.sol
189            conservator = _conservator;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L189

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
92      liquidationModule = SGLLiquidation(_liquidationModule);
        collateralModule = SGLCollateral(_collateralModule);
        borrowModule = SGLBorrow(_borrowModule);
        leverageModule = SGLLeverage(_leverageModule);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L92

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
75      leverageModule = USDOLeverageModule(_leverageModule);
        marketModule = USDOMarketModule(_marketModule);
        optionsModule = USDOOptionsModule(_optionsModule);

108        conservator = _conservator;        
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L75



```solidity
File:  tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol
39   swapRouter = IUniswapV2Router02(_router);

40   factory = IUniswapV2Factory(_factory);
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L39

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol
55     wrappedNative = IERC20(_token);

56     cToken = ICToken(_cToken);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L55

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol
93      wrappedNative = IERC20(_token);
        swapper = ISwapper(_multiSwapper);

        zap = IConvexZap(_zap);
        booster = IConvexBooster(_booster);
        lpGetter = ITricryptoLPGetter(_lpGetter);
        rewardPool = IConvexRewardPool(_rewadPool);

173     swapper = ISwapper(_swapper);

182     lpGetter = ITricryptoLPGetter(_lpGetter);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L93

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol
72      swapper = ISwapper(_multiSwapper);
        lpGetter = ITricryptoLPGetter(_lpGetter);
        lpGauge = ITricryptoLPGauge(_lpGauge);
        minter = ICurveMinter(_minter);

145      swapper = ISwapper(_swapper);
        
153      lpGetter = ITricryptoLPGetter(_lpGetter);        
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L72

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol
70      wrappedNative = IERC20(_token);
        swapper = ISwapper(_multiSwapper);

        lpGauge = ITricryptoLPGauge(_lpGauge);
        minter = ICurveMinter(_minter);
        lpGetter = ITricryptoLPGetter(_lpGetter);

136     swapper = ISwapper(_swapper);

144     lpGetter = ITricryptoLPGetter(_lpGetter); 
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L70

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol
114      feeRecipient = recipient;
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L114

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol
58      wrappedNative = IERC20(_token);
        stEth = IStEth(_stEth);
        curveStEthPool = ICurveEthStEthPool(_curvePool);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L58

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol
77      wrappedNative = IERC20(_token);
        swapper = ISwapper(_swapper);
        stgEthPool = _stgEthPool;

        addLiquidityRouter = IRouterETH(_ethRouter);
        lpStaking = ILPStaking(_lpStaking);

152     swapper = ISwapper(_swapper);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L77

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol
56      wrappedNative = IERC20(_token);
        vault = IYearnVault(_vault);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L56

```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol
129     routerETH = IStargateRouter(_routerETH);
        router = IStargateRouter(_router);
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L129

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
74      leverageModule = BaseTOFTLeverageModule(_leverageModule);
        strategyModule = BaseTOFTStrategyModule(_strategyModule);
        marketModule = BaseTOFTMarketModule(_marketModule);
        optionsModule = BaseTOFTOptionsModule(_optionsModule);
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L74



## [G-04]Use nested if statements instead of &&

using nested if statements instead of logical operators could potentially save gas in certain situations.

This is because every operation in a smart contract consumes gas, and the cost of executing a logical operator like && or || is higher than the cost of executing a simple comparison or arithmetic operation like == or +. Therefore, if a smart contract has many conditions to check, using nested if statements instead of logical operators could potentially reduce the overall gas cost.




```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
370          if (!success && !_forwardRevert) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L370

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol
1046         if (!success && !allowFailure) {
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1046

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
402        if (lendAmount == 0 && depositData.deposit) {
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
171    if (daysPassed && balanceOfStkAave > 0) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol
226        if (!isNative && _ercData.length == 0) revert PoolInfoRequired();
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L226

```solidity
File: tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol
121    if (_revertOnFailure && !success) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L121

```solidity
File: tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
412       if (!success && !_forwardRevert) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L412

```solidity
File: Yieldbox/tree/master/contracts/BoringMath.sol
27        if (roundUp && (result * div) / mul < value) {
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L27

```solidity
File:  Yieldbox/tree/master/contracts/YieldBoxRebase.sol
35    if (roundUp && (share * totalAmount) / totalShares_ < amount) {

58    if (roundUp && (amount * totalShares_) / totalAmount < share) { 
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol#L35




## [G-05] Make 3 event parameters indexed when possible

In Solidity, it is often more gas-efficient to mark event parameters as "indexed" when possible. This is because indexed parameters can be efficiently searched for and filtered on the Ethereum blockchain, and they do not contribute to the storage size of the event log.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events. When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.
Exmple:
```
• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);
```
```solidity
File:  tap-token-audit/tree/master/contracts/Vesting.sol
60    event UserRegistered(address indexed user, uint256 amount);

62    event Claimed(address indexed user, uint256 amount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L60

```solidity
File:  tap-token-audit/tree/master/contracts/governance/twTAP.sol
134  event Participate(
        address indexed participant,
        uint256 tapAmount,
        uint256 multiplier
    );

139 event AMLDivergence(
        uint256 cumulative,
        uint256 averageMagnitude,
        uint256 totalParticipants
    );


144     event ExitPosition(uint256 tokenId, uint256 amount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L134

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
115    event Participate(uint256 indexed epoch, uint256 aoTAPTokenID);

123    event NewEpoch(uint256 indexed epoch, uint256 epochTAPValuation);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L115

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
100  event Participate(
        uint256 indexed epoch,
        uint256 indexed sglAssetID,
        uint256 totalDeposited,
        LockPosition lock,
        uint256 discount
    );

107  event AMLDivergence(
        uint256 indexed epoch,
        uint256 cumulative,
        uint256 averageMagnitude,
        uint256 totalParticipants
    );

120   event NewEpoch(
        uint256 indexed epoch,
        uint256 extractedTAP,
        uint256 epochTAPValuation
    );

125   event ExitPosition(
        uint256 indexed epoch,
        uint256 indexed tokenId,
        uint256 amount
    );            
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L100

```solidity
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
78    event Emitted(uint256 week, uint256 amount);

80    event Minted(address indexed _by, address indexed _to, uint256 _amount);

82    event Burned(address indexed _from, uint256 _amount);

84    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L78

```solidity
File: tapioca-bar-audit/tree/master/contracts/Penrose.sol
138    event ProtocolWithdrawal(IMarket[] markets, uint256 timestamp);

140    event RegisterSingularityMasterContract(
        address location,
        IPenrose.ContractType risk
       );

145   event RegisterBigBangMasterContract(
        address location,
        IPenrose.ContractType risk
    );

150    event RegisterSingularity(address location, address masterContract);

152    event RegisterBigBang(address location, address masterContract);

154    event FeeToUpdate(address newFeeTo);

156    event SwapperUpdate(address swapper, bool isRegistered);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L22

```solidity
File: tapioca-bar-audit/tree/master/contracts/markets/Market.sol
94    event LogExchangeRate(uint256 rate);

96    event LogBorrowCapUpdated(uint256 _oldVal, uint256 _newVal);

102   event Liquidated(
        address liquidator,
        address[] users,
        uint256 liquidatorReward,
        uint256 protocolReward,
        uint256 repayedAmount,
        uint256 collateralShareRemoved
    );

111    event LogBorrowingFee(uint256 _oldVal, uint256 _newVal);

113     event LiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L94

```solidity
File: tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol
66  event ApprovalBorrow(
        address indexed owner,
        address indexed spender,
        uint256 value
    );
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L66

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
63    event LogAccrue(uint256 accruedAmount, uint64 rate);

65    event LogAddCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );

71  event LogRemoveCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );   

77 event LogBorrow(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 feeAmount,
        uint256 part
    );

85    event LogRepay(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 part
    );

92    event MinDebtRateUpdated(uint256 oldVal, uint256 newVal);

94    event MaxDebtRateUpdated(uint256 oldVal, uint256 newVal);

96    event DebtRateAgainstEthUpdated(uint256 oldVal, uint256 newVal);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L63

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol
    event LogAccrue(
        uint256 accruedAmount,
        uint256 feeFraction,
        uint64 rate,
        uint256 utilization
    );
    /// @notice event emitted when collateral is added
    event LogAddCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );
    /// @notice event emitted when asset is added
    event LogAddAsset(
        address indexed from,
        address indexed to,
        uint256 share,
        uint256 fraction
    );
    /// @notice event emitted when collateral is removed
    event LogRemoveCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );
    /// @notice event emitted when asset is removed
    event LogRemoveAsset(
        address indexed from,
        address indexed to,
        uint256 share,
        uint256 fraction
    );
    /// @notice event emitted when asset is borrowed
    event LogBorrow(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 feeAmount,
        uint256 part
    );
    /// @notice event emitted when asset is repayed
    event LogRepay(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 part
    );
    /// @notice event emitted when fees are extracted
    event LogWithdrawFees(address indexed feeTo, uint256 feesEarnedFraction);
    /// @notice event emitted when fees are deposited to YieldBox
    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);
    /// @notice event emitted when the minimum target utilization is updated
    event MinimumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the maximum target utilization is updated
    event MaximumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the minimum interest per second is updated
    event MinimumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the maximum interest per second is updated
    event MaximumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the interest elasticity updated
    event InterestElasticityUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the LQ collateralization rate is updated
    event LqCollateralizationRateUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the order book liquidation multiplier rate is updated
    event OrderBookLiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the bid execution swapper is updated
    event BidExecutionSwapperUpdated(address newAddress);
    /// @notice event emitted when the usdo swapper is updated
    event UsdoSwapperUpdated(address newAddress);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L70-L138

```solidity
File: tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol
331    event ApprovalForAll(address owner, address operator, bool approved);
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol#L331

```solidity
File: tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol
43    event PoolFee(uint256 _old, uint256 _new);
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol#L43

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
53  event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File: tapiocaz-audit/tree/master/contracts/Balancer.sol
    event ConnectedChainUpdated(
        address indexed _srcOft,
        uint16 _dstChainId,
        address indexed _dstOft
    );
    /// @notice event emitted when a rebalance operation is performed
    /// @dev rebalancing means sending an amount of the underlying token to one of the connected chains
    event Rebalanced(
        address indexed _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bool _isNative
    );
    /// @notice event emitted when max rebalanceable amount is updated
    event RebalanceAmountUpdated(
        address _srcOft,
        uint16 _dstChainId,
        uint256 _amount,
        uint256 _totalAmount
    );
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L69-L89



```solidity
File:  Yieldbox/tree/master/contracts/NativeTokenFactory.sol
28    event TokenCreated(address indexed creator, string name, string symbol, uint8 decimals, uint256 tokenId);
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L28






## [G-06] Sort Solidity operations using short-circuit mode

When you use the && operator, if the left operand is false, then the right operand is not evaluated because the result of the expression will always be false regardless of the value of the right operand. Similarly, when you use the || operator, if the left operand is true, then the right operand is not evaluated because the result of the expression will always be true regardless of the value of the right operand.

//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```


```solidity
File: tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol
315 _singularities[i] == sglAssetID && i < sglLastIndex
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L315

```solidity
File:  tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol
144            if (_call[i].revertOnFailure && !success) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L144






## [G-07] Amounts should be checked for 0 before calling a transfer


One common optimization technique in Solidity is to check the input data before calling a transfer function, to avoid unnecessary gas costs. In particular, it is often a good idea to check if the amount being transferred is zero before calling the transfer function.

example: 

 function transfer(address _to, uint256 _value) public returns (bool) {
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    emit Transfer(msg.sender, _to, _value);
    return true;
}

```solidity
File: tap-token-audit/tree/master/contracts/governance/twTAP.sol
260        tapOFT.transferFrom(msg.sender, address(this), _amount);

448        rewardToken.safeTransferFrom(msg.sender, address(this), _amount);

565        tapOFT.transfer(_to, releasedAmount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L260

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
377      paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
509     _paymentToken.transferFrom(
            msg.sender,
            address(this),
            discountedPaymentAmount
        );      

514     tapOFT.transfer(msg.sender, tapAmount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L377

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
230        tOLP.transferFrom(msg.sender, address(this), _tOLPTokenID);

342        tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);

491        paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );

530        _paymentToken.transferFrom(
            msg.sender,
            address(this),
            discountedPaymentAmount
           );
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L230

```solidity
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
144            _transferFrom(address(this), to, amount);

147            _transferFrom(address(this), to, amount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L144

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol
192     yieldBox.transfer(from, address(this), _assetId, share);

243     yieldBox.transfer(address(this), to, assetId, share);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L192

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol
182     IERC20(address(this)).safeTransfer(leverageFor, amount);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L182

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol
180       IERC20(address(this)).safeTransfer(
                    to,
                    lendParams.depositAmount
                );
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L180

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol
191   IERC20(address(this)).safeTransfer(
                    optionsData.from,
                    optionsData.paymentTokenAmount  
              );
240   IERC20(tapSendData.tapOftAddress).safeTransfer(from, tapAmount);              
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L191

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
779         IERC20(_token).safeTransferFrom(_from, address(this), _amount);
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L779

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol
162   IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L162

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
273   wrappedNative.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol
212  wrappedNative.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L212

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol
159   wrappedNative.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L159

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol
344  wrappedNative.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L344

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol
246   lpToken.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L246

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol
239   wrappedNative.safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L239

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol
93   gmx.safeTransfer(address(gmxWethPool), amount);

148 IERC20(contractAddress).safeTransfer(to, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L93






## [G-08] With assembly, .call (bool success) transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol
79    (result.success, result.returnData) = calli.target.call{value: val}(
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L79

```solidity
File:  tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol
120  (success, result) = payable(_toft).call{value: msg.value}(_bytecode);

141  (success, results[i]) = payable(_call[i].toft).call{
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L120

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
380  (bool sent, ) = to.call{value: amount}("");
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L380

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
280   (bool sent, ) = to.call{value: amount}("");
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280

## [G-09] Use constants instead of type(uintx).max


The reason for this is that the type(uintX).max expression involves a computation at runtime, whereas a constant is evaluated at compile-time. This means that using type(uintX).max can result in additional gas costs for each transaction that involves the expression.

By using a constant instead of type(uintX).max, you can avoid these additional gas costs and make your code more efficient.



```solidity
File:  tap-token-audit/tree/master/contracts/governance/twTAP.sol
313  require(expiry < type(uint56).max, "twTAP: too long");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L313

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol
172  if (spenderAllowance != type(uint256).max) {    
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L172

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
75    wrappedNative.approve(_lendingPool, type(uint256).max);

76    rewardToken.approve(_multiSwapper, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L75

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol
77        wrappedNative.approve(_vault, type(uint256).max);

78        IERC20(address(pool)).approve(_vault, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L77

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol
58  wrappedNative.approve(_cToken, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L58

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol
105     wrappedNative.approve(_lpGetter, type(uint256).max);
        lpToken.approve(_lpGetter, type(uint256).max);
        lpToken.approve(_booster, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);

174    rewardToken.approve(_swapper, type(uint256).max);

183    wrappedNative.approve(_lpGetter, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L105

```solidity 
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol
79      lpToken.approve(_lpGauge, type(uint256).max);
        lpToken.approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

144     rewardToken.approve(_swapper, type(uint256).max);

154     wrappedNative.approve(_lpGetter, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L79

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol
78      IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);
        IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

135    rewardToken.approve(_swapper, type(uint256).max);

145     wrappedNative.approve(_lpGetter, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L78

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol
62   IERC20(_stEth).approve(_curvePool, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L62

```solidity
File:   tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol
89      stgNative.approve(_lpStaking, type(uint256).max);
        stgNative.approve(address(router), type(uint256).max);

94      stgTokenReward.approve(_swapper, type(uint256).max);   

153     stgTokenReward.approve(_swapper, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L89

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol
59   wrappedNative.approve(address(vault), type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L59

```solidity
File:  Yieldbox/tree/master/contracts/BoringMath.sol
6  require(a <= type(uint128).max, "BoringMath: uint128 Overflow");

11 require(a <= type(uint64).max, "BoringMath: uint64 Overflow");

16 require(a <= type(uint32).max, "BoringMath: uint32 Overflow");
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L6

## [G-10] Use ERC721A instead ERC721


ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereum's sky-rocketing gas fee.


```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol
6  import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L6

```solidity
File:  tap-token-audit/tree/master/contracts/options/oTAP.sol
5 import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L5

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol
8  import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L8

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
10  import "@openzeppelin/contracts/token/ERC721/IERC721.sol";
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L10

```solidity
File:  Yieldbox/tree/master/contracts/YieldBox.sol
28   import "@boringcrypto/boring-solidity/contracts/interfaces/IERC721.sol";
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L28

## [G-11] bytes constants are more eficient than string constans
```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol
26    string public constant override name = "sGLP";
    string public constant override description =
        "Holds staked GLP tokens and compounds the rewards";
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L26-L28

## [G-12] Use assembly to hash instead of solidity

When it comes to hashing in Solidity, the keccak256 hash function is typically used, and it is implemented in Solidity itself. However, it is possible to write an assembly implementation of the keccak256 hash function, which can potentially be more gas-efficient.

example:

function keccak256hash(bytes memory data) public view returns (bytes32) {
    bytes32 hash;
    assembly {
        let ptr := mload(0x40)
        mstore(ptr, mload(data))
        mstore(0x40, add(ptr, 32))
        if iszero(call(not(0), 0x03, 0, ptr, mload(data), hash, 0x20)) {
            revert(0, 0)
        }
    }
    return hash;
}

```solidity
File:  Yieldbox/tree/master/contracts/YieldBoxPermit.sol
53     bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));

82   bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L53

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
418       bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L418

## [G-13] Avoid emitting storage values

In Solidity, it is important to be mindful of the gas costs associated with emitting events, especially when the event contains storage values. Emitting storage values in an event can be expensive, because it requires reading the value from storage and copying it to memory before emitting the event.

example:

uint256 myValue;

function doSomething() public {
    myValue = 123;
    emit MyEvent(myValue);
}

event MyEvent(uint256 value);


You can pass only the necessary data to the event like this:

function doSomething() public {
    uint256 myValue = 123;
    emit MyEvent(myValue);
}

event MyEvent(uint256 value);


```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
293    emit NewEpoch(epoch, epochTAPValuation);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L293

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol
123  emit Mint(_to, tokenId, option);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L123


```solidity
File:  tap-token-audit/tree/master/contracts/options/oTAP.sol
110          emit Mint(_to, tokenId, option);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L110

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
264         emit AMLDivergence(
                epoch,
                pool.cumulative,
                pool.averageMagnitude,
                pool.totalParticipants
            ); // Register new voting power event

285     emit Participate(
            epoch,
            lock.sglAssetID,
            pool.totalDeposited,
            lock,
            target
        );    

328        emit AMLDivergence(
                epoch,
                pool.cumulative,
                pool.averageMagnitude,
                pool.totalParticipants
            ); // Register new voting power event   

344   emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);

431   emit NewEpoch(epoch, epochTAP, epochTAPValuation);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L264

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol
104   emit UpdateTotalSingularityPoolWeights(totalSingularityPoolWeights);

200   emit Mint(_to, uint128(sglAssetID), lockPosition);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L104

```solidity
File:  tap-token-audit/tree/master/contracts/tokens/TapOFT.sol
143  emit GovernanceChainIdentifierUpdated(
            governanceChainIdentifier,
            _identifier
        );
154    emit PausedUpdated(paused, val);

162    emit MinterUpdated(minter, _minter);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L143

```solidity
File:  tapioca-bar-audit/tree/master/contracts/Penrose.sol
274  emit PausedUpdated(paused, val);

283  emit ConservatorUpdated(conservator, _conservator);

310  emit UsdoTokenUpdated(_usdoToken, usdoAssetId);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L274

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/Market.sol
144   emit LogBorrowingFee(borrowOpeningFee, _val);

152   emit LogBorrowCapUpdated(totalBorrowCap, _cap);

173   emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);

188   emit ConservatorUpdated(conservator, _conservator);

229   emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);

248   emit PausedUpdated(paused, val);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L144

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
483   emit MaxDebtRateUpdated(maxDebtRate, _maxDebtRate);

488    emit DebtRateAgainstEthUpdated(
                    debtRateAgainstEthMarket,
                    _debtRateAgainstEthMarket
                );

500   emit LiquidationMultiplierUpdated(
                    liquidationMultiplier,
                    _liquidationMultiplier
                );                                
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L483

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
499  emit MinimumTargetUtilizationUpdated(
                minimumTargetUtilization,
                _minimumTargetUtilization
            );
511  emit MaximumTargetUtilizationUpdated(
                maximumTargetUtilization,
                _maximumTargetUtilization
            );

526   emit MinimumInterestPerSecondUpdated(
                minimumInterestPerSecond,
                _minimumInterestPerSecond
            );
538   emit MaximumInterestPerSecondUpdated(
                maximumInterestPerSecond,
                _maximumInterestPerSecond
            );
546   emit InterestElasticityUpdated(
                interestElasticity,
                _interestElasticity
            );     

558  emit LqCollateralizationRateUpdated(
                lqCollateralizationRate,
                _lqCollateralizationRate
            );
567   emit LiquidationMultiplierUpdated(
                liquidationMultiplier,
                _liquidationMultiplier
            );                                                                          
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L499

```solidity 
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
98   emit FlashMintFeeUpdated(flashMintFee, _val);

107  emit ConservatorUpdated(conservator, _conservator);

117  emit PausedUpdated(paused, val);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L98

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
123    emit DepositThreshold(depositThreshold, amount);

130    emit MultiSwapper(address(swapper), _swapper);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L123

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol
90   emit DepositThreshold(depositThreshold, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L90

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol
164   emit DepositThreshold(depositThreshold, amount);

171   emit MultiSwapper(address(swapper), _swapper);

180    emit LPGetterSet(address(lpGetter), _lpGetter);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L164

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol
135     emit DepositThreshold(depositThreshold, amount);

142     emit MultiSwapper(address(swapper), _swapper);

151     emit LPGetterSet(address(lpGetter), _lpGetter);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L135

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol
126  emit DepositThreshold(depositThreshold, amount);

133   emit MultiSwapper(address(swapper), _swapper);

142   emit LPGetterSet(address(lpGetter), _lpGetter);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L126

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol
94   emit DepositThreshold(depositThreshold, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L94

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol
143   emit DepositThreshold(depositThreshold, amount);

150  emit MultiSwapper(address(swapper), _swapper);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L143

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.so
91   emit DepositThreshold(depositThreshold, amount);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L91

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol
120  emit ConnectedChainStatusUpdated(
            _chain,
            connectedChains[_chain],
            _status
        );        
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L120

## [G-14] Use of emit inside a loop

Each time an event is emitted in Solidity, it incurs a fixed cost of 375 gas.


```solidity
File: Yieldbox/tree/master/contracts/YieldBox.sol
350  emit TransferSingle(msg.sender, from, to, assetId, share_);
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol





## [G-15] Use selfbalance() instead of address(this).balance

In Solidity, it is generally recommended to use selfbalance() instead of address(this).balance for checking the balance of the current contract address, because selfbalance() can be more gas-efficient.

The selfbalance() function is a built-in Solidity function that returns the balance of the current contract address. In contrast, address(this).balance requires an additional EVM operation to retrieve the balance of the current contract address.

Here's an example to illustrate the difference:

function getContractBalance() public view returns (uint256) {
    return selfbalance();
}

```solidity
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
312   this.sendFrom{value: address(this).balance}(
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol
227   value: address(this).balance
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol
127   ISendFrom(address(this)).sendFrom{value: address(this).balance}(
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
286    address(this).balance

751     uint256 gas = msg.value > 0 ? msg.value : address(this).balance;
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol

```solidity
File:  tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol
29    address(this).balance >= amount,
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol
105    INative(address(wrappedNative)).deposit{value: address(this).balance}();

107    result = address(this).balance;

151    value: address(this).balance
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol
206   result = address(this).balance;
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol

```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol
286    if (address(this).balance < _amount) revert ExceedsBalance();
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
230  value: address(this).balance
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol
142    ISendFrom(address(this)).sendFrom{value: address(this).balance}(
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol
225   address(this).balance
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol



## [G-16] Gas savings can be achieved by changing the model for assigning value to the structure





```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol
            ITapiocaOFT.IBorrowParams({
                amount: amountOut,
                borrowAmount: 0,
                marketHelper: externalData.magnetar,
                market: externalData.srcMarket
            }),
            ICommonData.IWithdrawParams({
                withdraw: false,
                withdrawLzFeeAmount: 0,
                withdrawOnOtherChain: false,
                withdrawLzChainId: 0,
                withdrawAdapterParams: "0x"
            }),
            ICommonData.ISendOptions({
                extraGasLimit: lzData.srcExtraGasLimit,
                zroPaymentAddress: lzData.zroPaymentAddress
            }),
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L233-L249

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol
                IUSDOBase.IMintData({
                    mint: false,
                    mintAmount: 0,
                    collateralDepositData: ICommonData.IDepositData({
                        deposit: false,
                        amount: 0,
                        extractFromSender: false
                    })
                }),
                ICommonData.IDepositData({
                    deposit: true,
                    amount: lendParams.depositAmount,
                    extractFromSender: true
                }),
                lendParams.lockData,
                lendParams.participateData,
                ICommonData.ICommonExternalContracts({
                    magnetar: address(0),
                    singularity: lendParams.market,
                    bigBang: address(0)
                })
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L218-L238

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol
231             ISendFrom.LzCallParams({
                    refundAddress: payable(from),
                    zroPaymentAddress: tapSendData.zroPaymentAddress,
                    adapterParams: LzLib.buildDefaultAdapterParams(
                        tapSendData.extraGas
                    )
                })
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L231

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol
236              IUSDOBase.ILendOrRepayParams({
                repay: true,
                depositAmount: amountOut,
                repayAmount: repayableAmount,
                marketHelper: externalData.magnetar,
                market: externalData.srcMarket,
                removeCollateral: false,
                removeCollateralShare: 0,
                lockData: ITapiocaOptionLiquidityProvision.IOptionsLockData({
                    lock: false,
                    target: address(0),
                    lockDuration: 0,
                    amount: 0,
                    fraction: 0
                }),
                participateData: ITapiocaOptionsBroker.IOptionsParticipateData({
                    participate: false,
                    target: address(0),
                    tOLPTokenId: 0
                })
            }),
            approvals,
            ICommonData.IWithdrawParams({
                withdraw: false,
                withdrawLzFeeAmount: 0,
                withdrawOnOtherChain: false,
                withdrawLzChainId: 0,
                withdrawAdapterParams: "0x"
            }),
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L236-L264






## [G-17] Use != 0 instead of > 0 for unsigned integer comparison
In Solidity, unsigned integers are compared using the same comparison operators as signed integers (>, <, >=, <=, ==, !=). However, since Solidity does not have a built-in unsigned integer type, unsigned integers are represented using the uint type.

When comparing a uint to 0 in Solidity, it is generally safe to use the > 0 operator, since Solidity performs unsigned integer comparisons by default. However, using != 0 instead of > 0 may still result in a small gas savings, especially in cases where the comparison is performed frequently or in a loop.

Here's an example of using != 0 instead of > 0 for an unsigned integer comparison in Solidity:

function foo(uint x) public pure returns (bool) {
    // Compare x to 0 using !=
    return (x != 0);
}
In this example, the function foo takes a uint parameter x and returns true if x is not equal to 0, and false otherwise. By using != 0 instead of > 0, we ensure that an unsigned comparison is performed, potentially resulting in a small gas savings.


```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol
106           if (claimable > 0) {

153           if (claimable > 0) {

241           if (queued > 0) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L106

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol
119          if (feeAmount > 0) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L119


```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol
120           uint256 calcEth = stEthBalance > 0
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L120


```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol
122           if (claimable > 0) {

165           if (unclaimed > 0) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L122


```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol
149            canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L149

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
364           require(msg.value > 0, "TOFT_0");
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L364

```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
135           if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L135


```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol
186          if (approvals.length > 0) {

226          if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L186


```solidity
File: tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol
138          if (approvals.length > 0) {

231           if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L131


```solidity
File:  tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol
56           require(amount > 0, "TOFT_0");

98           require(amount > 0, "TOFT_0");

181          _amount = _share > 0
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L56


```solidity
File:  tap-token-audit/tree/master/contracts/twAML.sol
12    require(denominator > 0);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol#L12


```solidity
File:   tap-token-audit/tree/master/contracts/Vesting.sol
68         require(_duration > 0, "Vesting: no vesting");

131         if (start > 0) revert Initialized();

134        if (users[_user].amount > 0) revert AlreadyRegistered();

152        if (start > 0) revert Initialized();
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L68



```solidity
File:Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol
489                if (amount > 0) {

511                  if (amount > 0) {
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L489

```solidity
File:   tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
203        require(cachedEpoch > 0, "adb: Airdrop not started");

392        require(_eligibleAmount > 0, "adb: Not eligible");

467         require(_eligibleAmount > 0, "adb: Not eligible");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#203

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
419        require(singularities.length > 0, "tOB: No active singularities");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L419



```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol
177         require(_lockDuration > 0, "tOLP: lock duration must be > 0");

178        require(_amount > 0, "tOLP: amount must be > 0");

181         require(sglAssetID > 0, "tOLP: singularity not active");

264          activeSingularities[singularity].sglAssetID > 0,

281        require(assetID > 0, "tOLP: invalid asset ID");

288         activeSingularities[singularity].poolWeight = weight > 0 ? weight : 1;

301         require(sglAssetID > 0, "tOLP: not registered");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L177


```solidity
File: tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
104           require(duration > 0, "TapOFT: Small duration");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L104


```solidity
File:  tap-token-audit/tree/master/contracts/tokens/TapOFT.sol
201          if (emissionForWeek[week] > 0) return 0;

222         require(_amount > 0, "amount not valid");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L201

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/Market.sol
171    if (_borrowOpeningFee > 0) {

182 if (_oracleData.length > 0) {

192  if (_callerFee > 0) {

197 if (_protocolFee > 0) {

202   if (_liquidationBonusAmount > 0) {

210   if (_minLiquidatorReward > 0) {

219  if (_maxLiquidatorReward > 0) {

228   if (_totalBorrowCap > 0) {

233   if (_collateralizationRate > 0) {

244    require(rate > 0, "Market: invalid rate");
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L171


```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
152  	 EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0

348  	if (supplyShare > 0) {

449    	if (totalFees > 0) {

475    	if (_minDebtRate > 0) {

481    	if (_maxDebtRate > 0) {

487    	if (_debtRateAgainstEthMarket > 0) {

495   	if (_liquidationMultiplier > 0) {

604	if (_dexData.length > 0) {

680	require(liquidatedCount > 0, "SGL: no users found");

734	if (toBurn > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L152


```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol
159              if (supplyShare > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol#L159

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol
233        if (liquidationBonusAmount > 0) {

338            if (dexData.length > 0) {

397            require(liquidatedCount > 0, "SGL: no users found");
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L233



```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
131 EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0

480 if (accrueInfo.feesEarnedFraction > 0) {

498 if (_minimumTargetUtilization > 0) {

506   if (_maximumTargetUtilization > 0) {

521  if (_minimumInterestPerSecond > 0) {

533 if (_maximumInterestPerSecond > 0) {

545   if (_interestElasticity > 0) {

553   if (_lqCollateralizationRate > 0) {

565   if (_liquidationMultiplier > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L131

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol
89        require(amount > 0, "USDO: amount not valid");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol#L89

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol
119          if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L119


```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol
123          if (approvals.length > 0) {

197           if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L123



```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol
123          if (approvals.length > 0) {

216           if (approvals.length > 0) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L123



```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol
206       _action.call.length > 0,

235            if (_action.value > 0) {
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L206


```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
171  if (collateralAmount > 0) {

184    if (borrowAmount > 0) {

228    if (depositAmount > 0) {

245     if (repayAmount > 0) {

248    depositAmount > 0 ? address(this) : user,

258   if (collateralAmount > 0) {

353   if (mintData.collateralDepositData.amount > 0) {

405    if (lendAmount > 0) {

416     if (lockData.fraction > 0) {

511 removeAndRepayData.exitData.oTAPTokenID > 0,

717   refundAddress: msg.value > 0 ? refundAddress : payable(this),

743   require(withdrawData.length > 0, "MagnetarV2: withdrawData is empty");

751  uint256 gas = msg.value > 0 ? msg.value : address(this).balance;

761   gas > 0 ? payable(msg.sender) : payable(this),
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L171


```solidity
File:  tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol
127   if (amounts.amountIn > 0 || amounts.amountOut > 0) {

131   if (tokenInId > 0) {

136  if (tokenOutId > 0) {
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L127



```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
103   if (claimable > 0) {

145   if (unclaimedStkAave > 0) {

158   if (claimable > 0) {

168   if (currentCooldown > 0) {

171  if (daysPassed && balanceOfStkAave > 0) {

174   } else if (balanceOfStkAave > 0) {
```  
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L103

```solidity
File: tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol
133        if (claimable > 0) {

196        if (rewards[i] > 0) {

250        tempData.rewardContracts.length > 0,
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L133


```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol
113           if (claimable > 0) {

162           if (claimable > 0) {

249             if (queued > 0) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L113





## [G-18] Use Assembly To Check For address(0)


checking whether an address is equal to 0x0 (or address(0)) is a common operation that can be done using the == operator. However, this operation can be expensive in terms of gas cost, especially if it is performed frequently or in a loop.



```solidity
File:  tap-token-audit/tree/master/contracts/Vesting.sol
132        if (_user == address(0)) revert AddressNotValid();
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L132


```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
168              paymentTokenOracle.oracle != IOracle(address(0)),

243              paymentTokenOracle.oracle != IOracle(address(0)),

369              paymentTokenBeneficiary != address(0),
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L168


```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol
140           require(broker == address(0), "AOTAP: only once");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L140



```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol
127          require(broker == address(0), "OTAP: only once");
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L127


```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
171            paymentTokenOracle.oracle != IOracle(address(0)),

369            paymentTokenOracle.oracle != IOracle(address(0)),

483            paymentTokenBeneficiary != address(0),
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L171



```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
134            address(_collateral) != address(0) &&

135            address(_asset) != address(0) &&

136            address(_oracle) != address(0),
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#134

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol
216        emit Transfer(address(0), to, fraction);

237              emit Transfer(from, address(0), fraction);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L216



```solidity
File: tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol
40           if (address(liquidationQueue) != address(0)) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L40


```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
101     address(_collateral) != address(0) &&

102     address(_asset) != address(0) &&

103     address(_oracle) != address(0),

466     emit Transfer(address(0), _feeTo, _feesEarnedFraction);

581     if (address(_liquidationQueue) != address(0)) {

586     if (_bidExecutionSwapper != address(0)) {

591     if (_usdoSwapper != address(0)) {

611     if (module == address(0)) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L101

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
106    require(_conservator != address(0),

354           if (module == address(0)) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L106


```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol
235          magnetar: address(0),

237          bigBang: address(0)
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L235
```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol
1057        if (module == address(0)) {
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1057


```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
305          if (address(singularity) != address(0)) {

308          if (address(bigBang) != address(0)) {

465          lockData.target != address(0),

487          if (address(singularity) != address(0)) {

490          if (address(bigBang) != address(0)) {

718          zroPaymentAddress: address(0),
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L305

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol
19          if (_addr == address(0)) revert AddressNotValid();

56          address(0),

57          address(0),

112         if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {
```
https://github.com/Tapitapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.soloca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L19




## [G‑19] Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata

```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol
144       connectedOFTs[_srcOft][_dstChainId].srcPoolId,
145       connectedOFTs[_srcOft][_dstChainId].dstPoolId

149   canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;

156   connectedOFTs[_srcOft][_dstChainId].rebalanceable,





185   if (conn].ectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)

192   connectedOFTs[_srcOft][_dstChainId].dstOft,

210   connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;



255    connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;

260    connectedOFTs[_srcOft][_dstChainId].rebalanceable
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol


## [G‑20] keccak256() should only need to be called on a specific string literal once

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once

```solidity
File:
29   bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
    bytes32 private constant _PERMIT_TYPEHASH_BORROW =
        keccak256(
            "PermitBorrow(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
   
        );
```        
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L29-L36

```solidity
File:
27  bytes32 private constant _PERMIT_TYPEHASH =
        keccak256("Permit(address owner,address spender,uint256 assetId,uint256 nonce,uint256 deadline)");
    

    bytes32 private constant _PERMIT_ALL_TYPEHASH =
        keccak256("PermitAll(address owner,address spender,uint256 nonce,uint256 deadline)");
```       
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L27-L32




###  the last (two 19 and 20)  missed from bots
