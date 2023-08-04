
# Low Risk Issues

| Number | Issue | nstances |
|--------|--------|----------|
|[L-01]| Require statements should have error string  | 3 |
|[L-02]| Experimental functionality should not be used in production code  | 3 |
|[L-03]| Functions which set address state variables should have zero address checks  | 7 |
|[L-04]| For loops in public or external functions should be avoided due to high gas costs and possible DOS  | 4 |
|[L-05]| Using zero as a parameter  | 10 |
|[L-06]| Constant array index within iteration  | 7 |
|[L-07]| increase/decrease allowance should be used instead of approve  | 46 |
|[L-08]| Constant decimal values  | 3 |
|[L-09]| Arrays can grow in size without a way to shrink them  | 3 |
|[L-10]| Constructors contains no validation  | 5 |
|[L-11]| The function symbol() is not part of the ERC20 standard  | 3 |
|[L-12]| Burn functions should be protected with a modifier | 2 |
|[L-13]| For loops in public or external functions should be avoided due to high gas costs and possible DOS | 4 |


## [LOW-1]  Require statements should have error string

Adding error strings to require statements in Solidity contracts, although not mandatory, enhances error handling, debugging, and overall contract maintainability. Providing a descriptive error message with each require statement helps identify the specific reason for a transaction failure, making it easier for developers to troubleshoot issues and for users to understand the cause of a revert. Including error strings improves code readability and fosters transparency, as the logic and conditions behind each requirement are clearly communicated

```solidity
file:  master/contracts/twAML.sol
 
12   require(denominator > 0);

44  require(prod1 < denominator);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol#L12

```solidity
file:  contracts/markets/singularity/Singularity.sol

191    require(success || !revertOnFail, _getRevertMsg(result));

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L191 

## [LOW-2]  Experimental functionality should not be used in production code

Experimental pragma features should not be used within production code due to their unstable and untested nature. These features are still under development and have not undergone rigorous scrutiny, making them susceptible to bugs, security vulnerabilities, and potential breaking changes in future Solidity releases.

```solidity
file:  contracts/glp/GlpStrategy.sol

3  pragma experimental ABIEncoderV2;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L3

```solidity
file:  contracts/YieldBox.sol

25    pragma experimental ABIEncoderV2;

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L25

```solidity
file: contracts/YieldBoxRebase.sol

4   pragma experimental ABIEncoderV2;

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol#L4


## [LOW-3]  Functions which set address state variables should have zero address checks

```solidity
file:

471    function setPaymentTokenBeneficiary(
        address _paymentTokenBeneficiary
    ) external onlyOwner {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L471-L475

```solidity
file:  contracts/option-airdrop/AirdropBroker.sol

357       function setPaymentTokenBeneficiary(
        address _paymentTokenBeneficiary
    ) external onlyOwner {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L357-L361

```solidity
file:  contracts/tokens/BaseTapOFT.sol

326        function setTwTap(address _twTap) external onlyOwner {
        twTap = TwTAP(_twTap);
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L326-L328

```solidity
file:  master/contracts/tokens/TapOFT.sol

160       function setMinter(address _minter) external onlyOwner {
        require(_minter != address(0), "address not valid");
        emit MinterUpdated(minter, _minter);
        minter = _minter;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L160-L164

```solidity
file:  contracts/markets/Market.sol

263        function setBigBangEthMarket(address _market) external onlyOwner {
        bigBangEthMarket = _market;
        emit BigBangEthMarketSet(_market);
    }

281       function setConservator(address _conservator) external onlyOwner {
        require(_conservator != address(0), "Penrose: address not valid");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L263-L266

```solidity
file:  master/contracts/glp/GlpStrategy.sol

113       function setFeeRecipient(address recipient) external onlyOwner {
        feeRecipient = recipient;
    }

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L113-L114


## [LOW-4]  For loops in public or external functions should be avoided due to high gas costs and possible DOS

In Solidity, for loops can potentially cause Denial of Service (DoS) attacks if not handled carefully. DoS attacks can occur when an attacker intentionally exploits the gas cost of a function, causing it to run out of gas or making it too expensive for other users to call. Below are some scenarios where for loops can lead to DoS attacks: Nested for loops can become exceptionally gas expensive and should be used sparingly

### Findings

Findings are labeled with ' <= FOUND'

```solidity
file:  contracts/markets/bigBang/BigBang.sol

209    function execute(
        bytes[] calldata calls,
        bool revertOnFail
    ) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {  // <= FOUND
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
            require(success || !revertOnFail, _getRevertMsg(result));
            successes[i] = success;
            results[i] = _getRevertMsg(result);
        }
    }


```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L209-L223


```solidity
file:  contracts/markets/singularity/Singularity.sol

184       ) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {    // <= FOUND
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
            require(success || !revertOnFail, _getRevertMsg(result));
            successes[i] = success;
            results[i] = _getRevertMsg(result);
        }
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L184-L195


```solidity
file:  contracts/option-airdrop/AirdropBroker.sol

365     function collectPaymentTokens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
        require(
            paymentTokenBeneficiary != address(0),
            "adb: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) {  // <= FOUND
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
        }
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L365-L383


```solidity
file:   contracts/options/TapiocaOptionBroker.sol

479       function collectPaymentTokens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
        require(
            paymentTokenBeneficiary != address(0),
            "tOB: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) { // <= FOUND
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
        }
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L479-L497

## [LOW-5]  Using zero as a parameter

Taking 0 as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because '0' can be interpreted as an uninitialized address, leading to transfers to the '0x0' address, effectively burning tokens. Moreover, 0 as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like 0 appropriately. Use require() statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.


```solidity
file:  contracts/markets/Market.sol

314   if (borrowPart == 0) return (0, 0, 0);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L314

```solidity
file:  contracts/markets/bigBang/BigBang.sol

374    _addCollateral(from, from, false, 0, collateralShare);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L374

```solidity
file:  contracts/markets/singularity/SGLCommon.sol
 
151   emit LogAccrue(0, 0, startingInterestPerSecond, 0);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L151

```solidity
file:  contracts/markets/singularity/SGLLeverage.sol

41    _addCollateral(from, from, false, 0, collateralShare);

47   yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);

