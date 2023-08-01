## Title
Singularity Elastic Interest Rates target range

## Decription
From document :
-*Singularity also utilizes Kashi's Elastic Interest Rates. Current lending protocols try to achieve ideal interest rates by making the interest rate go up with increased utilization on a predefined curve. The minimum and maximum rates are however fixed. ***Singularity's elastic interest rate is a means of incentivizing liquidity to hover within an ideal range (70 - 80%)***. The elastic interest rate optimizes for utilization (borrowed / lent). The elastic interest rate is not linear. Rather, the closer utilization gets to the extremes, the faster it goes.*

-*Comparing utilization rates of assets lent to AAVE, dYdX, and Compound, while achieving desirable utilization of the top stablecoins, assets with non-fixed prices utilization rates are quite low, especially notable in the case of ETH being below 20% utilization. ***Singularity removes this inefficiency with its Elastic Interest Rates targeting a 70-80% utilization rate***. If utilization was low, like in the graph above, interest rates would decline on ETH until the optimal utilization of liquidity was achieved. This achieves better capital efficiency, which benefits both lenders and the overall Tapioca ecosystem.*

https://docs.tapioca.xyz/tapioca/core-technologies/singularity#elastic-interest-rates

According to the document provided by the team, Singularity want to use its Elastic Interest Rates, range 70% - 80% utilization rate, in order to better capital efficiency, which benefits both lenders and the overall Tapioca ecosystem.

But in the Singularity.sol#L139 and Singularity.sol#L139, minimumTargetUtilization and maximumTargetUtilization values are hardcoded to values 30% and 50%.

If Singularity ideal range is 70%-80%, so change minimumTargetUtilization and maximumTargetUtilization values to 70%-80% And then, change the values by timelock contract.

## Links
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L139
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L140

## Title
Singularity.sol#L114.interestElasticity 

## Decription
The Singularity.sol#L114.interestElasticity is :

        interestElasticity = 7200e36; // Half or double in 28800 seconds (1 hours) if linear

The problem is that 7200 is 2 hours, 28800 is 8 hours and 1 hours is 3600. Please specify the value.

## Links
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L114

## Title
Invalid accrueInfo.interestPerSecond value

## Decription
The accrueInfo.interestPerSecond, according to the comment, should set to 1% APR, but the value is set to the minimumInterestPerSecond = 951293760 = 3% APR.

## Links
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L117
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L115
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L112

## Title
Invalid value for claimable rewards from Convex rewardPool.sol

## Decription
Based on the document from Convex, to get value of Current Earned Rewards from pool, users should use baseRewardPool.earned(address) to see how much rewards will receive if they claim their rewards now.https://github.com/convex-eth/platform/blob/a5da3f127a321467a97a684c57970d2586520172/contracts/contracts/BaseRewardPool.sol#L149

But ConvexTricryptoStrategy.sol is using rewards mapping.
https://github.com/convex-eth/platform/blob/a5da3f127a321467a97a684c57970d2586520172/contracts/contracts/BaseRewardPool.sol#L73

## Links
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L131

## Title
BalancerStrategy.sol, use payable(address(this)) instead of payable(this)

## Decription
receive() external payable {}, only exists because the exitPool() function of the balancer vaults require an address payable as argument, and the current BalancerStrategy.sol contract uses the payable(this) to retrieve the payable address.
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L257

Casting the contract to an address first, and then to a payable address (i.e. payable(address(this))) would allow the removal of this function, receive() external payable {}.

## Links
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L257
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L301C4-L301C34