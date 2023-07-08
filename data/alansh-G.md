https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L446

Here `totalFees` doesn't need to be a storage variable at all, as it's only updated inside the `refreshPenroseFees` function, which resets `totalFees` to zero instantly after `depositAsset` if `totalFees` is positive. So from the outside, `totalFees` will always be zero.

(being continuously updated)