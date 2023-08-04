1. emit wrong data
amountOut & shareOut never be assigned
https://github.com/Tapioca-DAO/tapioca-sdk-audit/blob/90d1e8a16ebe278e86720bc9b69596f74320e749/src/contracts/YieldBox/contracts/YieldBox.sol#L297
2. not support lm
compound have lm reward strategy need to have the function to withdraw lm reward.
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol