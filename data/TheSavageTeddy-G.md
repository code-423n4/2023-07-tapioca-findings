### [G-01] Use constants instead of `type(uintx).max`

Using `type(uintx).max` such as `type(uint128).max` incurs more gas in distribution and for each transaction. Constants (such as `340282366920938463463374607431768211455` for `type(uint128).max`) should be used instead.

*There are 35 instances of this issue:*
```solidity
File: tap-token-audit/contracts/governance/twTAP.sol
  313         require(expiry < type(uint56).max, "twTAP: too long");
```
```solidity
File: tapioca-bar-audit/contracts/markets/MarketERC20.sol
  172                 if (spenderAllowance != type(uint256).max) {
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
  75         wrappedNative.approve(_lendingPool, type(uint256).max);
  76         rewardToken.approve(_multiSwapper, type(uint256).max);
  150                 type(uint256).max,
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
  77         wrappedNative.approve(_vault, type(uint256).max);
  78         IERC20(address(pool)).approve(_vault, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
  58         wrappedNative.approve(_cToken, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
  105         wrappedNative.approve(_lpGetter, type(uint256).max);
  106         lpToken.approve(_lpGetter, type(uint256).max);
  107         lpToken.approve(_booster, type(uint256).max);
  108         rewardToken.approve(_multiSwapper, type(uint256).max);
  174         rewardToken.approve(_swapper, type(uint256).max);
  183         wrappedNative.approve(_lpGetter, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
  79         lpToken.approve(_lpGauge, type(uint256).max);
  80         lpToken.approve(_lpGetter, type(uint256).max);
  81         rewardToken.approve(_multiSwapper, type(uint256).max);
  82         wrappedNative.approve(_lpGetter, type(uint256).max);
  144         rewardToken.approve(_swapper, type(uint256).max);
  154         wrappedNative.approve(_lpGetter, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
  78         IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);
  79         IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);
  80         rewardToken.approve(_multiSwapper, type(uint256).max);
  81         wrappedNative.approve(_lpGetter, type(uint256).max);
  135         rewardToken.approve(_swapper, type(uint256).max);
  145         wrappedNative.approve(_lpGetter, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
  62         IERC20(_stEth).approve(_curvePool, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
  89         stgNative.approve(_lpStaking, type(uint256).max);
  90         stgNative.approve(address(router), type(uint256).max);
  94         stgTokenReward.approve(_swapper, type(uint256).max);
  153         stgTokenReward.approve(_swapper, type(uint256).max);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
  59         wrappedNative.approve(address(vault), type(uint256).max);
```
```solidity
File: YieldBox/contracts/BoringMath.sol
  6         require(a <= type(uint128).max, "BoringMath: uint128 Overflow");
  11         require(a <= type(uint64).max, "BoringMath: uint64 Overflow");
  16         require(a <= type(uint32).max, "BoringMath: uint32 Overflow");
```

### [G-02] Do not calculate constants

The implementation of constant variables (replacements at compile-time) leads to the recomputation of an expression assigned to a constant variable each time the variable is used, wasting gas. Therefore constants should not involve calculations, rather the final value (e.g. `200_000` instead of `2 * 1e5`). For clarity, use comments to show where that value is being derived from.

```solidity
File: tap-token-audit/contracts/tokens/TapOFT.sol
  41     uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO
```
```solidity
File: tap-token-audit/contracts/options/TapiocaOptionBroker.sol
  76     uint256 constant dMAX = 50 * 1e4; // 5% - 50% discount
  77     uint256 constant dMIN = 5 * 1e4;
```
```solidity
File: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
  69     uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4; // 50%
  85     uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;
  93     uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;
```
```solidity
File: tap-token-audit/contracts/governance/twTAP.sol
  82     uint256 constant dMAX = 100 * 1e4; // 10% - 100% voting power multiplier
  83     uint256 constant dMIN = 10 * 1e4;
  95     uint256 constant DIST_PRECISION = 2 ** 128;
```
```solidity
File: tapioca-periph-audit/contracts/oracle/implementations/ARBTriCryptoOracle.sol
  41     uint256 public constant A0 = 2 * 3 ** 3 * 10_000;
```

### [G-03] Use `1eX` instead of `10 ** X`

`int`s and `uint`s in the form `10 ** X` should be re-written as `1eX`.

