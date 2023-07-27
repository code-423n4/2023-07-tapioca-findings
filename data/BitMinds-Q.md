## L-01 Check for sender not being the recipient (to) in MarketERC20 transfer function can be circumvented
In https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140 the check for "msg.sender == to" should be removed. The check that the sender does not send to himself is done here: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L143.

## L-02 Typo "_computeMaxAndMinLTVInAsset" across multiple files
Should be named "_computeMaxAndMinTVLInAsset"
Check https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_computeMaxAndMinLTVInAsset&type=code

## L-03 Using _computeMaxAndMinLTVInAsset but returning variables in order min, max is error prone
Reading the _computeMaxAndMinLTVInAsset function, one could intuitively assume that max is returned first. This could lead to serious issues if mixed up. Change the function name to _computeMinAndMaxLTVInAsset, and while doing that, correct "LTV" to "TVL". See https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_computeMaxAndMinLTVInAsset&type=code for all occurrences.

## L-04 Redundant check in function _liquidateuser 
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L567
The function _closedliquidate, which calls the function _liquidateuser, already checks for user solvency
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L668

## L-05 _getMasterContractLength function in Penrose.sol should not be public
There is no reason why _getMasterContractLength should be available outside of Penrose.sol as it is only called from this contract. Also, the "_" prefix of the function name indicates that this should not be public by function naming convention. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L542

## L-06 Small code asymmetry between _claimRewards and _claimRewardsOn functions in twTAP.sol
Both functions utilize an unchecked block. _claimRewards performs the "uint256 len = ..." statement outside of the unchecked block. _claimRewardsOn performs the "uint256 len = ..." statement inside of the unchecked block. Compare https://github.com/Tapioca-DAO/tap-token/blob/main/contracts/governance/twTAP.sol#L495 and https://github.com/Tapioca-DAO/tap-token/blob/main/contracts/governance/twTAP.sol#L515. Also, _claimRewards increases the "i" variable in the for loop signature, and _claimRewardsOn does it as the last statement in the for loop. Compare https://github.com/Tapioca-DAO/tap-token/blob/main/contracts/governance/twTAP.sol#L497 and https://github.com/Tapioca-DAO/tap-token/blob/main/contracts/governance/twTAP.sol#L525. As both functions are very similar, keeping symmetry in the implementation, even on small details, increases consistency and auditability.

