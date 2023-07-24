### Gas Optimizations List:
| Number | Optimization Details | Instances |
|:--:|:-------| :-----:|
| [G-01] | Validate the lengths of two array input parameters to be equal before using them |5|
| [G-02] | Using immutable instead of constant for inline keccak256 hashes of strings |2|

## [G-01]  Validate the lengths of two array input parameters to be equal before using them in a loop

##### Description:
Before using two array input parameters simultaneously in a loop, their lengths must be equal. If length are not equal and you attempt to read the index which doesn't exist, it reverts resulting in waste of gas. As loop requires a lot of gas and revert due to uneven input parameters happen at the last index,  the more lengthy the loop, the more gas would be lost.

##### Instances:

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L307C1-L314C6

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L316C1-L329C6

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L336C1-L353C6

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L127C1-L132C6

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L138C1-L145C6

## [G-02] Use immutable instead of constant for inline keccak256 hashes of strings

##### Description:
For inline keccak256 hashes of strings, using immutable instead of constant is more gas efficient.
Though after changing them to immutable, the state mutability of functions in which these hashes are used, need to be changed to view from pure.

##### Instances:

https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L27C1-L32C94


###### Savings:

Gas savings for using immutable instead of constant obtained from tests is ``22 gas`` per call. So, for 2 instances it will be ``44`` per call. 