185  _addCollateral(from, from, false, 0, collateralShare);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol#L41

```solidity
file:  contracts/glp/GlpStrategy.sol

176    glpRewardRouter.mintAndStakeGlp(address(weth), wethAmount, 0, 0);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L176

```solidity
file:  contracts/lido/LidoEthStrategy.sol

109    result = curveStEthPool.exchange(1, 0, toWithdraw, minAmount);

121       ? curveStEthPool.get_dy(1, 0, stEthBalance)

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L109

```solidity
file:   contracts/tOFT/modules/BaseTOFTLeverageModule.sol

217        .buildSwapData(erc20, swapData.tokenOut, amount, 0, false, false);

```
tapiocaz-audit-bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L217

## [LOW-6]  Constant array index within iteration

Using constant array indexes within for loops is error prone as typically [i] is used instead as by using a constant index such as [0] means that only one value will be used/modified within an array, historically many bugs have been introduced through using a constant index rather than the array iteration index i.e i,j. Provided this is intentional and not a typo consider using enum values to make this distinction clear.


```solidity
file:  contracts/markets/bigBang/BigBang.sol

628    _users[0] = user;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L628

```solidity
file:  contracts/Swapper/BaseSwapper.sol

171   path[0] = tokenIn;

172   path[1] = tokenOut;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L171

```solidity
file:  contracts/Swapper/UniswapV2Swapper.sol

75    amountOut = amounts[1];

96    amountIn = amounts[0];

160   amountOut = amounts[1];

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L75

```solidity
file:  contracts/aave/AaveStrategy.sol

147    tokens[0] = address(receiptToken);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L147

## [LOW-7]  increase/decrease allowance should be used instead of approve

In order to prevent front running, increase/decrease allowance should be used in place of approve where possible

```solidity
file:  contracts/markets/bigBang/BigBang.sol

450     asset.approve(address(yieldBox), totalFees);  // <= FOUND

761     asset.approve(address(yieldBox), amount);    // <= FOUND
``` 

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L450

```solidity
file:   master/contracts/aave/AaveStrategy.sol

74        wrappedNative.approve(_lendingPool, type(uint256).max);   // <= FOUND

75        rewardToken.approve(_multiSwapper, type(uint256).max);   // <= FOUND

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L74

```solidity
file:    contracts/Magnetar/modules/MagnetarMarketModule.sol

157                IERC20(collateralAddress).approve(     // <= FOUND
                address(yieldBox),
                collateralAmount
            );

234      IERC20(assetAddress).approve(address(yieldBox), depositAmount);   // <= FOUND
            yieldBox.depositAsset(
                assetId,
                address(this),
                address(this),
                depositAmount,
                0
            );


