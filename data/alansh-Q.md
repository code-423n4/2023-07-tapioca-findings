https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L480

Here the code to slice the 4-byte-sighash is wrong, because the first 32 bytes actually stores the length of the bytes. For the correct way to do slicing, refer to https://github.com/zhiqiangxu/crosschain/blob/7b706a1a8c1459378bee26bd243d420f95100002/src/libs/Utils.sol#L54 . The `_getRevertMsg` function is copied in several other places, please fix them accordingly by searching `_getRevertMsg`.

(being continuously updated)