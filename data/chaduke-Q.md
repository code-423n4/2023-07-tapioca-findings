QA1. MarketERC20.transfer() uses the wrong condition at L140. The correct condition should be ``amount != 0 && msg.sender != to``. In addition, the emit statement should be in the if-block so that false event will not be emitted.  The emit statement for MarketERC20.transferFrom should also be moved into the block of `` if (from != to)`` to avoid false emit. 

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

QA14. TricryptoNativeStrategy._addLiquidityAndStake() does not check and ensure that lpAmount !=0. As a result for small amount of _addLiquidityAndStake(amount), it is possible that lpAmount = 0, which means zero lpTokens will be minted while ``amount`` of wrappedNative tokens are deposited, leading to loss of funds. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L247C14-L252](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L247C14-L252)

Mitigation: Add a require statement to ensure that ``lpAmount !=0``. 

QA15. TricryptoLPStrategy._currentBalance() fails to include the balance of wrappedNative tokens in the calculation of the current balance. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L210-L215](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L210-L215)

Correction: includes the balance of wrappedNative tokens in the calculation of the current balance. 
 
QA16. BalancerStrategy.emergencyWithdraw() decreased the value of ``toWithdraw`` by 5%, as a result, some funds will be left in the vault. This is not necessary.

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L120-L125](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L120-L125)

correction: No need to decrease ``toWithdraw`` so that all funds can be withdrawn from the vault.

```diff
function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        uint256 toWithdraw = updateCache();
-        toWithdraw = toWithdraw - (toWithdraw * 50) / 10_000; //0.5%

        result = _vaultWithdraw(toWithdraw);
    }
```

QA17. GlpStrategy.setFeeRecipient() lacks a zero address check for the new feeRecipient address. 

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L113-L115](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L113-L115)

If the zero address is entered maliciously or by mistake, then fees will be lost to the zero address by 
withdrawFees():

[https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L117C14-L127](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L117C14-L127)

QA18. BaseUSDOStorage._getRevertMsg() uses assembly to decode the revert message. 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L87C22-L98](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L87C22-L98)

This can be accomplished by the lastest solidity functionality, see example here. 

[https://github.com/code-423n4/2021-05-yield-findings/issues/15](https://github.com/code-423n4/2021-05-yield-findings/issues/15)

QA19. NativeTokenFactory.createToken(), when calling the _registerAsset(), the second argument should be ``address(this)`` instead of ``address(0)``. 

[https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L90-L102](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L90-L102)

QA20. An attacker can steal funds from another user by front-running ``NativeTokenFactory.createToken()``.

[https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L90C14-L102](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L90C14-L102)

The following scenario explains how this can happen: 
1) Alice calls ``createToken("Alice", "ALI", 18, "alice.html")`` to create a token ID.
2) Bob front-runs Alice's ``createToken()`` with another call ``createToken("Alice", "ALI", 18, "alice.html")``, and return a token ID 222.
3) Bob calls ``mint(222, Bob, 1000e18)`` to mint 1000e18 amount of token 222 to himself. 
4) Bob calls  ``transferOwnership(222, Alice, true, false)`` to transfer the ownership to Alice.
5) Alice owns 222 and is tricked to think that she owns it in the first place, but Bob already stole 1000e18 from this token.

Mitigation: store all the names of tokens and symbols to make sure that names and symbols cannot have duplicates. 

QA21. The ``notPaused`` modifier for Market.setBorrowCap() can be bypassed by calling Market.setMarketConfig() which has no modifier of ``notPaused`` but also allows the owner to set a new borrowCap. 

Correction: add ``noPaused`` to ``Market.setMarketConfig()``. 

QA22. Market._getCallerReward() set callerReward to zero instead of ``maxLiquidatorReward`` when ``borrowed < startTVLInAsset``.  

We know in the case of ``borrowed = startTVLInAsset``, we have ``rewardPercentage = 0`` and ``reward = maxLiquidatorReward``. To keep the continuity of the function, we should have ``reward = maxLiquidatorReward`` when ``borrowed < startTVLInAsset`` as well. 

QA23. BigBang.SetBigBangConfig() fails to check and make sure that ``_debtRateAgainstEthMarket <= 1e18``.

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L180-L201](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L180-L201)