*There are 2 instances of this issue:*

```solidity
File: tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
  116         uint256 invested = (shares * pricePerShare) / (10 ** 18);
  146             uint256 toWithdraw = (((amount - queued) * (10 ** 18)) /
```

### [G-04] Use `else` statements instead of `if` statements twice when appropriate

Use `if else` statements saves gas compared to using two `if` statements, when appropriate, such as checking if a variable has changed from its default value.

For example:

```solidity
File: MagnetarV2.sol
1052:         address module;
1053:         if (_module == Module.Market) {
1054:             module = address(marketModule);
1055:         }
1056: 
1057:         if (module == address(0)) {
1058:             revert("MagnetarV2: module not found");
1059:         }
```

Can be changed to:
```diff
 1052:         address module;
 1053:         if (_module == Module.Market) {
 1054:             module = address(marketModule);
-1055:         }
-1056:
-1057:         if (module == address(0)) {
-1058:             revert("MagnetarV2: module not found");
-1059:         }
+1055:         } else {
+1056:             revert("MagnetarV2: module not found");
+1057:         }
```


*There are 4 instances of this issue:*
```solidity
File: tapioca-periph-audit\contracts\Magnetar\MagnetarV2.sol
1052:         address module;
1053:         if (_module == Module.Market) {
1054:             module = address(marketModule);
1055:         }
1056: 
1057:         if (module == address(0)) {
1058:             revert("MagnetarV2: module not found");
1059:         }
```

```solidity
File: tapioca-bar-audit\contracts\usd0\BaseUSDO.sol
345:         address module;
346:         if (_module == Module.Leverage) {
347:             module = address(leverageModule);
348:         } else if (_module == Module.Market) {
349:             module = address(marketModule);
350:         } else if (_module == Module.Options) {
351:             module = address(optionsModule);
352:         }
353: 
354:         if (module == address(0)) {
355:             revert("USDO: module not found");
356:         }
```

```solidity
File: ..\..\codearena\2023-07-tapioca\tapioca-bar-audit\contracts\markets\singularity\Singularity.sol
601:         address module;
602:         if (_module == Module.Borrow) {
603:             module = address(borrowModule);
604:         } else if (_module == Module.Collateral) {
605:             module = address(collateralModule);
606:         } else if (_module == Module.Liquidation) {
607:             module = address(liquidationModule);
608:         } else if (_module == Module.Leverage) {
609:             module = address(leverageModule);
610:         }
611:         if (module == address(0)) {
612:             revert("SGL: module not set");
613:         }
```

```solidity
File: ..\..\codearena\2023-07-tapioca\tapiocaz-audit\contracts\tOFT\BaseTOFT.sol
385:         address module;
386:         if (_module == Module.Leverage) {
387:             module = address(leverageModule);
388:         } else if (_module == Module.Strategy) {
389:             module = address(strategyModule);
390:         } else if (_module == Module.Market) {
391:             module = address(marketModule);
392:         } else if (_module == Module.Options) {
393:             module = address(optionsModule);
394:         }
395: 
396:         if (module == address(0)) {
397:             revert("TOFT_module");
398:         }
```



### [G-05] Use assembly to check for `address(0)`

*There are 27 instances of this issue:*

