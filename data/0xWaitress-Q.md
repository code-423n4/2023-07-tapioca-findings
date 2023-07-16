### [I-1] setMarketConfig would be reverted if new `_minLiquidatorReward` is larger than the old `maxLiquidatorReward`.

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