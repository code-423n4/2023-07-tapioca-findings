For easier code readability, the following changes can be made.
## [1]
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L76C1-L77C37
    
```
    uint256 constant dMAX = 50 * 1e4; // 5% - 50% discount
    uint256 constant dMIN = 5 * 1e4;
```

`dMIN` could be renamed to `DISCOUNT_MIN` 
`dMAX` could be renamed to `DISCOUNT_MAX`

## [2]

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/twAML.sol#L133C1-L134C23

```
    uint256 _dMin,
    uint256 _dMax,
```

`_dMin` could be renamed to `_discountMin`
`_dMax` could be renamed to `_discountMax`

## [3]

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L146C1-L167C6

```
    int128 i,
    int128 j,
```

`i` could be renamed to `inputTokenId`

`j` could be renamed to `outputTokenId`. 

This will make the code readability better and make it easier to understand.

## [4]

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/interfaces/ICurvePool.sol

```
    function get_dy(
        int128 i,
        int128 j,
        uint256 dx
    ) external view returns (uint256);

    function exchange(int128 i, int128 j, uint256 dx, uint256 min_dy) external;
```

`get_dy` could be renamed to `getOutputAmount`

`i` could be renamed to `inputTokenId`

`j` could be renamed to `outputTokenId`

`dx` could be renamed to `amountIn`

`min_dy` could be renamed to `minOutAmount`

This will make the code easier to understand.

## [5]

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L42

```
    uint256 public dso_supply = 53_313_405 * 1e18;
```

`dso_supply` could be renamed to `DSO_SUPPLY`.

## [6]

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L45

```
    uint256 constant decay_rate = 8800000000000000; // 0.88%
```

`decay_rate` could be renamed to `DECAY_RATE`.


