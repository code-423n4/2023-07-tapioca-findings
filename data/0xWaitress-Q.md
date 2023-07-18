### [L-1] setMarketConfig would be reverted if new `_minLiquidatorReward` is larger than the old `maxLiquidatorReward`.

The setMarketConfig first ensure if the new _minLiquidatorReward is smaller than the old maxLiquidatorReward; this is unnecessary. 
Consider it was 1(min), 5(max) and now the value pair is updated to 6(min) 10(max); the check would revert.

```solidity
if (_minLiquidatorReward > 0) {
            require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");
            require(
                _minLiquidatorReward < maxLiquidatorReward,
                "Market: not valid"
            );
            minLiquidatorReward = _minLiquidatorReward;
        }

        if (_maxLiquidatorReward > 0) {
            require(_maxLiquidatorReward < FEE_PRECISION, "Market: not valid");
            require(
                _maxLiquidatorReward > minLiquidatorReward,
                "Market: not valid"
            );
            maxLiquidatorReward = _maxLiquidatorReward;
        }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L158

Same occurrence on `setSingularityConfig`:

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L523-L535

### Recommendation
Just ensure the new _minLiquidatorReward is smaller than the NEW _maxLiquidatorReward

### [L-2] _getRatio should ensure a floor up by adding `9` instead of `5` before dividing by 10

On Market, AllowanceAmount is computed by calling a `_getRatio` function which calculate the ratio of borrowAmount over maxBorrowable; and the _numerator amplified by 10, plus 5 before diving by `10`. This is a classic round down (if the last digit is 4), and round up if the last digit is 5. 

However needed allowance is always rounded up for mitigating inadequate allowance during approve.

```solidity
    function _computeAllowanceAmountInAsset(
        address user,
        uint256 _exchangeRate,
        uint256 borrowAmount,
        uint256 assetDecimals
    ) internal view returns (uint256) {
        uint256 maxBorrowabe = _computeMaxBorrowableAmount(user, _exchangeRate);

        uint256 shareRatio = _getRatio(
            borrowAmount,
            maxBorrowabe,
            assetDecimals
        );
        return (shareRatio * userCollateralShare[user]) / (10 ** assetDecimals);
    }

    function _getRatio(
        uint256 numerator,
        uint256 denominator,
        uint256 precision
    ) private pure returns (uint256) {
        if (numerator == 0 || denominator == 0) {
            return 0;
        }
        uint256 _numerator = numerator * 10 ** (precision + 1);
        uint256 _quotient = ((_numerator / denominator) + 5) / 10;
        return (_quotient);
    }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L464-L491

### Recommendation 
```solidity
---        uint256 _quotient = ((_numerator / denominator) + 5) / 10;
+++        uint256 _quotient = ((_numerator / denominator) + 9) / 10;
```

### [L-3] buyCollateral in SGLLeverage does not deduce allowance from `_allowBorrow` first, which does not conform to CEI practice.

```solidity
        uint256 collateralShare;
        (amountOut, collateralShare) = swapper.swap(
            swapData,
            minAmountOut,
            from,
            dexData
        );
        require(amountOut >= minAmountOut, "SGL: not enough");

        _allowedBorrow(from, collateralShare);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L175-L184

On the contrary, the snippet at `multiHopBuyCollateral` has put _allowBorrow before calling swap/sendForLeverage, which is an external call. So they are not consistent here

```solidity
        _allowedBorrow(from, collateralShare);
        _addCollateral(from, from, false, 0, collateralShare);

        //borrow
        (, uint256 borrowShare) = _borrow(from, from, borrowAmount);

        //withdraw
        yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);

        IUSDOBase(address(asset)).sendForLeverage{value: msg.value}(
            borrowAmount,
            from,
            lzData,
            swapData,
            externalData
        );
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L40-L55

### Recommendation
be consistent and use the workflow in `multiHopBuyCollateral` to put _allowBorrow before swap.

### [NA-1] liquidate in SLLiquidation does not ensure length of users is equal to length of maxBorrowParts

liquidate takes an array of users and maxBorrowParts respectively, and iterate over them at an private function `_closedLiquidation`. However none of them checks length of users == maxBorrowParts.
```solidity
    function liquidate(
        address[] calldata users,
        uint256[] calldata maxBorrowParts,
        ISwapper swapper,
        bytes calldata collateralToAssetSwapData,
        bytes calldata usdoToBorrowedSwapData
    ) external notPaused {
```

_closedLiquidation
```solidity
    function _closedLiquidation(
        address[] calldata users,
        uint256[] calldata maxBorrowParts,
        ISwapper swapper,
        uint256 _exchangeRate,
        bytes calldata swapData
    ) private {
        uint256 liquidatedCount = 0;
        for (uint256 i = 0; i < users.length; i++) {
```

### Recommendation
group the two arrays as a struct, or add checks on users == maxBorrowParts

### [NA-2] liquidationStartsAt can be re-sued to remove redundant code

`numerator` is equal to `borrowPartScaled - liquidationStartsAt`
```solidity
        uint256 liquidationStartsAt = (collateralPartInAssetScaled *
            collateralizationRate) / (10 ** ratesPrecision);
        if (borrowPartScaled < liquidationStartsAt) return 0;

        uint256 numerator = borrowPartScaled -
            ((collateralizationRate * collateralPartInAssetScaled) /
                (10 ** ratesPrecision));
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L283-L289

### [N-2] computeClosingFactor is a mis-leading function name since `closing-factor` is well-interpreted as a ratio instead of the actual liquiditable amount.

```solidity
    /// @notice returns the maximum liquidatable amount for user
    function computeClosingFactor(
        uint256 borrowPart,
        uint256 collateralPartInAsset,
        uint256 borrowPartDecimals,
        uint256 collateralPartDecimals,
        uint256 ratesPrecision
    )
```

### Recommendation 
consider changing the function name to `computeClosingQuantiy` for more clarity.


### [N-3] transfer in MarketERC20 has unnecessarily logic check.

`|| msg.sender == to is unnecessary` . since `msg.sender!=to` is checked at the later part which only execute when the sender and receiver are two different addresses

```solidity
if (amount != 0 || msg.sender == to) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140

###Recommendation
```solidity
--- if (amount != 0 || msg.sender == to) {
+++ if (amount != 0) {
```