## Title
extra gas fee to withdraw from curve gauges

## Decription
When user call TricryptoNativeStrategy.sol._withdraw, if amount is more than queued, compound("") function get called and then the lpGauge.withdraw(lpBalance, true) get called.

compound :
This function takes the existing reward, then converts it to WETH with the swapper, and then adds as liquidity and stake again.
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L151

lpGauge.withdraw(lpBalance, true) : withdraw all balance in the lpGauge with all available rewards.

The user pays an additional gas fee to call the following functions:
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L152
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L154
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L155
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L175

In the _withdraw function, after lpGauge.withdraw(lpBalance, true), use rewardToken.balanceOf(address(this)) to get available reward token balance. then swap available reward token balance to weth. By using this, additional costs will be eliminated.

The most important thing is that you can use lpGetter.calcWethToLp(amount) and withdraw only the required amount of LPToken. so you don't need to call below code :
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L240C1-L244C42

## Links
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L216
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L182