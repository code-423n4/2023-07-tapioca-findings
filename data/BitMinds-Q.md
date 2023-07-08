## L-1 Check for sender not being the recipient (to) in MarketERC20 transfer function can be circumvented
In https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140 the check for "msg.sender == to" should be removed. The check that the sender does not send to himself is done here: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L143.

## L-2 Typo "_computeMaxAndMinLTVInAsset" across multiple files
Should be named "_computeMaxAndMinTVLInAsset"
Check https://github.com/search?q=repo%3ATapioca-DAO%2Ftapioca-bar-audit%20_computeMaxAndMinLTVInAsset&type=code