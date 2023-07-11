https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L446

Here `totalFees` doesn't need to be a storage variable at all, as it's only updated inside the `refreshPenroseFees` function, which resets `totalFees` to zero instantly after `depositAsset` if `totalFees` is positive. So from the outside, `totalFees` will always be zero.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L731

Here the calculation can be simplified as `uint256 toBurn = part;`

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L254

The `_getAmountForBorrowPart` function is not used and should be deleted.

(being continuously updated)