```solidity
File: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol
  40         if (address(liquidationQueue) != address(0)) {

```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol
  581         if (address(_liquidationQueue) != address(0)) {
  586         if (_bidExecutionSwapper != address(0)) {
  591         if (_usdoSwapper != address(0)) {
  611         if (module == address(0)) {

```
```solidity
File: tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
  354         if (module == address(0)) {

```
```solidity
File: tapioca-bar-audit/contracts/markets/Market.sol
  177         if (address(_oracle) != address(0)) {
  187         if (_conservator != address(0)) {

```
```solidity
File: tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
  371         if (erc20 == address(0)) {
  396         if (module == address(0)) {

```
```solidity
File: tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol
  94         if (erc20 == address(0)) {

```
```solidity
File: tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol
  74         if (erc20 == address(0)) {

```
```solidity
File: tapiocaz-audit/contracts/Balancer.sol
  112         if (connectedOFTs[_srcOft][_dstChainId].dstOft == address(0))
  127         if (_router == address(0)) revert RouterNotValid();
  128         if (_routerETH == address(0)) revert RouterNotValid();
  142         if (ITapiocaOFT(_srcOft).erc20() == address(0)) {

```
```solidity
File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
  272         if (erc20 == address(0)) {

```
```solidity
File: tap-token-audit/contracts/Vesting.sol
  132         if (_user == address(0)) revert AddressNotValid();

```
```solidity
File: tapioca-periph-audit/contracts/oracle/OracleChainlinkMultiEfficient.sol
  31             if (guardians[i] == address(0)) revert ZeroAddress();

```
```solidity
File: tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol
  19         if (_addr == address(0)) revert AddressNotValid();
  112         if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {

```
```solidity
File: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
  1057         if (module == address(0)) {

```
```solidity
File: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol
  305         if (address(singularity) != address(0)) {
  308         if (address(bigBang) != address(0)) {
  487         if (address(singularity) != address(0)) {
  490         if (address(bigBang) != address(0)) {

```

### [G-06] Hardcode address instead of using `address(this)`

Instead of address(this), it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's script.sol and solmate ```LibRlp.sol``` contracts can do this.

References:
- https://book.getfoundry.sh/reference/forge-std/compute-create-address
- https://twitter.com/transmissions11/status/1518507047943245824


*There are 311 instances of this issue:*
```solidity
File: tap-token-audit/contracts/governance/twTAP.sol
  260         tapOFT.transferFrom(msg.sender, address(this), _amount);
  448         rewardToken.safeTransferFrom(msg.sender, address(this), _amount);
```
```solidity
File: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
  379                     paymentToken.balanceOf(address(this))
  511             address(this),
```
```solidity
File: tap-token-audit/contracts/options/TapiocaOptionBroker.sol
  230         tOLP.transferFrom(msg.sender, address(this), _tOLPTokenID);
  342         tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);
  493                     paymentToken.balanceOf(address(this))
  532             address(this),
```
```solidity
File: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol
  186         yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);
  242             address(this),
```
```solidity
File: tap-token-audit/contracts/tokens/BaseTapOFT.sol
  134         _creditTo(_srcChainId, address(this), amount);
  144             _transferFrom(address(this), to, amount);
  147             _transferFrom(address(this), to, amount);
  232                     address(this),
  235                     IERC20(rewardTokens[i]).balanceOf(address(this)),
  312             this.sendFrom{value: address(this).balance}(
  313                 address(this),
```
```solidity
File: tap-token-audit/contracts/tokens/LTap.sol
  42         tapToken.transferFrom(msg.sender, address(this), amount);
```
```solidity
File: tap-token-audit/contracts/Vesting.sol
  157         uint256 availableToken = _token.balanceOf(address(this));
```
```solidity
File: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
  216             (bool success, bytes memory result) = address(this).delegatecall(
  445         uint256 balance = asset.balanceOf(address(this));
  454                 address(this),
  597             address(this),
  608         uint256 balanceBefore = yieldBox.balanceOf(address(this), assetId);
  618         swapper.swap(swapData, minAssetMount, address(this), "");
  619         uint256 balanceAfter = yieldBox.balanceOf(address(this), assetId);
  648         yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
  649         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
  700                 share <= yieldBox.balanceOf(address(this), _tokenId) - total,
  704             yieldBox.transfer(from, address(this), _tokenId, share);
  717         yieldBox.transfer(address(this), to, collateralId, share);
  732         yieldBox.withdraw(assetId, from, address(this), amount, 0);
  735             IUSDOBase(address(asset)).burn(address(this), toBurn);
  758         IUSDOBase(address(asset)).mint(address(this), amount);
  762         yieldBox.depositAsset(assetId, address(this), to, amount, 0);
```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol
  188                 share <= yieldBox.balanceOf(address(this), _assetId) - total,
  192             yieldBox.transfer(from, address(this), _assetId, share);
  243         yieldBox.transfer(address(this), to, assetId, share);
```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol
  49         yieldBox.transfer(address(this), to, collateralId, share);
  79         yieldBox.transfer(address(this), to, assetId, share);
```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol
  47         yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);
  71         _removeCollateral(from, address(this), share);
  74             address(this),
  75             address(this),
```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol
  167             address(this),
  179         uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -
  193         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
  198             address(this),
  275         uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -
  281         yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
  282         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
  288             address(this),
  331             address(this),
  350         swapper.swap(swapData, minAssetAmount, address(this), "");
```
```solidity
File: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol
  188             (bool success, bytes memory result) = address(this).delegatecall(
```
```solidity
File: tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol
  161         uint256 balanceBefore = balanceOf(address(this));
  164             _creditTo(_srcChainId, address(this), amount);
  167         uint256 balanceAfter = balanceOf(address(this));
  182                 IERC20(address(this)).safeTransfer(leverageFor, amount);
  198         _approve(address(this), externalData.swapper, amount);
  201                 address(this),
  212             address(this),
  219             address(this),
  220             address(this),
  227             value: address(this).balance
  229             address(this),
```
```solidity
File: tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol
  160         uint256 balanceBefore = balanceOf(address(this));
  163             _creditTo(_srcChainId, address(this), lendParams.depositAmount);
  166         uint256 balanceAfter = balanceOf(address(this));
  180                 IERC20(address(this)).safeTransfer(
```
```solidity
File: tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol
  127         ISendFrom(address(this)).sendFrom{value: address(this).balance}(
  127         ISendFrom(address(this)).sendFrom{value: address(this).balance}(
  162         uint256 balanceBefore = balanceOf(address(this));
  167                 address(this),
  172         uint256 balanceAfter = balanceOf(address(this));
  191                 IERC20(address(this)).safeTransfer(
  227                 address(this),
```
```solidity
File: tapioca-bar-audit/contracts/usd0/USDO.sol
  68         require(token == address(this), "USDO: token not valid");
  87         require(token == address(this), "USDO: token not valid");
  99         uint256 _allowance = allowance(address(receiver), address(this));
  101         _approve(address(receiver), address(this), _allowance - (amount + fee));
```
```solidity
File: tapioca-bar-audit/contracts/Penrose.sol
  515                 address(this),
  536             yieldBox.transfer(address(this), feeTo, assetId, feeShares);
```
```solidity
File: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol
  163                 address(this),
  164                 address(this),
  174                 deposit ? address(this) : user,
  186                 ? address(this)
  237                 address(this),
  238                 address(this),
  248                 depositAmount > 0 ? address(this) : user,
  261                 ? address(this)
  286                     address(this).balance
  345                     address(this),
  346                     address(this),
  358                         ? address(this)
  392                 address(this),
  411         //      - transfer `fraction` from user to `address(this)
  412         //      - deposits `fraction` to YB for `address(this)`
  425                 address(this),
  431                 address(this),
  432                 address(this),
  438             address lockTo = participateData.participate ? address(this) : user;
  478             IERC721(oTapAddress).approve(address(this), oTAPTokenId);
  480                 address(this),
  528                 ownerOfTapTokenId == user || ownerOfTapTokenId == address(this),
  534                     address(this),
  544                     address(this),
  582                 ? address(this)
  599                     address(this),
  617                 address(this),
  625                     address(this),
  643                 ? address(this)
  664                     address(this),
  712         yieldBox.withdraw(assetId, from, address(this), amount, 0);
  726             address(this),
  751         uint256 gas = msg.value > 0 ? msg.value : address(this).balance;
  771             address(this),
  784             address(this),
  797         IERC20(_token).safeTransferFrom(_from, address(this), _amount);
```
```solidity
File: tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol
  155                 address(this),
  156                 address(this),
  162         IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
```
```solidity
File: tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol
  134                 address(this),
  158         uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));
  163         uint256 balanceAfter = IERC20(tokenOut).balanceOf(address(this));
```
```solidity
File: tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol
  155             swapData.yieldBoxData.depositToYb ? address(this) : to,
  165                 address(this),
```
```solidity
File: tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol
  186                     ? address(this)
  200                 address(this),
```
```solidity
File: tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol
  29             address(this).balance >= amount,
  60         return computeAddress(salt, bytecodeHash, address(this));
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
  100             address(this)
  139         uint256 aaveBalanceBefore = rewardToken.balanceOf(address(this));
  142             address(this)
  151                 address(this)
  156             address(this)
  159             stakedRewardToken.claimRewards(address(this), claimable);
  164             address(this)
  167         uint256 balanceOfStkAave = stakedRewardToken.balanceOf(address(this));
  179         uint256 aaveBalanceAfter = rewardToken.balanceOf(address(this));
  194             swapper.swap(swapData, minAmount, address(this), "");
  197             uint256 queued = wrappedNative.balanceOf(address(this));
  201                 address(this),
  212             address(this)
  217             address(this)
  226         (amount, , , , , ) = lendingPool.getUserAccountData(address(this));
  227         uint256 queued = wrappedNative.balanceOf(address(this));
  235         uint256 queued = wrappedNative.balanceOf(address(this));
  240                 address(this),
  257         uint256 queued = wrappedNative.balanceOf(address(this));
  266                 address(this)
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
  131         uint256 queued = wrappedNative.balanceOf(address(this));
  139         uint256 queued = wrappedNative.balanceOf(address(this));
  150         uint256 lpBalanceBefore = pool.balanceOf(address(this));
  173             address(this),
  174             address(this),
  181         vault.joinPool(poolId, address(this), address(this), joinPoolRequest);
  181         vault.joinPool(poolId, address(this), address(this), joinPoolRequest);
  182         uint256 lpBalanceAfter = pool.balanceOf(address(this));
  198         uint256 queued = wrappedNative.balanceOf(address(this));
  209             amount <= wrappedNative.balanceOf(address(this)),
  220             address(this)
  241             pool.balanceOf(address(this))
  246             address(this),
  251         uint256 maxBpt = pool.balanceOf(address(this));
  257         vault.exitPool(poolId, address(this), payable(this), exitRequest);
  260             address(this)
  272         uint256 lpBalance = pool.balanceOf(address(this));
  292             address(this),
  293             address(this),
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
  103         uint256 toWithdraw = cToken.balanceOf(address(this));
  105         INative(address(wrappedNative)).deposit{value: address(this).balance}();
  107         result = address(this).balance;
  114         uint256 shares = cToken.balanceOf(address(this));
  117         uint256 queued = wrappedNative.balanceOf(address(this));
  124         uint256 queued = wrappedNative.balanceOf(address(this));
  143         uint256 queued = wrappedNative.balanceOf(address(this));
  151                 value: address(this).balance
  156             wrappedNative.balanceOf(address(this)) >= amount,
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
  131         uint256 claimable = rewardPool.rewards(address(this));
  151         uint256 lpBalance = rewardPool.balanceOf(address(this));
  208                 swapper.swap(swapData, minAmount, address(this), "");
  212         uint256 queued = wrappedNative.balanceOf(address(this));
  257                 address(this)
  275                 address(this)
  292         uint256 lpBalance = rewardPool.balanceOf(address(this));
  294         uint256 queued = wrappedNative.balanceOf(address(this));
  301         uint256 queued = wrappedNative.balanceOf(address(this));
  330         uint256 queued = wrappedNative.balanceOf(address(this));
  333             uint256 lpBalance = rewardPool.balanceOf(address(this));
  341             wrappedNative.balanceOf(address(this)) >= amount,
  346         queued = wrappedNative.balanceOf(address(this));
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
  106             abi.encodeWithSignature("claimable_tokens(address)", address(this))
  161         uint256 claimable = lpGauge.claimable_tokens(address(this));
  163             uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));
  165             uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));
  180                 swapper.swap(swapData, minAmount, address(this), "");
  183                     address(this)
  191                 lpGauge.deposit(lpAmount, address(this), false);
  202         result = lpGauge.balanceOf(address(this));
  211         uint256 lpBalance = lpGauge.balanceOf(address(this));
  212         uint256 queued = lpToken.balanceOf(address(this));
  219         uint256 queued = lpToken.balanceOf(address(this));
  221             lpGauge.deposit(queued, address(this), false);
  236         uint256 queued = lpToken.balanceOf(address(this));
  239             uint256 lpBalance = lpGauge.balanceOf(address(this));
  243             lpToken.balanceOf(address(this)) >= amount,
  248         queued = lpToken.balanceOf(address(this));
  250             lpGauge.deposit(queued, address(this), false);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
  104         uint256 claimable = lpGauge.claimable_tokens(address(this));
  152         uint256 claimable = lpGauge.claimable_tokens(address(this));
  154             uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));
  156             uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));
  171                 swapper.swap(swapData, minAmount, address(this), "");
  173                 uint256 queued = wrappedNative.balanceOf(address(this));
  185         uint256 lpBalance = lpGauge.balanceOf(address(this));
  197         uint256 lpBalance = lpGauge.balanceOf(address(this));
  199         uint256 queued = wrappedNative.balanceOf(address(this));
  206         uint256 queued = wrappedNative.balanceOf(address(this));
  226         uint256 queued = wrappedNative.balanceOf(address(this));
  229             uint256 lpBalance = lpGauge.balanceOf(address(this));
  236             wrappedNative.balanceOf(address(this)) >= amount,
  240         queued = wrappedNative.balanceOf(address(this));
  251         lpGauge.deposit(lpAmount, address(this), false);
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
  120             uint256 wethAmount = weth.balanceOf(address(this));
  131         amount = IERC20(contractAddress).balanceOf(address(this));
  141         uint256 freeGlp = stakedGlpTracker.balanceOf(address(this));
  167         uint256 wethAmount = weth.balanceOf(address(this));
  190         uint256 totalVestable = vester.getMaxVestableAmount(address(this));
  191         uint256 vestingNow = vester.balances(address(this));
  192         uint256 alreadyVested = vester.cumulativeClaimAmounts(address(this));
  205             address(this)
  209         uint256 tokenReserved = vester.pairAmounts(address(this));
  254         uint256 freeEsGmx = esGmx.balanceOf(address(this));
  256             address(this),
  263             stakedGlpTracker.balanceOf(address(this))
  281         uint256 freeEsGmx = esGmx.balanceOf(address(this));
  288         uint256 unusedStakedTokens = feeGmxTracker.balanceOf(address(this));
  302         uint256 freeEsGmx = esGmx.balanceOf(address(this));
  305             address(this),
  317         uint256 gmxAmount = gmx.balanceOf(address(this));
  325             address(this),
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
  107         uint256 toWithdraw = stEth.balanceOf(address(this));
  119         uint256 stEthBalance = stEth.balanceOf(address(this));
  123         uint256 queued = wrappedNative.balanceOf(address(this));
  129         uint256 queued = wrappedNative.balanceOf(address(this));
  148         uint256 queued = wrappedNative.balanceOf(address(this));
  161         queued = wrappedNative.balanceOf(address(this));
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
  119             address(this)
  162             address(this)
  166             uint256 stgBalanceBefore = stgTokenReward.balanceOf(address(this));
  168             uint256 stgBalanceAfter = stgTokenReward.balanceOf(address(this));
  184                 swapper.swap(swapData, minAmount, address(this), "");
  186                 uint256 queued = wrappedNative.balanceOf(address(this));
  198             address(this)
  204             address(this)
  206         result = address(this).balance;
  215         uint256 queued = wrappedNative.balanceOf(address(this));
  216         (amount, ) = lpStaking.userInfo(lpStakingPid, address(this));
  224         uint256 queued = wrappedNative.balanceOf(address(this));
  235         uint256 toStake = stgNative.balanceOf(address(this));
  248         uint256 queued = wrappedNative.balanceOf(address(this));
  256                 address(this)
  263             amount <= wrappedNative.balanceOf(address(this)),
```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
  104         uint256 toWithdraw = vault.balanceOf(address(this));
  105         result = vault.withdraw(toWithdraw, address(this), 0);
  112         uint256 shares = vault.balanceOf(address(this));
  115         uint256 queued = wrappedNative.balanceOf(address(this));
  122         uint256 queued = wrappedNative.balanceOf(address(this));
  124             vault.deposit(queued, address(this));
  139         uint256 queued = wrappedNative.balanceOf(address(this));
  145             vault.withdraw(toWithdraw, address(this), 0);
```
```solidity
File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
  176         uint256 balanceBefore = balanceOf(address(this));
  179             _creditTo(_srcChainId, address(this), amount);
  182         uint256 balanceAfter = balanceOf(address(this));
  197                 IERC20(address(this)).safeTransfer(leverageFor, amount);
  212         _unwrap(address(this), amount);
  221             address(this),
  230             value: address(this).balance
  232             address(this),
```
```solidity
File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol
  152         uint256 balanceBefore = balanceOf(address(this));
  155             _creditTo(_srcChainId, address(this), borrowParams.amount);
  158         uint256 balanceAfter = balanceOf(address(this));
  172                 IERC20(address(this)).safeTransfer(_from, borrowParams.amount);
```
```solidity
File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol
  142         ISendFrom(address(this)).sendFrom{value: address(this).balance}(
  142         ISendFrom(address(this)).sendFrom{value: address(this).balance}(
  177         uint256 balanceBefore = balanceOf(address(this));
  182                 address(this),
  187         uint256 balanceAfter = balanceOf(address(this));
  206                 IERC20(address(this)).safeTransfer(
  242                 address(this),
```
```solidity
File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol
  143         uint256 balanceBefore = balanceOf(address(this));
  146             _creditTo(_srcChainId, address(this), amount);
  149         uint256 balanceAfter = balanceOf(address(this));
  159                 address(this),
  165                 IERC20(address(this)).safeTransfer(onBehalfOf, amount);
  206         _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));
  209             address(this),
  211             LzLib.addressToBytes32(address(this)),
  225             address(this).balance
  230             LzLib.addressToBytes32(address(this)),
```
```solidity
File: tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
  359         IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);
  460                     IERC20(address(this))
```
```solidity
File: tapiocaz-audit/contracts/Balancer.sol
  286         if (address(this).balance < _amount) revert ExceedsBalance();
  305         if (erc20.balanceOf(address(this)) < _amount) revert ExceedsBalance();
```
```solidity
File: tapiocaz-audit/contracts/TapiocaWrapper.sol
  198                                 address(this),
  216                                 address(this),
```
```solidity
File: YieldBox/contracts/YieldBox.sol
  148             if (asset.contractAddress == address(this)) {
  361         require(operator != address(this), "YieldBox: can't approve yieldBox");
  383         require(operator != address(this), "YieldBox: can't approve yieldBox");
  489             return depositAsset(registerAsset(TokenType.ERC1155, address(this), strategy, tokenId), from, to, amount, share);
```

### [G-07] Avoid multiple check combinations by using nested `if` statements

Using nested `if` statements is cheaper than using multiple check combinations with `&&`.

*There are 28 instances of this issue:*
```solidity
File: tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
  370         if (!success && !_forwardRevert) {

```
```solidity
File: tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
  412         if (!success && !_forwardRevert) {

```
```solidity
File: tapiocaz-audit/contracts/Balancer.sol
  226         if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

```
```solidity
File: tapiocaz-audit/contracts/TapiocaWrapper.sol
  121         if (_revertOnFailure && !success) {
  144             if (_call[i].revertOnFailure && !success) {

```
```solidity
File: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol
  315                     _singularities[i] == sglAssetID && i < sglLastIndex

```
```solidity
File: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
  1046         if (!success && !allowFailure) {

```
```solidity
File: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol
  402         if (lendAmount == 0 && depositData.deposit) {
  612             !removeAndRepayData.assetWithdrawData.withdraw &&

```
```solidity
File: tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
  171             if (daysPassed && balanceOfStkAave > 0) {

```

```solidity
File: YieldBox/contracts/BoringMath.sol
  27         if (roundUp && (result * div) / mul < value) {

```
```solidity
File: YieldBox/contracts/YieldBoxRebase.sol
  35         if (roundUp && (share * totalAmount) / totalShares_ < amount) {
  58         if (roundUp && (amount * totalShares_) / totalAmount < share) {
```

### [G-08] Rearrange require/revert statements to save gas

Check cheaper checks first to potentially fail earlier and waste less gas. Checks such as ones that invoke `msg.sender` (which is expensive) should be placed further down in order to fail at a previous statement, saving gas.

*There are 6 instances of this issue:*

This:
```solidity
File: Penrose.sol
271:     function updatePause(bool val) external {
272:         require(msg.sender == conservator, "Penrose: unauthorized");
273:         require(val != paused, "Penrose: same state");
```

Can be changed to 
```solidity
require(val != paused, "Penrose: same state");
require(msg.sender == conservator, "Penrose: unauthorized");
```

Similar for the following (rearrange to have `msg.sender` or the other expensive checks after the other statements):
```solidity
File: AirdropBroker.sol
447:         require(PCNFT.ownerOf(_tokenID) == msg.sender, "adb: Not eligible");
448:         address tokenIDToAddress = address(uint160(_tokenID));
449:         require(
450:             userParticipation[tokenIDToAddress][3] == false,
451:             "adb: Already participated"
452:         );

```

```solidity
File: TapOFT.sol
219:     /// @param _amount TAP amount
220:     function extractTAP(address _to, uint256 _amount) external notPaused {
221:         require(msg.sender == minter, "unauthorized");
222:         require(_amount > 0, "amount not valid");

```
```solidity
File: Market.sol
245:     function updatePause(bool val) external {
246:         require(msg.sender == conservator, "Market: unauthorized");
247:         require(val != paused, "Market: same state");

```
```solidity
File: BaseUSDO.sol
114:     function updatePause(bool val) external {
115:         require(msg.sender == conservator, "USDO: unauthorized");
116:         require(val != paused, "USDO: same state");
```

### [G-09] `x +=` costs more gas than `x = x +` for state variables (instances missed in automated findings)

There are some instances missed in the automated findings, for when using `+=` and `-=` to increment/decrement costs more gas than using `x = x +` or `x = x -` for state variables.

Some of these were missed in automated findings. Below contains the missed findings.

*There are 29 additional instances of this issue:*
```solidity
File: SGLCommon.sol
184:         _yieldBoxShares[to][_asset_sig] += share;

```
```solidity
File: SGLLendingCommon.sol
46:         userCollateralShare[from] -= share;

```
```solidity
File: SGLLendingCommon.sol
47:         totalCollateralShare -= share;

```
```solidity
File: SGLLendingCommon.sol
53:             _yieldBoxShares[from][COLLATERAL_SIG] -= share;

```
```solidity
File: Vesting.sol
116:         users[msg.sender].claimed += _claimable;

```
```solidity
File: twTAP.sol
343:         weekTotals[w0 + 1].netActiveVotes += int256(votes);

```
```solidity
File: twTAP.sol
344:         weekTotals[w1 + 1].netActiveVotes -= int256(votes);

```
```solidity
File: twTAP.sol
491:                     claimed[_tokenId][i] += amount;

```
```solidity
File: twTAP.sol
513:                     claimed[_tokenId][claimableIndex] += amount;

```
```solidity
File: twTAP.sol
514:                     rewardTokens[claimableIndex].safeTransfer(_to, amount);

```
```solidity
File: AirdropBroker.sol
259:         aoTAPCalls[_aoTAPTokenID][cachedEpoch] += chosenAmount; // Adds up exercised amount to current epoch

```
```solidity
File: TapiocaOptionBroker.sol
392:         oTAPCalls[_oTAPTokenID][cachedEpoch] += chosenAmount; // Adds up exercised amount to current epoch

```
```solidity
File: TapiocaOptionLiquidityProvision.sol
187:         activeSingularities[_singularity].totalDeposited += _amount;

```
```solidity
File: TapOFT.sol
227:         mintedInWeek[week] += _amount;

```

```solidity
File: MarketERC20.sol
147:                 balanceOf[to] += amount;

```
```solidity
File: BigBang.sol
553:         userCollateralShare[to] += share;

```
```solidity
File: SGLCommon.sol
184:         _yieldBoxShares[to][_asset_sig] += share;

```
```solidity
File: SGLLendingCommon.sol
26:         userCollateralShare[to] += share;

```
```solidity
File: SGLLendingCommon.sol
70:         userBorrowPart[from] += part;

```
```solidity
File: SGLLiquidation.sol
195:         totalAsset.elastic += uint128(returnedShare - callerShare);

```
```solidity
File: SGLLiquidation.sol
284:         totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);

```
```solidity
File: Singularity.sol
465:         balanceOf[_feeTo] += _feesEarnedFraction;

```
```solidity
File: Balancer.sol
255:         connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;

```
```solidity
File: YieldBox.sol
324:             balanceOf[from][id] -= value;

```
```solidity
File: YieldBox.sol
325:             balanceOf[to][id] += value;
```

```solidity
File: TapiocaOptionLiquidityProvision.sol
247:         activeSingularities[_singularity].totalDeposited -= lockPosition.amount;
```

```solidity
File: TapOFT.sol
204:         dso_supply -= mintedInWeek[week - 1];
```

```solidity
File: MarketERC20.sol
80:             allowance[from][msg.sender] -= share;
```

```solidity
File: MarketERC20.sol
89:             allowanceBorrow[from][msg.sender] -= share;

```

