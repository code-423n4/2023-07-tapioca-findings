QA1. MarketERC20.transfer() uses the wrong condition at L140. The correct condition should be ``amount != 0 && msg.sender != to``. In addition, the emit statement should be in the if-block so that false event will not be emitted. 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135-L152](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135-L152)

Correction:
 
```diff
 function transfer(
        address to,
        uint256 amount
    ) public virtual returns (bool) {
        // If `amount` is 0, or `msg.sender` is `to` nothing happens
-        if (amount != 0 || msg.sender == to) {
+        if (amount != 0 && msg.sender != to) {

            uint256 srcBalance = balanceOf[msg.sender];
            require(srcBalance >= amount, "ERC20: balance too low");
-            if (msg.sender != to) {
                require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas

                balanceOf[msg.sender] = srcBalance - amount; // Underflow is checked
                balanceOf[to] += amount;
-            }
+           emit Transfer(msg.sender, to, amount);
        }
-        emit Transfer(msg.sender, to, amount);
        return true;
    }
```

QA2. ``Market.setMarketConfig()`` will fail to set a new ``[_minLiquidatorReward, _maxLiquidatorReward]`` when such new range has no overlap with the default range ``[1e3, 1e4]``. By default, we have ``[minLiquidatorReward, maxLiquidatorReward]`` = ``[1e3, 1e4]``. However, if the new range has NO overlap with the existing default range, then ``Market.setMarketConfig()`` will always fail. For example, one cannot set a new range of ``[1e2, 1e3]`` or a new range of ``[1e4, 1.1e4]`` since they have no overlap with ``[1e3, 1e4]``. This is because we compare ``_minLiquidatorReward < maxLiquidatorReward`` not ``_minLiquidatorReward < _maxLiquidatorReward`` below: 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L212C12-L215](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L212C12-L215)

Similarly, we compare ``_maxLiquidatorReward > minLiquidatorReward`` instead of ``_maxLiquidatorReward > _minLiquidatorReward`` below: 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L221-L224](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L221-L224)

The correction should be only compare the new ``_minLiquidatorReward`` with the new ``_maxLiquidatorReward`` and to make sure that ``_minLiquidatorReward < _maxLiquidatorReward``.


QA3. The contructor of LzApp does not specify an argument for the parent contract ownable: 

[https://github.com/Tapioca-DAO/tapioca-sdk-audit/blob/90d1e8a16ebe278e86720bc9b69596f74320e749/src/contracts/lzApp/LzApp.sol#L31-L33](https://github.com/Tapioca-DAO/tapioca-sdk-audit/blob/90d1e8a16ebe278e86720bc9b69596f74320e749/src/contracts/lzApp/LzApp.sol#L31-L33)

Correction: use msg.sender is the default owner for the contract, and pass ``msg.sender`` to ownerable: 

```diff
- constructor(address _endpoint) {
+ constructor(address _endpoint) Ownable(msg.sender){
        lzEndpoint = ILayerZeroEndpoint(_endpoint);
    }
```

QA4. BalancerStrategy.updateCache() tries to search for ``index`` as the index of the ``wrappedNative`` in the array of ``poolTokens``. However, it does not differentiate the case of zero index (the first token) and the case that ``wrappedNative`` cannot be found in the array of ``poolTokens``, which also results in index = 0 (by default). 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L271-L299](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L271-L299)

The correction would be to initialize index = -1 so that the two cases can be differentiated. 

QA5. BalancerStrategy._vaultWithdraw() initialize ``index`` to be -1 so that the function can differentiate the two cases: 1) index = 0; 2) the case that ``wrappedNative`` cannot be found in the array of ``poolTokens``. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L218-L269](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L218-L269)

Unfortunately, the logic of the function does not deal with the case of index = -1 after the for-loop. The function should for example, does an explicit revert when index = -1. 

QA6. ``ConvexTricryptoStrategy.emergencyWithdraw()`` calculates the wrong ``minAmount``. As a result, the slippage control is not effective. The calculated ``minAmount`` is too small and less tokens might be withdrawn than expected. Similar problem occurs for ``LidoEthStrategy.emergencyWithdraw()``. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L148-L156](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L148-L156)

