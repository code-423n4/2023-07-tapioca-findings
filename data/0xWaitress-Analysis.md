## Swapper too powerful and harm decentralization
### Some swapper setter(update) are not needed
setTricryptoLPGetter is quite unnecessarily since Convex and Curve are both immutable, the interface and function in tricryptoLPGetter does not need to change and can be hardened to reduce degree of centralization

### Swapper can brick withdrawal
_currentBalance depends on swapper; which is settable reduce degree of decentralisation.
Imagine the swapper get set to 0 or a malicious contract, to make the `swapper.getOutputAmount` revert, this would completely break the `compoundAmount`, thus _currentBalance, since withdrawal needs to be compared against _currentBalance, withdrawal is bricked.

### Swapper too much allowance 
Also; the swapper can be set into an EOA, which then has max ability to pull money from the strategy, which is unnecessarily privileged. Instead, the swapper could just get the allowance whenever swap is needed (during compound), this cost some more gas but keep the principle safe from swapper vulnerability.


## external dependency
### swap in emergencyWithdraw should be optional instead of mandatory
Consider removing the swap path during emergencyWithdraw in all strategy since it added unnecessarily external dependency on swapper.

### withdraw takes compounding is fine in normal flow but may potentially block withdrawal in market volatility.

Consider allowing people to _withdraw without compounding, since compound is an expensive operation, and also has dependency on swapper which is admin controlled. This weakens the permission less nature since now user can be bricked from withdrawal if the owner set swapper to address(0); alternatively market may turn volatile and lead to slippage wider than tolerance leading to revert of withdrawal.


## Slippage Protection
### Strategy
In many strategies swapping, including but not limittted to GLP, Curve, Convex, Aave and Balancer, slippage is hardcoded as 0.5%; and applied on the latest price on the pool based on state data; this is quite vulnerable to on-chain MEV; it's advised to :

1. incorporate chainlink data feed
2. allow user to specify a user-customed slippage relative to the chainlink price feed, so that he/she can express more control over how he/she wants the tx to happen.

Occurence:
All strategy get price from swapper which uses pool state to compute price. this makes the price vulnerable to price manipulation/frontrunning.

(Using Convex as example)

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L142

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L59-L76

### Slippage Protection on Option oTAP exercise
Similarly, oTAP exerciseOption also would better have a slippage protection, since it allows option exerciser to pay for options under discount, and an external price is also needed to calculate the amount of TAP output. Slippage protection is quite necessary so user dont get caught with excessive input.


## CodeBase Quality
Overall the code is very symmetrical and inheritance is very easy to follow. Storage variables are clearly separated; Strategy and yieldbox interacts in systematic ways. However, this should not be the reason for doing mechanical copy-and-past. There are some quite easy-to-catch bugs for example emergencyWithdrawal(minAmount is set to 0.5% of principle); which actually appear TWICE. So it probably happens because of hurry copy-pasting. Introducing some unit test, with more coverage would be helpful. While keeping a nice interface among instances of strategy/swapper is nice, but maybe the BASEERC20Strategy could be thought-out with less common function, so unused function in certain instance, dont have to be implemented as an empty function: eg `compound()` in LidoETHStrategy.

## Systemic risks
### Bridge
The biggest risks are mostly bridge risk; change of gas in op-code or gasLimit in relation to the trigger on layerzero relayer at the other chain remains non-trivial; and should be handled with continuous care.

### Liquidity 
On UniswapV3Swapper, poolFee is a state variable. Although it is configurable by the owner, however it's quite unflexible since liquidity could always hop among different pools (0.3%, 0.05% or even 0.01%) depending which assets are being swapped, and how the market is evaluating them. Also the poolFee can be misconfigured, if the owner set the poolFee to a value that is not existent (there is no pool for 0.5% for example)

It's better to ideally, 
1. allow the swap initialiser to specify which pool(s) to swap; 

2. provide some custom approach in hopping multiple paths, instead of using SingleInput only. Allow ExactSingle call.

3. try to set poolFee to be tier (0.3%, 0.05%, 0.01%) as this is encoded in uniswap too and toggle, dont allow arbitrary setting



### Time spent:
20 hours