339     IERC20(bbCollateralAddress).approve(    // <= FOUND
                    address(yieldBox),
                    mintData.collateralDepositData.amount
                );
            
386      IERC20(sglAssetAddress).approve(    // <= FOUND
                address(yieldBox),
                depositData.amount
            );

428     IERC20(address(singularity)).approve(address(yieldBox), fraction);   // <= FOUND
            yieldBox.depositAsset( 
                tOLPSglAssetId,
                address(this),
                address(this),
                fraction,
                0
            );

469     IERC721(lockData.target).approve(    // <= FOUND
                participateData.target,
                tOLPTokenId
            );

478      IERC721(oTapAddress).approve(address(this), oTAPTokenId);    // <= FOUND
            IERC721(oTapAddress).safeTransferFrom(
                address(this),
                user,
                oTAPTokenId,
                "0x"
            );

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L157-L160

```solidity
file:  contracts/balancer/BalancerStrategy.sol

77    wrappedNative.approve(_vault, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L77

```solidity
file:  contracts/compound/CompoundStrategy.sol

58   wrappedNative.approve(_cToken, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L58

```solidity
file:   contracts/convex/ConvexTricryptoStrategy.sol

105          wrappedNative.approve(_lpGetter, type(uint256).max);

106        lpToken.approve(_lpGetter, type(uint256).max);

107        lpToken.approve(_booster, type(uint256).max);

108        rewardToken.approve(_multiSwapper, type(uint256).max);

172     rewardToken.approve(address(swapper), 0);

174    rewardToken.approve(_swapper, type(uint256).max);

181     wrappedNative.approve(address(lpGetter), 0);

183    wrappedNative.approve(_lpGetter, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L105


```solidity
file:   contracts/curve/TricryptoLPStrategy.sol

79         lpToken.approve(_lpGauge, type(uint256).max);

80        lpToken.approve(_lpGetter, type(uint256).max);

81        rewardToken.approve(_multiSwapper, type(uint256).max);

82        wrappedNative.approve(_lpGetter, type(uint256).max);

143      rewardToken.approve(address(swapper), 0);

144    rewardToken.approve(_swapper, type(uint256).max);

152       wrappedNative.approve(address(lpGetter), 0);

154    wrappedNative.approve(_lpGetter, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L79

```solidity
file:    contracts/curve/TricryptoNativeStrategy.sol

78          IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);

79        IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);

80        rewardToken.approve(_multiSwapper, type(uint256).max);

81        wrappedNative.approve(_lpGetter, type(uint256).max);

134            rewardToken.approve(address(swapper), 0);

135        rewardToken.approve(_swapper, type(uint256).max);

143     wrappedNative.approve(address(lpGetter), 0);

145     wrappedNative.approve(_lpGetter, type(uint256).max);
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L78

```solidity
file:  contracts/glp/GlpStrategy.sol

175   weth.approve(address(glpManager), wethAmount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L175

```solidity
file:  contracts/stargate/StargateStrategy.sol

89     stgNative.approve(_lpStaking, type(uint256).max);

90        stgNative.approve(address(router), type(uint256).max);

94     stgTokenReward.approve(_swapper, type(uint256).max);

151   stgTokenReward.approve(address(swapper), 0);

153    stgTokenReward.approve(_swapper, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L89

```solidity
file:   contracts/yearn/YearnStrategy.sol

59     wrappedNative.approve(address(vault), type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L59


```solidity
file:   contracts/Balancer.sol

321    erc20.approve(address(router), _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L321


```solidity 
file:   contracts/tOFT/modules/BaseTOFTStrategyModule.sol

184    _erc20.approve(address(yieldBox), _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L184


## [LOW-8]  Constant decimal values

The use of fixed decimal values such as 1e18 or 1e8 in Solidity contracts can lead to inaccuracies, bugs, and vulnerabilities, particularly when interacting with tokens having different decimal configurations. Not all ERC20 tokens follow the standard 18 decimal places, and assumptions about decimal places can lead to miscalculations.

Resolution: Always retrieve and use the decimals() function from the token contract itself when performing calculations involving token amounts. This ensures that your contract correctly handles tokens with any number of decimal places, mitigating the risk of numerical errors or under/overflows that could jeopardize contract integrity and user funds.

```solidity
file:  contracts/markets/bigBang/BigBang.sol

188    debtRateAgainstEthMarket) / 1e18;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L188


```solidity
file:  contracts/oracle/implementations/ARBTriCryptoOracle.sol

127    ? (_wbtcPrice * _btcPrice) / 1e18

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L127

```solidity
file:  contracts/markets/Market.sol

295    uint256 x = (numerator * 1e18) / denominator;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L295



## [LOW-9]  Arrays can grow in size without a way to shrink them

It's a good practice to maintain control over the size of array state variables in Solidity, especially if they are dynamically updated. If a contract includes a mechanism to push items into an array, it should ideally also provide a mechanism to remove items. This is because Solidity arrays don't automatically shrink when items are deleted - their length needs to be manually adjusted.

Ignoring this can lead to bloated and inefficient contracts. For instance, iterating over a large array can cause your contract to hit the block gas limit. Additionally, if entries are only marked for deletion but never actually removed, you may end up dealing with stale or irrelevant data, which can cause logical errors.

Therefore, implementing a method to 'pop' items from arrays helps manage contract's state, improve efficiency and prevent potential issues related to gas limits or stale data. Always ensure to handle potential underflow conditions when popping elements from the array.


```solidity
file:  contracts/options/TapiocaOptionLiquidityProvision.sol

63    uint256[] public singularities;

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L63

```solidity
file:  contracts/balancer/BalancerStrategy.sol

41    address[] public rewardTokens;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L41

```solidity
file:   contracts/governance/twTAP.sol

97    IERC20[] public rewardTokens;

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L97


## [LOW-10] Constructors contains no validation

In Solidity, when values are being assigned in constructors to unsigned or integer variables, it's crucial to ensure the provided values adhere to the protocol's specific operational boundaries as laid out in the project specifications and documentation. If the constructors lack appropriate validation checks, there's a risk of setting state variables with values that could cause unexpected and potentially detrimental behavior within the contract's operations, violating the intended logic of the protocol. This can compromise the contract's security and impact the maintainability and reliability of the system. In order to avoid such issues, it is recommended to incorporate rigorous validation checks in constructors. These checks should align with the project's defined rules and constraints, making use of Solidity's built-in require function to enforce these conditions. If the validation checks fail, the require function will cause the transaction to revert, ensuring the integrity and adherence to the protocol's expected behavior.



```solidity
file:  contracts/tokens/LTap.sol

32       constructor(
        IERC20 _tapToken,
        uint256 _maxLockedUntil
    ) ERC20("LTAP", "LTAP") ERC20Permit("LTAP") {
        tapToken = _tapToken;
        lockedUntil = _maxLockedUntil;
        maxLockedUntil = _maxLockedUntil;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol#L32-L39


```solidity
file:  contracts/oracle/implementations/SGOracle.sol

30       constructor(
        string memory __name,
        string memory __symbol,
        IStargatePool pool,
        AggregatorV2V3Interface _underlying
    ) {
        _name = __name;
        _symbol = __symbol;
        SG_POOL = pool;
        UNDERLYING = _underlying;
    }

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/SGOracle.sol#L30-L40


```solidity
file:  contracts/option-airdrop/AirdropBroker.sol
 
98      constructor(
        address _aoTAP,
        address payable _tapOFT,
        address _pcnft,
        address _paymentTokenBeneficiary,
        address _owner
    ) {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tapOFT = TapOFT(_tapOFT);
        aoTAP = AOTAP(_aoTAP);
        PCNFT = IERC721(_pcnft);
        owner = _owner;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L98-L110

```solidity
file:  contracts/option-airdrop/aoTAP.sol

39     constructor(
        address _owner
    ) ERC721("Airdrop Option TAP", "aoTAP") ERC721Permit("Airdrop Option TAP") {
        owner = _owner;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L39-L43


```solidity
file:  contracts/options/TapiocaOptionBroker.sol

81      constructor(
        address _tOLP,
        address _oTAP,
        address payable _tapOFT,
        address _paymentTokenBeneficiary,
        uint256 _epochDuration,
        address _owner
    ) {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tOLP = TapiocaOptionLiquidityProvision(_tOLP);
        tapOFT = TapOFT(_tapOFT);
        oTAP = OTAP(_oTAP);
        EPOCH_DURATION = _epochDuration;
        owner = _owner;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L81-L95


## [LOW-11] The function symbol() is not part of the ERC20 standard

Assuming that the symbol() function is always present in an ERC20 contract can lead to unexpected issues, as not all ERC20 tokens implement it. Some tokens may omit this function or use a non-standard implementation. If your contract makes an external call to symbol() on a token that does not implement this function, it will result in a runtime error, possibly breaking your contract's functionality. The recommended solution is to use try/catch statements when calling symbol(). This way, your contract can handle cases where the function is missing or implemented differently, ensuring robust interaction with any ERC20 token.


```solidity
file:  contracts/markets/singularity/SGLStorage.sol

161    asset.safeSymbol(),

163   oracle.symbol(oracleData)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L161

## [LOW-12] Burn functions should be protected with a modifier

```solidity
file  contracts/option-airdrop/aoTAP.sol

128   function burn(uint256 _tokenId) external {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L128


```solidity
file:  contracts/options/oTAP.sol

115   function burn(uint256 _tokenId) external {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L115

## [LOW-10] For loops in public or external functions should be avoided due to high gas costs and possible DOS

In Solidity, for loops can potentially cause Denial of Service (DoS) attacks if not handled carefully. DoS attacks can occur when an attacker intentionally exploits the gas cost of a function, causing it to run out of gas or making it too expensive for other users to call. Below are some scenarios where for loops can lead to DoS attacks: Nested for loops can become exceptionally gas expensive and should be used sparingly



```solidity
file:   contracts/governance/twTAP.sol

397       function advanceWeek(uint256 _limit) public {
        // TODO: Make whole function unchecked
        uint256 cur = currentWeek();
        uint256 week = lastProcessedWeek;
        uint256 goal = cur;
        unchecked {
            if (goal - week > _limit) {
                goal = week + _limit;
            }
        }
        uint256 len = rewardTokens.length;
        while (week < goal) {
            WeekTotals storage prev = weekTotals[week];
            WeekTotals storage next = weekTotals[++week];
            // TODO: Prove that math is safe
            next.netActiveVotes += prev.netActiveVotes;
            for (uint256 i = 0; i < len; ) {
            
                next.totalDistPerVote[i] += prev.totalDistPerVote[i];
                unchecked {
                    ++i;
                }
            }
        }
        lastProcessedWeek = goal;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L397-L422


```solidity
file:   contracts/options/TapiocaOptionBroker.sol

479        function collectPaymentTokens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
        require(
            paymentTokenBeneficiary != address(0),
            "tOB: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
        }
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L479-497

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

297       function unregisterSingularity(
        IERC20 singularity
    ) external onlyOwner updateTotalSGLPoolWeights {
        uint256 sglAssetID = activeSingularities[singularity].sglAssetID;
        require(sglAssetID > 0, "tOLP: not registered");

        unchecked {
            uint256[] memory _singularities = singularities;
            uint256 sglLength = _singularities.length;
            uint256 sglLastIndex = sglLength - 1;

            for (uint256 i = 0; i < sglLength; i++) {
                // If last element, just pop
                if (i == sglLastIndex) {
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];
                    singularities.pop();
                } else if (
                    _singularities[i] == sglAssetID && i < sglLastIndex
                ) {
                    // If in the middle, copy last element on deleted element, then pop
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];

                    singularities[i] = _singularities[sglLastIndex];
                    singularities.pop();
                    break;
                }
            }
        }

        emit UnregisterSingularity(address(singularity), sglAssetID);
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L297-L329

```solidity
file:   tree/master/contracts/Penrose.sol

542       function _getMasterContractLength(
        IPenrose.MasterContract[] memory array
    ) public view returns (address[] memory markets) {
        uint256 _masterContractLength = array.length;
        uint256 marketsLength = 0;

        unchecked {
            // We first compute the length of the markets array
            for (uint256 i = 0; i < _masterContractLength; ) {
                marketsLength += clonesOfCount(array[i].location);

                ++i;
            }
        }

        markets = new address[](marketsLength);

        uint256 marketIndex;
        uint256 clonesOfLength;

        unchecked {
            // We populate the array
            for (uint256 i = 0; i < _masterContractLength; ) {
                address mcLocation = array[i].location;
                clonesOfLength = clonesOfCount(mcLocation);

                // Loop through clones of the current MC.
                for (uint256 j = 0; j < clonesOfLength; ) {
                    markets[marketIndex] = clonesOf[mcLocation][j];
                    ++marketIndex;
                    ++j;
                }
                ++i;
            }
        }
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L542-L577