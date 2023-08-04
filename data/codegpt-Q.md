## violation of check effect pattern
- Function `AaveStrategy.compound` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 
- Function `ConvexTricryptoStrategy.compound` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 
- Function `TricryptoLPStrategy.compound` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 
- Function `TricryptoNativeStrategy.compound` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 
- Function `GlpStrategy._sellGmx` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 
- Function `StargateStrategy.compound` violates the check effect pattern because `ISwapper.swap` can be triggered by an external non-privileged individual. 

Recommend adding non-reentrant modifier

## Missing zero address validation to avoid unexpected configuration
When setting up or update address, there is no validation to avoid the zero address in the current codebase.

For example, in GlpStrategy.sol
```solidity
    function setFeeRecipient(address recipient) external onlyOwner {
        feeRecipient = recipient;
    }
```

## unsafe type cast
The following type cast may cause unexpected value conversion due to the range issue:
```
BalancerStrategy.sol L179
joinPoolRequest.userData = abi.encode(2, bptOut, uint256(index));

StargateStrategy.sol L202/254
        router.instantRedeemLocal(
            uint16(lpRouterPid), <-
            toWithdraw,
            address(this)
        );
```

## BalancerStrategy.sol may lock native token
BalancerStrategy.sol maintain an `receive` function but no way to withdraw native token.

## Divide before multiply may lose precision
In contract `twAML.sol`
```solidity=65
            denominator := div(denominator, twos)
```

```solidity=86
        uint256 inv = (3 * denominator) ^ 2;
```

---
```solidity=70
            prod0 := div(prod0, twos)
```

```solidity=104
        result = prod0 * inv;
```

## Unused values
In MagnetarMarketModule.sol
```solidity=744
        (
            bool withdrawOnOtherChain,
            uint16 destChain,
            bytes32 receiver,
            bytes memory adapterParams
        ) = abi.decode(withdrawData, (bool, uint16, bytes32, bytes));
```
- The variable `withdrawOnOtherChain` is never used after this assignment.

---
In BaseSwapper.sol
```solidity=153
            (amount, share) = _yieldBox.withdraw(
                tokenId,
                address(this),
                address(this),
                amount,
                share
            );
```
- The variable `share` is never used after this assignment.

## Unupdate Loop
```
            for (uint256 i = 0; i < sglLength; i++) {
                // If last element, just pop
                if (i == sglLastIndex) {
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];
                    singularities.pop();
                } else if (
                    _singularities[i] == sglAssetID && i < sglLastIndex
                ) {
                    // If in the middle, copy last element on deleted element, then pop
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];

                    singularities[i] = _singularities[sglLastIndex];
                    singularities.pop();
                    break;
                }
            }
```
The length of singularities may decrease overtime but sglLength never updated.
Also it can be a chance run out of the gas.

## Unused state variable

Some state variables are not used in the codebase. This can lead to incomplete functionality or potential vulnerabilities if these variables are expected to be utilized.

Variable `FEE_PRECISION_DECIMALS` in `Market` is never used in `Market.sol`.

```solidity=84
    uint256 internal constant FEE_PRECISION_DECIMALS = 5;
```

```solidity=14
abstract contract Market is MarketERC20, BoringOwnable {
```