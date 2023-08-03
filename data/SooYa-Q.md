## Function calling contracts with transfer hooks are missing reentrancy guards

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L207-L250

```        
        yieldBox.transfer(
            address(this),
            _to,
            lockPosition.sglAssetID,
            sharesOut
        );
        activeSingularities[_singularity].totalDeposited -= lockPosition.amount;
```
It is not safe when we call an address with transfer hooks without using reentrancy guards, even the function follows the best practice of check-effects-interaction.
And i suggest to swap position for the transfer function and the operation 0f `activeSingularities[_singularity].totalDeposited` to get the best practice of check-effects-interaction.