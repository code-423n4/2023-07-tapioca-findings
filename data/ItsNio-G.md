## Gas 1
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L730C9-L731C27

The following lines of calculation are not needed. Replace `toWithdraw` with `part`, or simply just use `part`.

## Gas 2
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L353
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L373

`_borrow()` on line 353 is called before `_allowedBorrow()` is called on 373. Move the `_allowedBorrow()` before the `_borrow() call` so that gas will not be wasted on potential swapping that will be reverted.