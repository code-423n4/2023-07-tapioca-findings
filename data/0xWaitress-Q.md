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