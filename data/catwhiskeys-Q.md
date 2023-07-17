# 1) Functions return nothing
There are functions that return nothing; by reviewing and testing code it appears that they return `-` (nothing).
They are used in the following functions '_vestByGlp', '_vestByEsGmx', '_sellGmx' which might adversely affect contract security and compile in an unexpected way.

Empty return statement permitted on functions with return values, but there are no returning values defined in these functions.
Additional info can be found by the following thread in github: https://github.com/ethereum/solidity/issues/4541

3 Instances:
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L266
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L295
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L319

Recommended mitigation steps:
Consider adding return value to the functions or reformat them.
It might be `return 0;` or other returning value, according to the Company's needs.


# 2) Internal, but public function `exerciseInternal`
There are 2 function that exercise options: `exercise` and `exerciseInternal`. Both of them are public, and they have quite a similar functionality.
If `exerciseInternal` is for internal use, consider making it `internal`, or limit pool of users, who can acccess it.
There is no ownership check provided for `exerciseInternal`, which might be not secure.

Instance:
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L221-L257

Recommended mitigation steps:
Consider making this function `internal` or `private`, and adding ownership check.

# 3) Prefer abi.encode over abi.encodePacked
abi.encodePacked can result in hash collisions when used with two dynamic arguments (string/bytes).
Unless there's compelling reason, use abi.encode. 

5 Instances:
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L196
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L214
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L272
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L291
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L72

Recommended mitigation steps:
Consider replacing abi.encodePacked by abi.encode