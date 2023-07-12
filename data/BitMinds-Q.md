## L-1 Check for sender not being the recipient (to) in MarketERC20 transfer function can be circumvented
In https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140 the check for "msg.sender == to" should be removed. The check that the sender does not send to himself is done here: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L143.

## L-2 Typo "_computeMaxAndMinLTVInAsset" across multiple files
Should be named "_computeMaxAndMinTVLInAsset"
Check https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_computeMaxAndMinLTVInAsset&type=code

## L-3 Using _computeMaxAndMinLTVInAsset but returning variables in order min, max is error prone
Reading the _computeMaxAndMinLTVInAsset function, one could intuitively assume that max is returned first. This could lead to serious issues if mixed up. Change the function name to _computeMinAndMaxLTVInAsset, and while doing that, correct "LTV" to "TVL". See https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_computeMaxAndMinLTVInAsset&type=code for all occurrences.

## L-4 Redundant check in function _liquidateuser 
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L567
The function _closedliquidate, which calls the function _liquidateuser, already checks for user solvency
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L668

## L-05 _getMasterContractLength function in Penrose.sol should not be public
There is no reason why _getMasterContractLength should be available outside of Penrose.sol as it is only called from this contract. Also, the "_" prefix of the function name indicates that this should not be public by function naming convention. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L542
