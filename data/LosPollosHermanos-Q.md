# [LOW 1] Zero address checks in `Penrose.withdrawAllMarketFees()` check only the first element of the `swappers_[]` array and `markets_[]` array.

## Description
The require statement in `withdrawAllMerketFees()` here: 
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L237
only makes sure that the first `swapper` and `market` are not `address(0)`; however, `address(0)` can still be a ur present in `swappers_[]` and `markets_[]` arrays.

## Mitigation
Check every element of `swappers_[]` and `markets_[]` arrays for `address(0)` in 
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L232

# [LOW 2] `Market.computeClosingFactor()` can report incorrect results.

## Description
`ratePrecision != FEE_PRECISION_DECIMALS` will cause `Market.computeClosingFactor()` to report incorrect results. This arises due to:
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L285

borrowPartScaled will have 18 decimals, while liquidationStartsAt has 18 - ratePrecision + FEE_PRECISION_DECIMALS. This can cause computeClosingFactor() to return 0 even if a user should be liquidated (when ratePrecision > FEE_PRECISION_DECIMALS)

## Mitigation
Replace `ratePrecision` with a constant `FEE_PRECISION_DECIMALS`.

# [NON-CRITICAL 1] Nested if statements can be removed from `MarketERC20` transfer functions. 
## Description
Nested if statements in `Market.transfer()` (https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140) and `Market.transferFrom()` (https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L165) is unnecessary.

## Mitigation
Consider changing if-statements mentioned above from `amount !=0 || msg.sender == to` to `amount !=0 && msg.sender != to` and remove nested if-statements.

# [NON-CRITICAL 2] Penrose._getMasterContractLength() has a misleading name.
## Description
`Penrose._getMasterContractLength()` (https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L542) does not return `length` but rather instances of markets.

## Mitigation
Consider changing `Penrose._getMasterContractLength()` to something more appropriate e.g. `Penrose._getMasterContractInstances()` 