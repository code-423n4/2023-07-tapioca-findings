**tapiocaz-audit/contracts/TapiocaWrapper.sol**
- L51/61 - The SetFees event is created, but it is never used, therefore it should be removed. The same is true for the TapiocaWrapper__MngmtFeeTooHigh error.


**tapiocaz-audit/contracts/Balancer.sol**
- L63/339 - The SLIPPAGE_PRECISION constant is created in storage, but it is only used once, therefore gas could be saved, if the local constant is created.


**tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol**
- L52 - The RewardTokens event is created, but it is never used, therefore an extra gas expense is generated, it should be eliminated.
