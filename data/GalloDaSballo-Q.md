
* 1. [U - Singularity `execute` is not payable despite a few functions being payable](#U-Singularityexecuteisnotpayabledespiteafewfunctionsbeingpayable)
	* 1.1. [Mitigation](#Mitigation)
* 2. [U - All `compoundAmount` using swappers output are manipulatable](#U-AllcompoundAmountusingswappersoutputaremanipulatable)
* 3. [U - `StargateStratgy.compoundAmount` is ignoring the value of rewards in the strategy](#U-StargateStratgy.compoundAmountisignoringthevalueofrewardsinthestrategy)
* 4. [U - Seems like _accrue can overflow](#U-Seemslike_accruecanoverflow)
* 5. [U - Magnetar is performing `delegatecall` on contracts with different storage pattern](#U-Magnetarisperformingdelegatecalloncontractswithdifferentstoragepattern)
* 6. [L - False Positive for safeApprove to an EOA](#L-FalsePositiveforsafeApprovetoanEOA)
* 7. [L - Return value is already checked by Curve](#L-ReturnvalueisalreadycheckedbyCurve)
* 8. [R - Swap with amountOutMin will revert as long as amountOutMin is > 0 -](#R-SwapwithamountOutMinwillrevertaslongasamountOutMinis0-)
* 9. [L - YieldBox `BaseBufferStrategy` will only work for tokens with 18 decimals Incorrect Assumption around token decimals (maybe QA)](#L-YieldBoxBaseBufferStrategywillonlyworkfortokenswith18decimalsIncorrectAssumptionaroundtokendecimalsmaybeQA)
	* 9.1. [Mitigation](#Mitigation-1)
* 10. [L - Emergency Withdraw is calling compound which is very likely to revert during an emergency](#L-EmergencyWithdrawiscallingcompoundwhichisverylikelytorevertduringanemergency)
* 11. [Mitigation](#Mitigation-1)
* 12. [L - 60 Seconds Oracle Watchtime is easily attackable](#L-60SecondsOracleWatchtimeiseasilyattackable)
* 13. [L - GMX is using 0 min out which could be prone to manipulation or cause a loss due to GLP being imbalanced](#L-GMXisusing0minoutwhichcouldbepronetomanipulationorcausealossduetoGLPbeingimbalanced)
	* 13.1. [Impact](#Impact)
	* 13.2. [POC](#POC)
	* 13.3. [Mitigation](#Mitigation-1)
* 14. [L - Tail Risk for Yearn and Compound Tokens](#L-TailRiskforYearnandCompoundTokens)
* 15. [L - First Donation Exploit - Deposit can be sandwhiched and deposit will accept any slippage](#L-FirstDonationExploit-Depositcanbesandwhichedanddepositwillacceptanyslippage)
* 16. [L - No Performance Fee is Charged](#L-NoPerformanceFeeisCharged)
* 17. [L - stETH-Eth can be View Reentered](#L-stETH-EthcanbeViewReentered)
	* 17.1. [Impact](#Impact-1)
	* 17.2. [Coded POC](#CodedPOC)
* 18. [U - Missing out on Aura](#U-MissingoutonAura)
* 19. [L - Balancer is not view-reentranble](#L-Balancerisnotview-reentranble)
* 20. [L - Balancer LPing is vulnerable to Sandwhiching](#L-BalancerLPingisvulnerabletoSandwhiching)
* 21. [L - Withdrawal Slippage Amount is not used and unnecessary](#L-WithdrawalSlippageAmountisnotusedandunnecessary)
* 22. [L - Withdrawal Slippage Amount is not used and unnecessary](#L-WithdrawalSlippageAmountisnotusedandunnecessary-1)
* 23. [L - stkAAVE claims are delayed by the cooldown - No new cooldown if one is queued](#L-stkAAVEclaimsaredelayedbythecooldown-Nonewcooldownifoneisqueued)
* 24. [L - Incorrectly Handling Multiple Rewards](#L-IncorrectlyHandlingMultipleRewards)
* 25. [Strat doesn't work for most other chains - Prob QA because it's fine for Mainnet](#Stratdoesntworkformostotherchains-ProbQAbecauseitsfineforMainnet)
	* 25.1. [POC](#POC-1)
* 26. [QA: Safe but worth flagging](#QA:Safebutworthflagging)
* 27. [NC - Technically Claimable Here is stale](#NC-TechnicallyClaimableHereisstale)
* 28. [Emergency Withdraw is calling compound which is very likely to revert during an emergency](#EmergencyWithdrawiscallingcompoundwhichisverylikelytorevertduringanemergency)
* 29. [Mitigation](#Mitigation-1)
* 30. [U - If `sglAssetID` is ever duplicated, then accounting can be broken](#U-IfsglAssetIDiseverduplicatedthenaccountingcanbebroken)
* 31. [L - No way to sweep](#L-Nowaytosweep)
* 32. [L - This forces to lock for EPOCH, but you can join at the last few seconds](#L-ThisforcestolockforEPOCHbutyoucanjoinatthelastfewseconds)
* 33. [R - Immutables](#R-Immutables)
* 34. [L - Can be front-run](#L-Canbefront-run)
* 35. [R - Unused lib](#R-Unusedlib)
* 36. [R - aoTAP and oTAP are the same code](#R-aoTAPandoTAParethesamecode)
* 37. [L - You can change the epoch at the edge, but you cannot exercise](#L-Youcanchangetheepochattheedgebutyoucannotexercise)
* 38. [R - Array Length Mismatch with comment](#R-ArrayLengthMismatchwithcomment)
* 39. [L - New users could be added mid event or re-added](#L-Newuserscouldbeaddedmideventorre-added)
* 40. [L - Very low odds of Clash](#L-VerylowoddsofClash)
* 41. [L - Loss of Precision](#L-LossofPrecision)
* 42. [ NC - Forgot event for setter](#NC-Forgoteventforsetter)
* 43. [L - _paymentTokenOracle.oracle.get may use a stale price](#L-_paymentTokenOracle.oracle.getmayuseastaleprice)
* 44. [L -  Unbounded Loop - But safe in most cases](#L-UnboundedLoop-Butsafeinmostcases)
* 45. [L - Overflow possible](#L-Overflowpossible)
* 46. [L - Flashloan pattern looks dangerous - Allowance directly decreased](#L-Flashloanpatternlooksdangerous-Allowancedirectlydecreased)
	* 46.1. [Mitigation](#Mitigation-1)
* 47. [L - Custom Errors will still revert in spite of Try Catch](#L-CustomErrorswillstillrevertinspiteofTryCatch)
* 48. [L - Fees should be sent to `feeTo` and not owner](#L-FeesshouldbesenttofeeToandnotowner)
	* 48.1. [POC](#POC-1)
	* 48.2. [Mitigation Step](#MitigationStep)
* 49. [L - `setMarketConfig` bounds are too high](#L-setMarketConfigboundsaretoohigh)
* 50. [L - Liquidations are enforcing Swappers with Hardcoded Slippage](#L-LiquidationsareenforcingSwapperswithHardcodedSlippage)
* 51. [L - Swappers are Single Segment](#L-SwappersareSingleSegment)
* 52. [L - Duplicate Markets can be added by mistake](#L-DuplicateMarketscanbeaddedbymistake)
* 53. [L - The above looks unnecessary, it's done automatically by `deploy`](#L-Theabovelooksunnecessaryitsdoneautomaticallybydeploy)


# Executive Summary

The Following QA Report is an aggregate of a lot of notes I took in the process of reviewing the codebase

These are mostly notes and considerations classified with the following severities:

- U - Undefined, potentially Medium Severity
- L - Low Severity, a Gotcha, a Risk, but not a Vulnerability without additional requirements
- R - A Refactoring, a suggestion to improve the code
- NC - A Non-Critical / Informational Finding

##  1. <a name='U-Singularityexecuteisnotpayabledespiteafewfunctionsbeingpayable'></a>U - Singularity `execute` is not payable despite a few functions being payable

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L181-L195
```solidity
    function execute(
        bytes[] calldata calls,
        bool revertOnFail
    ) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
            require(success || !revertOnFail, _getRevertMsg(result));
            successes[i] = success;
            results[i] = _getRevertMsg(result);
        }
    }
```


Both `multiHopBuyCollateral` and `multiHopSellCollateral` are `payable` and wouldn't work with `execute`

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L381C1-L401


```solidity
    function multiHopBuyCollateral(
        address from,
        uint256 collateralAmount,
        uint256 borrowAmount,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L409-L427

```solidity
    function multiHopSellCollateral(
        address from,
        uint256 share,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable {
        _executeModule(
            Module.Leverage,
            abi.encodeWithSelector(
                SGLLeverage.multiHopSellCollateral.selector,
                from,
                share,
                swapData,
                lzData,
                externalData
            )
        );
    }
```

###  1.1. <a name='Mitigation'></a>Mitigation

You seem to already have dealt with this by using `multicallValue`
##  2. <a name='U-AllcompoundAmountusingswappersoutputaremanipulatable'></a>U - All `compoundAmount` using swappers output are manipulatable

Stargate: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L116-L135
Curve
Etc

All of these swappers using amountOuts are vulnerable to manipulation anytime there's sufficient rewards

##  3. <a name='U-StargateStratgy.compoundAmountisignoringthevalueofrewardsinthestrategy'></a>U - `StargateStratgy.compoundAmount` is ignoring the value of rewards in the strategy

`compoundAmount` checks for the `pendingStargate`

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L116-L135

```solidity
    function compoundAmount() public view returns (uint256 result) {
        uint256 claimable = lpStaking.pendingStargate(
            lpStakingPid,
            address(this)
        );
        result = 0;
        if (claimable > 0) {
            ISwapper.SwapData memory swapData = swapper.buildSwapData(
                address(stgTokenReward),
                address(wrappedNative),
                claimable,
                0,
                false,
                false
            );
            result = swapper.getOutputAmount(swapData, "");

            result = result - (result * 50) / 10_000; //0.5%
        }
    }
```

Each deposit will release some `stargate` token to the strategy, for this reason `compoundAmount` should also check the balance of `stargate` token

NOTE: Technically this is correct since you're losing those tokens, but if you fix `compound` you should also change `compoundAmount`

##  4. <a name='U-Seemslike_accruecanoverflow'></a>U - Seems like _accrue can overflow

If elastic was 1e21, the interest rate was 1e19
Then 
```solidity
1e21 * x  * 2e6 > type(uint128).max
Type: bool
└ Value: true
➜ x // x = 1e19 / 31536000; 
```

##  5. <a name='U-Magnetarisperformingdelegatecalloncontractswithdifferentstoragepattern'></a>U - Magnetar is performing `delegatecall` on contracts with different storage pattern

Delegate call to contract with different Storage
```solidity
contract MagnetarV2 is Ownable, MagnetarV2Storage {
```
```solidity
contract MagnetarMarketModule is MagnetarV2Storage {
```
Delegatecalls to the Module, which tries to have the same storage, but doesn't import ownable, meaning the storage slots will be read in different locations

In this case it seems to be safe as the Modules don't seem to use any storage variable

##  6. <a name='L-FalsePositiveforsafeApprovetoanEOA'></a>L - False Positive for safeApprove to an EOA

BaseSwapper

```solidity
    function _safeApprove(address token, address to, uint256 value) internal {
        (bool success, bytes memory data) = token.call(
            abi.encodeWithSelector(0x095ea7b3, to, value)
        );
        require(
            success && (data.length == 0 || abi.decode(data, (bool))),
            "BaseSwapper::safeApprove: approve failed"
        );
    }
```

##  7. <a name='L-ReturnvalueisalreadycheckedbyCurve'></a>L - Return value is already checked by Curve

CurveSwapper

```
        uint256 outputAmount = curvePool.get_dy(i, j, amountIn);
        require(outputAmount >= amountOutMin, "insufficient-amount-out");

        uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));

        _safeApprove(tokenIn, address(curvePool), amountIn);
        curvePool.exchange(i, j, amountIn, amountOutMin);
```

No need to check
`require(outputAmount >= amountOutMin, "insufficient-amount-out");`

It's already done in the `exchange` function

##  8. <a name='R-SwapwithamountOutMinwillrevertaslongasamountOutMinis0-'></a>R - Swap with amountOutMin will revert as long as amountOutMin is > 0 -
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/CurveSwapper.sol#L156-L161
```solidity
        uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));

        _safeApprove(tokenIn, address(curvePool), amountIn);
        curvePool.exchange(i, j, amountIn, amountOutMin);

        uint256 balanceAfter = IERC20(tokenOut).balanceOf(address(this));
        require(balanceAfter > balanceBefore, "swap failed");
```
    

##  9. <a name='L-YieldBoxBaseBufferStrategywillonlyworkfortokenswith18decimalsIncorrectAssumptionaroundtokendecimalsmaybeQA'></a>L - YieldBox `BaseBufferStrategy` will only work for tokens with 18 decimals Incorrect Assumption around token decimals (maybe QA)

Will cause the buffer to not work for tokens with lower decimals

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/strategies/BaseBufferStrategy.sol#L57-L70

```solidity
    function deposited(uint256) public override {
        require(msg.sender == address(yieldBox), "Not YieldBox");

        uint256 balance = _balanceInvested();
        uint256 reserve = _reserve();

        // Get the size of the reserve in % (1e18 based)
        uint256 reservePercent = (reserve * 100e18) / (balance + reserve); /// @audit Incorrect Math, assumes tokens have 18 decimals

        // Check if the reserve is too large, if so invest it
        if (reservePercent > MAX_RESERVE_PERCENT) {
            _invest(balance.muldiv(reservePercent - TARGET_RESERVE_PERCENT, 100e18, false));
        }
    }
```

###  9.1. <a name='Mitigation-1'></a>Mitigation

Scale the reserve by token decimals

# QA for Strategies repo

##  10. <a name='L-EmergencyWithdrawiscallingcompoundwhichisverylikelytorevertduringanemergency'></a>L - Emergency Withdraw is calling compound which is very likely to revert during an emergency

In case of an emergency there are only 2 main things that can go wrong:
- Reward System or Selling of tokens is broken
- Staking Contract is Broken

If the Staking Contract is Broken, you won't be able to withdraw either way, so there's nothing that can be done at that point

If the Reward System is broken, you would want to withdraw and skip the `compound`

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L209-L210

This can cause issues in two ways:

1) DOS, a front-runner can simply grief the withdrawal by front-running the tx and making the slippage check fail
2) Revert due to internal reasons, if the Staking Contract or Claim Contract is broken, then `compound` will revert


##  11. <a name='Mitigation-1'></a>Mitigation

Make emergencyWithdraw optionally compound or simply skip the compound (which could be done separately) 

##  12. <a name='L-60SecondsOracleWatchtimeiseasilyattackable'></a>L - 60 Seconds Oracle Watchtime is easily attackable

Most of the times a 60 second TWAP is easily attackable

See my work here:
https://github.com/sherlock-audit/2023-04-splits-judging/blob/15ed1328bed52511a772aeb1a8607db1bcf11163/001-H/112.md


##  13. <a name='L-GMXisusing0minoutwhichcouldbepronetomanipulationorcausealossduetoGLPbeingimbalanced'></a>L - GMX is using 0 min out which could be prone to manipulation or cause a loss due to GLP being imbalanced

###  13.1. <a name='Impact'></a>Impact
The `GlpStrategy` is buying GLP with a 0 minOut


```solidity
            weth.approve(address(glpManager), wethAmount);
            glpRewardRouter.mintAndStakeGlp(address(weth), wethAmount, 0, 0);
```

In most cases, this is fine, because `GLP` uses oracles and has fairly high fees


###  13.2. <a name='POC'></a>POC
However, in some cases, the amount out can still be manipulated, by changing the balance of tokens in GLP

This can be performed if the strategy has sufficiently high funds, to outset the GLP Fees

###  13.3. <a name='Mitigation-1'></a>Mitigation

Add a offChain calculated value for minOut


##  14. <a name='L-TailRiskforYearnandCompoundTokens'></a>L - Tail Risk for Yearn and Compound Tokens

The Yearn and Compound tokens present a quirk, if they are all redeemed, they will reset the PPFS

While the system doesn't allow Flashloan of Collateral, it may or it may compose with tools that do.

For these reasons, it's worth keeping in mind that at no time all of Yearn-Like Vaults or Compound Tokens should be flashloanable without checking that their `exchangeRate` / `ppfs` hasn't changed.

A mock mitigation for the problem is:

```solidity
uint256 currentRate = vault.getPricePerShare();

// Flashloan or similar
require(vault.getPricePerShare() == currentRate, "Must maintain ppfs");
```

This ensures the invariant that tokens borrowed have the same value as tokens repaid

##  15. <a name='L-FirstDonationExploit-Depositcanbesandwhichedanddepositwillacceptanyslippage'></a>L - First Donation Exploit - Deposit can be sandwhiched and deposit will accept any slippage
        if (queued > depositThreshold) {
            vault.deposit(queued, address(this));
            emit AmountDeposited(queued);
            return;
        }

Good old classic rebase attack via first depositor or via donation, yearn vaults are subject to it

If the attacker holds the majority of the tokens, they can rebase the vault shares to cause the deposit call to trigger a loss, the loss will be at most % the new base

If this is done as the first attack, this will be a total loss in most cases

Whereas if it's done later, the loss will be limited to a % of the deposit

##  16. <a name='L-NoPerformanceFeeisCharged'></a>L - No Performance Fee is Charged

https://docs.tapioca.xyz/tapioca/core-technologies/singularity#initial-singularity-markets

In contrast to the docs, that state that a performance fee is charged

No performance fee is charged on `compound("")` meaning the DAO is not earning anything


##  17. <a name='L-stETH-EthcanbeViewReentered'></a>L - stETH-Eth can be View Reentered

###  17.1. <a name='Impact-1'></a>Impact
Due to how the pool is priced, the view-reentrancy is not exploitable

It would be ideal to trigger the lock to ensure no manipulation is undergoing while pricing the strategy

This would require changing the `_currentBalance` to non-view which is most likely a painful change, but one that I recommend overall


###  17.2. <a name='CodedPOC'></a>Coded POC

```python
[PASS] testViewReenter() (gas: 311132)
Logs:
  original_price 999692966893247721
  original_price 1077695319132698530
  Hello from the view-reentrant side
```

```solidity
// SPDX-License Identifier: MIT

pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import "forge-std/console2.sol";

interface ICurvePoolWeird {
    function add_liquidity(uint256[2] memory amounts, uint256 min_mint_amount) external payable returns (uint256);
    function remove_liquidity(uint256 _amount, uint256[2] memory _min_amounts) external returns (uint256[2] memory);
}

interface ICurvePool {
    function add_liquidity(uint256[2] memory amounts, uint256 min_mint_amount) external payable returns (uint256);
    function remove_liquidity(uint256 _amount, uint256[2] memory _min_amounts) external returns (uint256[2] memory);

    function get_virtual_price() external view returns (uint256);
    function remove_liquidity_one_coin(uint256 _token_amount, int128 i, uint256 _min_amount) external;

    function get_dy(int128 i, int128 j, uint256 dx) external view returns (uint256);
}

interface IERC20 {
    function balanceOf(address) external view returns (uint256);
    function approve(address, uint256) external returns (bool);
    function transfer(address, uint256) external returns (bool);
}

contract VieReentrant is Test {
    ICurvePool pool = ICurvePool(0xDC24316b9AE028F1497c275EB9192a3Ea0f67022);
    IERC20 token = IERC20(0x06325440D014e39736583c165C2963BA99fAf14E);

    function start() external payable {
        uint256 original_price = pool.get_dy(1, 0, 1e18);
        console2.log("original_price", original_price);
        console2.log("original_price", pool.get_virtual_price());

        uint256[2] memory minAmts;
        minAmts[0] = 0;
        minAmts[1] = 0;
        pool.remove_liquidity_one_coin(token.balanceOf(address(this)), 0, 0);
    }

    function reEnter() internal {
        console2.log("Hello from the view-reentrant side");
    }

    fallback() external payable {
        // Call the extra thing
        reEnter();
    }
}

contract ViewReentrantTest is Test {
    VieReentrant c;
    IERC20 LP_TOKEN = IERC20(0x06325440D014e39736583c165C2963BA99fAf14E);

    function setUp() public {
        c = new VieReentrant();
    }

    function testViewReenter() public {
        deal(address(LP_TOKEN), address(this), 100_000e18);
        LP_TOKEN.transfer(address(c), LP_TOKEN.balanceOf(address(this)));
        c.start();
    }
}

```

# Balancer Strategies

##  18. <a name='U-MissingoutonAura'></a>U - Missing out on Aura

Aura is pretty lindy and is going to yield higher yield in most cases, it's worth writing an integration for it

##  19. <a name='L-Balancerisnotview-reentranble'></a>L - Balancer is not view-reentranble

The check has flaws, in it's sandwhichable.

But it's not view-reentranble since it uses queryExit which performs a simulation of the swap on the Vault, and triggers the reEntrancy guard


##  20. <a name='L-BalancerLPingisvulnerabletoSandwhiching'></a>L - Balancer LPing is vulnerable to Sandwhiching

See Deposit for X that will trigger deposit threshold
Sandwhich the Balancer Pool
Deposit is Rekt (quote is Spot)


Docs:
https://docs.balancer.fi/reference/contracts/query-functions.html

Clearly stating this should not be relied on

##  21. <a name='L-WithdrawalSlippageAmountisnotusedandunnecessary'></a>L - Withdrawal Slippage Amount is not used and unnecessary


```solidity
        if (amount > queued) {
            uint256 pricePerShare = pool.getRate(); // TODO: This looks off // It's off because rate will not work in single sided exposure since it doesn't account for the prorata / IL
            uint256 decimals = IStrictERC20(address(pool)).decimals();
            uint256 toWithdraw = (((amount - queued) * (10 ** decimals)) /
                pricePerShare);

            _vaultWithdraw(toWithdraw);
        }
```

##  22. <a name='L-WithdrawalSlippageAmountisnotusedandunnecessary-1'></a>L - Withdrawal Slippage Amount is not used and unnecessary
        bptIn = bptIn + (bptIn * 250) / 10_000; //2.5%

2.5% hardcoded slippage is excessive (since you're reading the value onChain)

Because you're quoting from onChain (manipulatable) the check is not necessary either since the Vault will burn whatever the vault asks it to

# AAVE Strategy

##  23. <a name='L-stkAAVEclaimsaredelayedbythecooldown-Nonewcooldownifoneisqueued'></a>L - stkAAVE claims are delayed by the cooldown - No new cooldown if one is queued

https://etherscan.io/address/0xaa9faa887bce5182c39f68ac46c43f36723c395b#code


This means that yield distribution is in consistent

We can deposit during those 12 days (even last second)

To get the yield generated by the stkAAVE that wasn't processed

(QA) Additionally, the strat will require an additional 12 days to release it's yield after it's deprecated, which can also create negative externalities


https://etherscan.io/address/0xaa9faa887bce5182c39f68ac46c43f36723c395b#code#F4#L189

```solidity

  function _cooldown(address from) internal {
    uint256 amount = balanceOf(from);
    require(amount != 0, 'INVALID_BALANCE_ON_COOLDOWN');
    stakersCooldowns[from] = CooldownSnapshot({
      timestamp: uint40(block.timestamp),
      amount: uint216(amount)
    });

    emit Cooldown(from, amount);
  }
```

##  24. <a name='L-IncorrectlyHandlingMultipleRewards'></a>L - Incorrectly Handling Multiple Rewards

Most Staking Contracts allow multiple rewards

This contract only works with one token

This can cause issues, but it's more of a QA issue

The incentives controller on sidechains is slightly different, to allow more than one reward

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L138-L153

```solidity
        // TODO: Missing other vaults
        // TODO: Slippage
        uint256 aaveBalanceBefore = rewardToken.balanceOf(address(this));
        //first claim stkAave
        uint256 unclaimedStkAave = incentivesController.getUserUnclaimedRewards(
            address(this)
        ); // TODO: Figure out if this actually works since stkAAVE may not even be the correct reward

        if (unclaimedStkAave > 0) {
            address[] memory tokens = new address[](1);
            tokens[0] = address(receiptToken);
            incentivesController.claimRewards(
                tokens,
                type(uint256).max,
                address(this)
            );
        }
```




##  25. <a name='Stratdoesntworkformostotherchains-ProbQAbecauseitsfineforMainnet'></a>Strat doesn't work for most other chains - Prob QA because it's fine for Mainnet

The AAVE Strategy only works on mainnet, where stkAAVE exists and it's cooldown feature is available

For most other chains, the reward token is seldom AAVE, and most often the Chain Token (e.g. Polygon) or a LM incentive token

These tokens do not have the function `cooldown`, which will cause the strategy to not work for them


###  25.1. <a name='POC-1'></a>POC
```soldity
        uint256 balanceOfStkAave = stakedRewardToken.balanceOf(address(this));
        if (currentCooldown > 0) {
            //we have an active cooldown; check if we need to cooldown again
            bool daysPassed = (currentCooldown + 12 days) < block.timestamp;
            if (daysPassed && balanceOfStkAave > 0) {
                stakedRewardToken.cooldown();
            }
        } else if (balanceOfStkAave > 0) {
            stakedRewardToken.cooldown(); // TODO: This breaks if it's not stkAAVE which applies to the majority of chains
        }
```


##  26. <a name='QA:Safebutworthflagging'></a>QA: Safe but worth flagging

You could enable claims on behalf, these are disabled by default, so the code is safe

In the future, for gas savings, you may chose to have a claim done by someone else

This would require changing the code in `compound` from a delta balances to absolute balances 

  function redeemOnBehalf(
    address from,
    address to,
    uint256 amount
  ) external override onlyClaimHelper {
    _redeem(from, to, amount);
  }
claimRewardsOnBehalf()
https://docs.aave.com/developers/v/2.0/guides/liquidity-mining#claimrewardsonbehalf

# Curve Strategy

##  27. <a name='NC-TechnicallyClaimableHereisstale'></a>NC - Technically Claimable Here is stale

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L104

```solidity
uint256 claimable = lpGauge.claimable_tokens(address(this));
```

Since the minter may not have advanced period

That said the check is for non-zero so it's fine

# Strategies in General

##  28. <a name='EmergencyWithdrawiscallingcompoundwhichisverylikelytorevertduringanemergency'></a>Emergency Withdraw is calling compound which is very likely to revert during an emergency

In case of an emergency there are only 2 main things that can go wrong:
- Reward System or Selling of tokens is broken
- Staking Contract is Broken

If the Staking Contract is Broken, you won't be able to withdraw either way, so there's nothing that can be done at that point

If the Reward System is broken, you would want to withdraw and skip the `compound`

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L209-L210

This can cause issues in two ways:

1) DOS, a front-runner can simply grief the withdrawal by front-running the tx and making the slippage check fail
2) Revert due to internal reasons, if the Staking Contract or Claim Contract is broken, then `compound` will revert


##  29. <a name='Mitigation-1'></a>Mitigation

Make emergencyWithdraw optionally compound or simply skip the compound (which could be done separately)


# Tap Token QA

##  30. <a name='U-IfsglAssetIDiseverduplicatedthenaccountingcanbebroken'></a>U - If `sglAssetID` is ever duplicated, then accounting can be broken

USD0 is provided by someone, that's gonna mess things up


If USD0 is tracked for `totalDeposited` then this should happen since YieldBox uses unique IDs for Unique Tokens

If USD0 is not meant to be tracked, then this can still happen, if two singularities using the same underlying token are crated


##  31. <a name='L-Nowaytosweep'></a>L - No way to sweep
        uint256 availableToken = _token.balanceOf(address(this));
        if (availableToken < _seededAmount) revert BalanceTooLow();

Ensure the check is the total balance since there's no sweep function


##  32. <a name='L-ThisforcestolockforEPOCHbutyoucanjoinatthelastfewseconds'></a>L - This forces to lock for EPOCH, but you can join at the last few seconds
    function participate(
        uint256 _tOLPTokenID
    ) external returns (uint256 oTAPTokenID) {
        // Compute option parameters
        (bool isPositionActive, LockPosition memory lock) = tOLP.getLock(
            _tOLPTokenID
        );
        require(isPositionActive, "tOB: Position is not active");
        require(lock.lockDuration >= EPOCH_DURATION, "tOB: Duration too short");


##  33. <a name='R-Immutables'></a>R - Immutables

contract LTap is BoringOwnable, ERC20Permit {
    IERC20 tapToken;
    uint256 public lockedUntil;
    uint256 public maxLockedUntil;

##  34. <a name='L-Canbefront-run'></a>L - Can be front-run
    /// @notice ADB claim
    function brokerClaim() external {
        require(broker == address(0), "AOTAP: only once");
        broker = msg.sender;
    }

    Just add to constructor or add `onlyOwner`

Or deploy the token via the Constructor in Airdrop Broker

##  35. <a name='R-Unusedlib'></a>R - Unused lib
    using ExcessivelySafeCall for address;


##  36. <a name='R-aoTAPandoTAParethesamecode'></a>R - aoTAP and oTAP are the same code
Can just import and change the name


##  37. <a name='L-Youcanchangetheepochattheedgebutyoucannotexercise'></a>L - You can change the epoch at the edge, but you cannot exercise
        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");

        Exercising would cause issues, so it's safe
        For consistency you could also switch to the next epoch a second after

##  38. <a name='R-ArrayLengthMismatchwithcomment'></a>R - Array Length Mismatch with comment
    // [OG Pearls, Sushi Frens, Tapiocans, Oysters, Cassava]
    bytes32[4] public phase2MerkleRoots; // merkle root of phase 2 airdrop
    uint8[4] public PHASE_2_AMOUNT_PER_USER = [200, 190, 200, 190];
    uint8[4] public PHASE_2_DISCOUNT_PER_USER = [50, 40, 40, 33]

##  39. <a name='L-Newuserscouldbeaddedmideventorre-added'></a>L - New users could be added mid event or re-added
        if (_phase == 1) {
            for (uint256 i = 0; i < _users.length; i++) {
                phase1Users[_users[i]] = _amounts[i];
            }
        

##  40. <a name='L-VerylowoddsofClash'></a>L - Very low odds of Clash
        require(PCNFT.ownerOf(_tokenID) == msg.sender, "adb: Not eligible");
        address tokenIDToAddress = address(uint160(_tokenID));
        require(
            userParticipation[tokenIDToAddress][3] == false,
            "adb: Already participated"
        );
        // Close eligibility
        // To avoid a potential attack vector, we cast token ID to an address instead of using _to,
        // no conflict possible, tokenID goes from 0 ... 714.
        userParticipation[tokenIDToAddress][3] = true;

But technically can happen since you're casting to Address



##  41. <a name='L-LossofPrecision'></a>L - Loss of Precision

    function _getDiscountedPaymentAmount(
        uint256 _otcAmountInUSD,
        uint256 _paymentTokenValuation,
        uint256 _discount,
        uint256 _paymentTokenDecimals
    ) internal pure returns (uint256 paymentAmount) {
        // Calculate payment amount
        uint256 rawPaymentAmount = _otcAmountInUSD / _paymentTokenValuation;
        paymentAmount =
            rawPaymentAmount -
            muldiv(rawPaymentAmount, _discount, 100e4); // 1e4 is discount decimals, 100 is discount percentage

        paymentAmount = paymentAmount / (10 ** (18 - _paymentTokenDecimals));
    }

Would be best to apply the discounted valuation to _otcAmountInUSD as to reduce the precision loss


##  42. <a name='NC-Forgoteventforsetter'></a> NC - Forgot event for setter
    function setPaymentTokenBeneficiary(
        address _paymentTokenBeneficiary
    ) external onlyOwner {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }

##  43. <a name='L-_paymentTokenOracle.oracle.getmayuseastaleprice'></a>L - _paymentTokenOracle.oracle.get may use a stale price
        // Get payment token valuation
        (, uint256 paymentTokenValuation) = _paymentTokenOracle.oracle.get(
            _paymentTokenOracle.oracleData
        );

Since it doesn't verify that it's fresh

However. chainlink will revert per the code here:
```solidity
            if (
                ratio <= 0 ||
                roundId > answeredInRound ||
                block.timestamp - updatedAt > stalePeriod
            ) revert InvalidChainlinkRate();
            castedRatio = uint256(ratio);
```

##  44. <a name='L-UnboundedLoop-Butsafeinmostcases'></a>L -  Unbounded Loop - But safe in most cases
```solidity
    /// @notice Compute the total pool weight of all active singularity markets
    function _computeSGLPoolWeights() internal view returns (uint256) {
        uint256 total;
        uint256 len = singularities.length;
        for (uint256 i = 0; i < len; i++) {
            total += activeSingularities[sglAssetIDToAddress[singularities[i]]]
                .poolWeight;
        }

        return total;
    }
```
Assuming 15 Million Gas per Block

15*10^6 / 2100 = 7142 singularities before the loop goes OOG

##  45. <a name='L-Overflowpossible'></a>L - Overflow possible
```solidity
uint256 sglAssetID = activeSingularities[_singularity].sglAssetID;
lockPosition.sglAssetID = uint128(sglAssetID);
```

But safe in most cases 3e38 tokens

# Tapioca Bar Audit - QA

##  46. <a name='L-Flashloanpatternlooksdangerous-Allowancedirectlydecreased'></a>L - Flashloan pattern looks dangerous - Allowance directly decreased

The function flashLoan doesn't transfer tokens from the `receiver` but instead updates the allowance and burns those tokens from them.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L81-L104

```solidity
    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override notPaused returns (bool) {
        require(token == address(this), "USDO: token not valid");
        require(maxFlashLoan(token) >= amount, "USDO: amount too big");
        require(amount > 0, "USDO: amount not valid");
        uint256 fee = flashFee(token, amount);
        _mint(address(receiver), amount);

        require(
            receiver.onFlashLoan(msg.sender, token, amount, fee, data) ==
                FLASH_MINT_CALLBACK_SUCCESS,
            "USDO: failed"
        );

        uint256 _allowance = allowance(address(receiver), address(this));
        require(_allowance >= (amount + fee), "USDO: repay not approved");
        _approve(address(receiver), address(this), _allowance - (amount + fee)); /// @audit this is changing allowance
        _burn(address(receiver), amount + fee);
        return true;
    }
```

The allowance is always decreased:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/21716722ad78ee3e9bf21a5b47ff420323c8fe27/contracts/token/ERC20/ERC20.sol#L338-L349

```solidity
    function _approve(address owner, address spender, uint256 value, bool emitEvent) internal virtual {
        if (owner == address(0)) {
            revert ERC20InvalidApprover(address(0));
        }
        if (spender == address(0)) {
            revert ERC20InvalidSpender(address(0));
        }
        _allowances[owner][spender] = value;
        if (emitEvent) {
            emit Approval(owner, spender, value);
        }
    }
```

This is in contrast with using `_spendAllowance` which would work with infinite allowance

This can create gotchas for integrators

###  46.1. <a name='Mitigation-1'></a>Mitigation

Enforce and _approve(0) if you wish to reset the allowance

Otherwise use `_spendAllowance`


##  47. <a name='L-CustomErrorswillstillrevertinspiteofTryCatch'></a>L - Custom Errors will still revert in spite of Try Catch

```solidity
// SPDX-License Identifier: MIT

pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import "forge-std/console2.sol";


contract CatchString is Test {
    error TheError();

    function throwTheError() external {
        revert TheError();
    }

    function testCatch() external returns (uint256) {
        try this.throwTheError() {
            return 1;
        } catch Error(string memory reason) {
            return 2;
        }
    }

}

contract CompoundedStakesFuzz is Test {
    CatchString c;

    function setUp() public {
        c = new CatchString();
    }

    function testRevert() public {
        c.testCatch();
    }

    
}
```

As you can see will still revert
```js
Running 1 test for test/CdpID.invariants.t.sol:CatchString
[FAIL. Reason: TheError()] testCatch():(uint256) (gas: 1012)
Test result: FAILED. 0 passed; 1 failed; finished in 282.44µs
```


##  48. <a name='L-FeesshouldbesenttofeeToandnotowner'></a>L - Fees should be sent to `feeTo` and not owner

`withdrawFeesEarned` is accruing fees to the address `feeTo`

However, these fees are not paid to `feeTo`, but to the Owner, in the form of `msg.sender`

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L461C3-L485

```solidity
    function withdrawFeesEarned() public {
        _accrue();
        address _feeTo = penrose.feeTo();
```
```solidity
      feeShares = _removeAsset(feeTo, msg.sender, balanceOf[feeTo], false); /// @audit Should transfer to feeTo
```


As you can see the second parameter `to` is set to `msg.sender` which corresponds to the `owner`, but not necessariyl to `feeTo`
```solidity
    function _removeAsset(
        address from,
        address to,
        uint256 fraction,
        bool updateYieldBoxShares
    ) 
```

###  48.1. <a name='POC-1'></a>POC
- Call `refreshPenroseFees`
- Expect funds to go to `feesTo`
- Funds go to `owner`


###  48.2. <a name='MitigationStep'></a>Mitigation Step

Transfer the fees to `feeTo`

```solidity

        feeShares = _removeAsset(feeTo, feeTo, balanceOf[feeTo], false); // TODO: Prob should transfer to feeTo

```



# L - Spearphishing vector
    /// @notice allows 'operator' to act on behalf of the sender
    /// @param status true/false
    function updateOperator(address operator, bool status) external {
        operators[msg.sender][operator] = status;
    }


Approving this means losing 100% of tokens

##  49. <a name='L-setMarketConfigboundsaretoohigh'></a>L - `setMarketConfig` bounds are too high


##  50. <a name='L-LiquidationsareenforcingSwapperswithHardcodedSlippage'></a>L - Liquidations are enforcing Swappers with Hardcoded Slippage
        require(penrose.swappers(swapper), "BigBang: Invalid swapper");

These swappers will offer substantially subpar slippage checks than more sophisticated MEV bots

##  51. <a name='L-SwappersareSingleSegment'></a>L - Swappers are Single Segment
Single Segment Swappers will not be as efficient as more flexible aggregated swaps, most MEV bots have better routing than single swap
Limiting them to a single swap will reduce arbitrage opportunities, increase their cost, and overall make the system less efficient

##  52. <a name='L-DuplicateMarketscanbeaddedbymistake'></a>L - Duplicate Markets can be added by mistake
    function addSingularity(
        address mc,
        address _contract
    ) external onlyOwner registeredSingularityMasterContract(mc) {
        isMarketRegistered[_contract] = true;
        clonesOf[mc].push(_contract);
        emit RegisterSingularity(_contract, mc);
    }


##  53. <a name='L-Theabovelooksunnecessaryitsdoneautomaticallybydeploy'></a>L - The above looks unnecessary, it's done automatically by `deploy`
https://github.com/boringcrypto/BoringSolidity/blob/78f4817d9c0d95fe9c45cd42e307ccd22cf5f4fc/contracts/BoringFactory.sol#L61C7-L62


