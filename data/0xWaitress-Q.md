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


### [N-1] liquidationStartsAt can be re-sued to remove redundant code

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