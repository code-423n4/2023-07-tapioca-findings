Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot’s contents, replace the bits taken up by the boolean, and then writeback.

There are hundreds of instances where booleans can be replaced with private constants like this:
    uint256 private constant _NOT_WHITELISTED_ADDRESS = 1;
    uint256 private constant _WHITELISTED_ADDRESS = 2;

1 for false and 2 for true, using 1 and 2 not zero to save more gas. 
This scenario can be can be used in hundreds of instances in the code and millions of gas can be saved,
I'm dropping few instances in Singularity market file where bool can be replaced with private constant uint256 as shown above.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L183

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L238
Skim is used multiple times in different functions dropping for just one instance

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L622

any further execution where the ‘State’ of the mapping changes is going to be almost 20_000 gas cheaper.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L46

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L61

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L63

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L65

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L71

There are obviously many more instances where this can be implemented and millions of gas can be saved.
I have attached these permalinks from manual review so can't attach all of them.