## L-07 Wrong from address used for LogRepay event in _repay function of SGLLendingCommon.sol
In https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L97 the skim variable is used. Considering that in SGLBorrow.sol, it is documented that skip = false should indicate Yieldbox (see https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLBorrow.sol#L42) the ternary operator used for the LogRepay event seems inverted.

## L-08 Using wrong constant (name) in _isSolvent function of Market.sol
The Tapioca documentation says that the borrowing/lending logic was licensed from Kaski. When comparing the _isSolvent function in Market.sol with the respective implementation of Kashi, the used FEE_PRECISION constant should actually be COLLATERIZATION_RATE_PRECISION to be semantically correct. Both constants have the same value of ie5, so it is not a problem in calculations but it is nevertheless confusing. Compare https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L393 with _isSolvent implementation here: https://etherscan.io/address/0x009e9cFaD18132D9fB258984196191BdB6D58CFF#code (line 954).

## L-09 Setting owner directly in constructor of Penrose.sol (and other contracts) circumvents zero address check and emitting related event
This may have been done to save gas, but a zero address could be relevant. Also, no event will be emitted that indicates the new contract owner. See this as an example: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L94 but search the project globally to find all occurrences. BoringOwnable.sol itself emits this event: https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/BoringOwnable.sol#L18. So for consistency reasons of always logging ownership changes, consider using "transferOwnership" with the direct parameter set to true instead of setting the owner state variable directly: https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/BoringOwnable.sol#L26.

## L-10 _executeViewModule function of Singularity.sol is unused
When searching for the _executeViewModule function being used across the project (https://github.com/search?q=org%3ATapioca-DAO%20_executeViewModule&type=code) no call showed up. It may be removed from the project.

## L-11 _getAmountForBorrowPart function of SGLCommon.sol is unused
The _getAmountForBorrowPart function implemented in SGLCommon.sol (https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L254) is unused in the project and can be removed. Check: https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_getAmountForBorrowPart&type=code.

## L-12 amount parameter of addCollateral function in SGLCollateral.sol is undocumented
The "amount" parameter is missing in the Natspec of the addCollateral function. See: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCollateral.sol#L15-L26. It should be added to the Natspec.

## L-13 allowedBorrow function in MarketERC20.sol has the side effect of decreasing borrow allowance
Just looking at the function name "allowedBorrow" it could be assumed that this is only a check and not a state-altering function. But it also decreases the borrow allowance: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L89.  The function should best be renamed to also indicate that the borrow allowance is decreased.

## L-14 BigBang.sol error messages still contain "SGL" in error messages
It looks like some leftovers of developing BigBang as a version of Singularity remained in error messages of require statements in BigBang.sol. Here is 1 example: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L344. All occurrences in the file should be corrected to indicate that the error came from BigBang.

## L-15 SGLCommon.sol and SGLLendingCommon.sol contain a hardcoded and undocumented check for _totalAsset.base >= 1000
It is unclear why the check is against a value of 1000 and why the value is hardcoded. Instead a descriptive variable should be used which is set to 1000. Appears https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L75 and here https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L240

## L-16 minimumTargetUtilization and maximumTargetUtilization are not checked against each other in setSingularityConfig function of Singularity.sol
It is not checked that maximumTargetUtilization > minimumTargetUtilization. Other min/max variable pairs are checked against each other:
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L523, https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L535. It necessary, the missing check should be added.

## L-17 Different checks against FEE_PRECISION for _lqCollateralizationRate and _liquidationMultiplier in Singularity.sol
For the _lqCollateralizationRate a "lower than or equal" check is done: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L555. For the check of _liquidationMultiplier against the same FEE_PRECISION constant only a "lower than" check is done: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L566. If both checks should be done with the same operator, it should be corrected.

## L-18 LidoEThStrategy.sol, CompoundStrategy.sol, and YearnStrategy.sol call an empty implementation of compound()
Both contracts only have an empty implementation of the "compound" function. But both call the function in their "emergencyWithdraw" function: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L102, https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L101, https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L105. The calls to the compound function can be removed.

## L-19 Inconsistency of using correct variable naming for shares and amounts across the protocol
The protocol utilizes "rebasing" via the "BoringRebase" library by BoringCrypto (https://github.com/boringcrypto/BoringSolidity). In various places of the TapiocaDAO protocol, it is unclear whether a value is represented as a "share" or as an "amount". One of the places is https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L289. The amount here is actually a share. Consistency in naming variables to indicate whether they are a value or share would greatly remove ambiguity and the risk of error.

## L-20 BalancerStrategy.sol uses hardcoded integer values instead of using more descriptive enums for joining/exiting pool argument
Balancer offers descriptive enums in the context of joining/exiting pools: https://github.com/balancer/balancer-v2-monorepo/blob/e16fad44b29199778b0104f3bf6d402b16cc1ea9/pkg/interfaces/contracts/pool-stable/StablePoolUserData.sol#L18-L19. In BalancerStrategy.sol these values are hardcoded without any comment what the respective integer means. Examples for joining a pool: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L169. Example for exiting a pool: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L288. At least leave a comment to explain the used integers. Alternatively, use descriptive enums.

## L-21 Withdrawing from lending pool in AaveStratgy during emergency withdrawal should use max integer value instead user balance
The Strategy first determines the available user balance and then withdraws the balance: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L211-L216. As documented by Aave itself documents to "Use type(uint).max to withdraw the entire balance" (see: https://docs.aave.com/developers/v/2.0/the-core-protocol/lendingpool). The dode should be updated to follow the Aave docs.

## L-22 BalancerStrategy.sol contains an unused state variable and related event
The "rewardTokens" state variable (https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L41) and the "RewardTokens" event (https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L52) are unused and can be removed from the strategy.

## L-23 Strategies contain comments containing the name of other strategies
E.g., BalancerStrategy.sol refers to the Yearn strategy in multiple places: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L44, https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L190. This also appears in other strategies. All strategies should be cleaned up to not refer to other strategies.

## L-24 Inconsistency between Singularity strategies regarding emitted events on asset deposits which may distort off-chain accounting
On deposits, there are two events that may be issued across the strategies: AmountQueued and AmountDeposited. AaveStrategy.sol, for example, does not emit the AmountQueued event for the amount passed to the deposit function if the threshold is reached that assets are deposited into the lending pool: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L234-L247. In comparison BalancerStrategy.sol does always emit an AmountQueued and AmountDeposited event irrespective whether the threshold was met or not: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L138-L147. This is inconsistent and may lead to distortion in off-chain accounting. This should be corrected so that all strategies follow the same pattern of emitting events for deposits.

## L-25 Typo in ConvexTricryptoStrategy.sol
It should be "successEmptyApproval" instead of "successEmtptyApproval": https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L356, https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L364

## L-26 Typo in Market.sol
It should be "maxBorrowable" instead of "maxBorrowabe": https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L470, https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L474

## L-27 Typo in ConvexTricryptoStrategy.sol
It should be "_rewardPool" instead of "_rewadPool": https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L87, https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L99