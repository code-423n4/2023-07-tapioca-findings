# [01] TapiocaOptionLiquidityProvision.sol doesn't fully support ERC1155
According to [comments on L356](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L356C17-L371), the contract is supposed to support ERC1155.
Quoting from [eip-1155](https://eips.ethereum.org/EIPS/eip-1155): **Smart contracts MUST implement all of the functions in the ERC1155TokenReceiver interface to accept transfers.**
To fully support ERC1155, the contract should also implement `function onERC1155BatchReceived`

# [02] BaseTapOFT._lockTwTapPosition should revoke allowance when `twTap.participate` fails
Function [BaseTapOFT._lockTwTapPosition](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L125C14-L149) first approves `twTap` to spend `amount` of token, then the function calls [twTap.participate](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L138C13-L140). If `twTap.participate` fails, the function will first emit a event, then transfers the tokens. 
**But the function forgets to revoke the allowance approved to twTap** in [Line135](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L135C25-L135C30)

# [03] missing updating `_yieldBoxShares` in function SGLLendingCommon._borrow
While calling [SGLLendingCommon._addCollateral](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L16-L37) and [SGLLendingCommon._repay](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L83-L97), `_yieldBoxShares` is updated in [function _addTokens](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L174C19-L194), and in [SGLLendingCommon._removeCollateral](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L41-L55), `_yieldBoxShares` is also got updated. 
It seems that only `SGLLendingCommon._borrow` doesn't update `_yieldBoxShares`. So I'm guessing it's better to update it too

# [04] totalBorrow.sub in SGLLendingCommon._repay should roundDown instead roundUp
While handling roundDown/roundUp issue in borrow/repay system, I think it's better to make the tiny profit toward to the protocol to make the system more robust.
With above idea in mind, I think it's better to change `true` to `false` in [totalBorrow.sub](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L89C33-L89C48) in [SGLLendingCommon._repay](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L83C14-L98)
Which also comply with [totalBorrow.add](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L65C31-L65C46) in [SGLLendingCommon._borrow](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L58C14-L80)

# [05] missing calling `function compound`
File
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L120-L125
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L191-L216
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L136-L162
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L136-L162
```
## details
Althouth the function `compound` is empty function, but I think it's better to call the function to in accordance with other contract, besides if `function compound` will be implemented in furture, `_withdraw` and `emergencyWithdraw` will work without modifying

# [06] functions will always revert
File:
```
    https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L180-L185
    https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L178-L186
    https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L187-L195
    https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L195-L200
    https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L170-L174
    https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L202-L212
    https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L163-L168
```

There are some failed case when `safeTransfer` is called, but after the call, the function will call `revert`, which makes the `safeTransfer` useless
For example [USDOLeverageModule.leverageUp](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L133-L188), while the function fails on [delegatecall](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L169C54-L178), [L181](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L181-L184) will be executed, within this [if branch](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L181-L184), the code will **revert** in the end, so it make no scense to call `safeTransfer`.
Maybe the patch should be:

# [07] Division Before Multiplication
File:
    https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L438-L439
```solidity
        max = (collateralAmount * EXCHANGE_RATE_PRECISION) / _exchangeRate;
        min = (max * collateralizationRate) / FEE_PRECISION;
```

# [08] missing check ECDSA.recover's return address is zero
File:
    https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L277
```solidity
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "ERC20Permit: invalid signature");
```
File:
    https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L57-L58
```solidity
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "YieldBoxPermit: invalid signature");
```
File:
    https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L86-L87
```solidity
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "YieldBoxPermit: invalid signature");
```

# [09] MarketERC20.transfer wrong logic which causes more gas fee
Although the implementation in [MarketERC20.transfer](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135C14-L152)
```diff
--- MarketERC20.sol     2023-08-03 19:11:36.562629796 +0800
+++ MarketERC20_New.sol 2023-08-03 19:12:12.299228034 +0800
@@ -137,7 +137,7 @@
         uint256 amount
     ) public virtual returns (bool) {
         // If `amount` is 0, or `msg.sender` is `to` nothing happens
-        if (amount != 0 || msg.sender == to) {
+        if (amount != 0 && msg.sender != to) {
             uint256 srcBalance = balanceOf[msg.sender];
             require(srcBalance >= amount, "ERC20: balance too low");
             if (msg.sender != to) {
```