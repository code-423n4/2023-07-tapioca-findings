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