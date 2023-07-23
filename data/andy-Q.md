
## `_executeViewModule`: not working

[_executeViewModule](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L631) not working(and not used).

The `Singularity` contract is split in modules, unlike `delegatecall`, the `staticcall` will be reading the modules storage context(instead of `Singularity`). The module storage is not properly initilized. Hence, the result will be not correct.

```solidity
function _executeViewModule(
    Module _module,
    bytes memory _data
) private view returns (bytes memory returnData) {
    bool success = true;
    address module = _extractModule(_module);

    (success, returnData) = module.staticcall(_data);
    if (!success) {
        revert(_getRevertMsg(returnData));
    }
}
```

`_executeViewModule` is not being used in this project. Hence, suggest to remove it.


