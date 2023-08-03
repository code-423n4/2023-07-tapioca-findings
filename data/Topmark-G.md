https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L308-L326
from L308-L326 pf TapiocaOptionLiquidityProvision.sol contract, a better way to save gas from the for loop is to reshuffle the if else condition with its content and the if condition and its content, the if condition content can only be executed at the very last end of the for loop "if(i == sglLastIndex)", so checking this condition first over and over again would consume excess gas
```solidity
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
a better way is 
```solidity
 for (uint256 i = 0; i < sglLength; i++) {         
                 if (
                    _singularities[i] == sglAssetID && i < sglLastIndex
                ) {
                    // If in the middle, copy last element on deleted element, then pop
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];

                    singularities[i] = _singularities[sglLastIndex];
                    singularities.pop();
                    break;
                }
                 // If last element, just pop
                else if (i == sglLastIndex) {    // <----- this condition will only be checked at the last loop
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];
                    singularities.pop();
                } 
            }
```