Correction: add the check to make sure ``_debtRateAgainstEthMarket <= 1e18``.

QA24. Market._computeMaxBorrowableAmount() has a division-before-multiply rounding error. As a result, the maxBorrowableAmount might not calculated accurately and a user might borrow more than allowed. There is a similar problem for function Market._isSolvent().

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L385-L398](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L385-L398)

Correction:
```diff
 function _computeMaxBorrowableAmount(
        address user,
        uint256 _exchangeRate
    ) internal view returns (uint256 collateralAmountInAsset) {
        collateralAmountInAsset =
            yieldBox.toAmount(
                collateralId,
                (userCollateralShare[user] *
-                    (EXCHANGE_RATE_PRECISION / FEE_PRECISION) *
-                    collateralizationRate),
+              EXCHANGE_RATE_PRECISION  * collateralizationRate / FEE_PRECISION) 
                false
            ) /
            _exchangeRate;
    }
```

QA25. SGLLendingCommon._borrow() checks and make sure that ``totalBorrow.base <= totalBorrowCap`` but BigBang._borrow() checks and make sures that ``totalBorrow.elastic <= totalBorrowCap``. Therefore, the check for ``totalBorrowCap`` is not consistent. 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L58-L80](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L58-L80)

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L742-L767](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L742-L767)

Correction: make them consistent in terms of how to check ``totalBorrowCap``. 

QA26. Penrose.setFeeTo() fails to perform a zero address check for the input ``feeTo_``. As a result, protocol fee might be lost if ``feeTo_`` is set to zero by mistake or maliciously. 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L455C14-L458](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L455C14-L458)

Correction: perform a zero address check for ``feeTo_``. 

QA26. YieldBox.depositETH() fails to have a zero address check for ``strategy``. As a result, if ``strat3egy`` is set to zero by accident or maliciously, then funds will be lost to the zero address.

[https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L500-L502C1](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L500-L502C1)

This is because none of the callee functions, ``registerAsset()`` and ``_registerAsset()`` perform any zero address check and funds will be deposited to the zero address when  ``strategy`` is a zero address.

QA27. Balancer.rebalance() has the wrong logic to check the msg.value. As a result, the function will not work properly. For example, when msg.value == _amount, the function will revert, which is not supposed to be for the case of ``_isNative == true``. Meanwhile, when ``msg.value  == 0`` for the case of   ``_isNative ~= true``, it will revert as well. 

[https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L172-L199C1](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L172-L199C1)

The correction is as follows:

```diff
 function rebalance(
        address payable _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bytes memory _ercData
    )
        external
        payable
        onlyOwner
        onlyValidDestination(_srcOft, _dstChainId)
        onlyValidSlippage(_slippage)
    {
        if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
            revert RebalanceAmountNotSet();

        //check if OFT is still valid
        if (
            !_isValidOft(
                _srcOft,
                connectedOFTs[_srcOft][_dstChainId].dstOft,
                _dstChainId
            )
        ) revert DestinationOftNotValid();

        //extract
        ITapiocaOFT(_srcOft).extractUnderlying(_amount);

        //send
        bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
        if (_isNative) {
-            if (msg.value <= _amount) revert FeeAmountNotSet();
+            if (msg.value != _amount) revert FeeAmountNotSet();

            _sendNative(_srcOft, _amount, _dstChainId, _slippage);
        } else {
-            if (msg.value == 0) revert FeeAmountNotSet();
+            if (msg.value != 0) revert FeeAmountNotSet();

            _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
        }

        connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
        emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);
    }
```

QA28. Vesting.claim() fails to check the ``data.revoked``. As a result, a user can claim tokens even they have already been revoked. 

[https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L110-L121](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L110-L121)

Correction: check ``data.revoked`` and if it is true, the function should not allow the user to claim tokens. 

QA29. TapiocaOptionLiquidityProvision.registerSingularity() fails to check whether ``assetID`` has been registered or not.

[https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L276-L293](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L276-L293)

Other functions such as TapiocaOptionLiquidityProvision.unregisterSingularity() assumes that there is no duplicate of ``assetID`` in ``singularities``. It is important to ensure that TapiocaOptionLiquidityProvision.registerSingularity() would check whether ``assetID`` has been registered or not.


