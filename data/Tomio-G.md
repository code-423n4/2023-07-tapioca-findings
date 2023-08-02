Title: Using `!=` in `require` statement is more gas efficient

Proof of Concept:
[USDO.sol#L89](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L89)

Recommended Mitigation Steps:
Change `> 0` to `!= 0`
________________________________________________________________________

Title: Use single comparison operation instead of two is more gas efficient

Proof of Concept:
[SGLLendingCommon.sol#L75](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L75)

Recommended Mitigation Steps:
Change to:
```
    require(_totalAsset.base > 999, "SGL: min limit"); // >999 is the same as >=1000.
```
________________________________________________________________________