The correction would be as follows.

```diff
 function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 lpBalance = rewardPool.balanceOf(address(this));
        rewardPool.withdrawAndUnwrap(lpBalance, false);
        uint256 calcWithdraw = lpGetter.calcLpToWeth(lpBalance);
-        uint256 minAmount = (calcWithdraw * 50) / 10_000; //0.5%
+        uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%

        result = lpGetter.removeLiquidityWeth(lpBalance, minAmount);
    }
```

QA7. CompoundStrategy.emergencyWithdraw() calculates the wrong value for ``result``, which will always be zero. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L100-L108](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L100-L108)

The correction would be the last two statements should be exchanged. The result should be the balance of the native tokens before the deposit occurs.

```diff
function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 toWithdraw = cToken.balanceOf(address(this));
        cToken.redeem(toWithdraw);
+       result = address(this).balance;
        INative(address(wrappedNative)).deposit{value: address(this).balance}();

-        result = address(this).balance;
    }
```

QA8. CompoundStrategy._withdraw() uses rounding down to calculate ``toWithdraw``, as a result, the number of shares redeemed might be less than expected, and as a result, ``_withdraw()`` will likely fail due to insufficient balance at L156 (fails the check here). similar problem occurs for YearnStrategy._withdraw().

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L136C14-L162](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L136C14-L162)

There are two cases: 1) there is sufficient balance of wrappedNative tokens for withdrawal; 2) there is not sufficient balance of wrappedNative tokens for withdrawal. For the later case, some shares need to be redeemed from ctoken. The number of shares is calculated as follows:

```javascript
         uint256 toWithdraw = (((amount - queued) * (10 ** 18)) /
                pricePerShare);
```

It uses rounding down. As a result, even after the redeeming, there still not be sufficient balance of wrappedNative tokens to cover ``amount``. The correction should be using a rounding up instead.

QA9. TricryptoNativeStrategy.compound() calls _addLiquidityAndStake(queued) without checking ``queued > depositThreshold``. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L151-L179](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L151-L179)

This is inconsistent with the logic in _deposited(), which only performs _addLiquidityAndStake(queued) when ``queued > depositThreshold``. 

QA10. The logic of depositing only when  ``queued > depositThreshold`` is not implemented in 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L138-L206](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L138-L206)

Although at L196, it says "//stake if > depositThreshold``, such logic is not implemented in the function. 

QA11. The AaveStrategy._withdraw() might withdraw more tokens than the specified ``amount`` in the argument. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L250-L275](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L250-L275)

This is because at L269, ``amount`` might be incremented by some amount that depends on the the outcome of lendingPool.withdraw(). 

Correction: do not change the value of argument ``amount``.

QA12: ConvexTricryptoStrategy._deposited() might emit false  event AmountDeposited(queued). 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L300-L308](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L300-L308)

This is because it calls _addLiquidityAndStake(queued) but deposit only occurs when ``calcAmount >= 1e18``:

```javascript
 function _addLiquidityAndStake(uint256 amount) private {
        uint256 calcAmount = lpGetter.calcWethToLp(amount);
        if (calcAmount >= 1e18) {
            uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
            uint256 lpAmount = lpGetter.addLiquidityWeth(amount, minAmount);
            booster.deposit(pid, lpAmount, true);
        }
    }
```
To mitigate this, one needs to put the emit statement in the if-block of ``_addLiquidityAndStake()``. 

QA13. CompoundStrategy._deposited() is favor of small deposit amount over large deposit amount when ctoken is frozen. In the history, ceth was frozen for one week (https://beincrypto.com/bug-compound-finance-freeze-ceth-market-week/). 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L123-L133](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L123-L133)

When the underlying ctoken contract is frozen, small deposit might go through as long as queued <= depositThreshold, but large deposit will fail due to the failure of mint. 

Correction:  CompoundStrategy._deposited() can be reimplemented so that it will proceed regardless of the status of the underlying ctoken. When ctoken is not working, the deposit can be saved as wrappedNative tokens. 

