| | issue |
| ----------- | ----------- |
| 1 | [state variable should be declared as constant which saves lots of gas](#1-state-variable-should-be-declared-as-constant-which-saves-lots-of-gas) |
| 2 | [state variables should be cached in stack variables rather than re-reading them from storage](#2-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage-the-ones-not-in-the-bot-findings) |
| 3 | [use existing cached version of state var or argument containing the same value](#3-use-existing-cached-version-of-state-var-or-argument-containing-the-same-value) |
| 4 | [state variables are read inside the loop every time for no reason](#4-state-variables-are-read-inside-the-loop-every-time-for-no-reason) |
| 5 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#5-x--y-costs-more-gas-than-x--x--y-for-state-variables-also---ones-not-caught-by-the-bot) |
| 6 | [Consider merging sequential for loops](#6-consider-merging-sequential-for-loops) |
| 7 | [Use of emit inside a loop](#7-use-of-emit-inside-a-loop) |
| 8 | [Using `this.<fn>()` wastes gas](#8-using-thisfn-wastes-gas) |
| 9 | [Unbounded Gas Consumption Risk Due to External Call Recipients](#9-unbounded-gas-consumption-risk-due-to-external-call-recipients) |
| 10 | [Use selfbalance() instead of address(this).balance](#10-use-selfbalance-instead-of-addressthisbalance) |
| 11 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#11-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 12 | [using > 0 costs more gas than != 0 when used on a uint in a require() statement](#12-using--0-costs-more-gas-than--0-when-used-on-a-uint-in-a-require-statement) |
| 13 | [Use assembly to check for address(0)](#13-use-assembly-to-check-for-address0) |
| 14 | [Use hardcoded address instead address(this)](#14-use-hardcoded-address-instead-addressthis) |
| 15 | [The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement](#15-the-use-of-a-logical-and-in-place-of-double-if-is-slightly-less-gas-efficient-in-instances-where-there-isnt-a-corresponding-else-statement-for-the-given-if-statement) |
| 16 | [Divisions which do not divide by -X cannot overflow or overflow so such operations can be unchecked to save gas](#16-divisions-which-do-not-divide-by--x-cannot-overflow-or-overflow-so-such-operations-can-be-unchecked-to-save-gas) |
| 17 | [Use assembly hashing instead of keccak256()](#17-use-assembly-hashing-instead-of-keccak256) |


## 1. state variable should be declared as constant which saves lots of gas

every use of them will be very cheap instead of 100 gas. and a Gsset (20000 gas) will be eliminated

- [SGLStorage.sol#L50](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol#L50)
- [SGLStorage.sol#L52](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol#L52)

these two can be made into constant[] saving huge amounts of gas
```solidity
uint8 constant[4] public PHASE_2_AMOUNT_PER_USER = [200, 190, 200, 190];
uint8 constant[4] public PHASE_2_DISCOUNT_PER_USER = [50, 40, 40, 33];
```
- [AirdropBroker.sol#L77](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L77)
- [AirdropBroker.sol#L78](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L78)


## 2. state variables should be cached in stack variables rather than re-reading them from storage (the ones not in the bot findings)

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

in #L115 `minimumInterestPerSecond` is just a number that is used before so straight up give the number to `startingInterestPerSecond` instead of using a SLOAD for it. save 100 gas
- [Singularity.sol#L115](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L115)

also in #L117 we can just use the number instead of doing a SLOAD and cast on it. save 102 gas
- [Singularity.sol#L117](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L117)

also in #L141 `maximumTargetUtilization` is just a number and we can use the number instead of doing a SLOAD for it. saves 100 gas
- [Singularity.sol#L141](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L141)

`allowance[from][msg.sender]` will be read in if statement #L77 and if the condition is false it will be read from storage again in #L80 so we should cache it before the #L77 if statement and use the cached version in #L80 (notice that we should change the -= to simple equals). reduces one complex SLOAD. even if the #77 condition is true we only lose 3 gas compared to a big saving otherwise.
- [MarketERC20.sol#L80](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L80)

same logic as the last one for `allowanceBorrow[from][msg.sender]` here
- [MarketERC20.sol#L89](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L89)

in #L96 instead of assigning to `emptyStrategies[address(tapToken_)]` first assign the result to a new stack variable so we can use that stack variable in #L108 to reduce one complex SLOAD to reduce gas usage.
- [Penrose.sol#L108](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L108)

exact logic as the one before for `emptyStrategies[address(wethToken_)]`
- [Penrose.sol#L126](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L126)

also same logic as the ones before for `emptyStrategies[_usdoToken]`
- [Penrose.sol#L306](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L306)

`assetId` is being read from storage both in #L206 and #L217 but its value never changes. so we can cache it before to save 97 gas.
- [SGLCommon.sol#L217](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L217)

same logic for `assetId` in #L234 and #L243
- [SGLCommon.sol#L243](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L243)

`_yieldBoxShares[from][ASSET_SIG]` can be cached before #L245 to possibly save 300 gas if the `share > _yieldBoxShares[from][ASSET_SIG]` condition is false, and if its true there is only loss of 3 gas. in #L248 use simple equals and use the cached version to save gas.
- [SGLCommon.sol#L248](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L248)

`collateralId` is being read in #L24 and #L32 but its not changed. cache it before to save 97 gas.
- [SGLLendingCommon.sol#L32](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L32)

`_yieldBoxShares[from][COLLATERAL_SIG]` will be read in if statement #L50 and if the condition is false it will be read from storage again in #L53 so we should cache it before the #L50 if statement and use the cached version in #L53 (notice that we should change the -= to simple equals). reduces one complex SLOAD. even if the #50 condition is true we only lose 3 gas compared to a big saving otherwise.
- [SGLLendingCommon.sol#L53](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L53)

`assetId` is read both in #L73 and #L79 so cache it before to save 97 gas
- [SGLLendingCommon.sol#L79](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L79)

same for `assetId` again in #L93 and #L95
- [SGLLendingCommon.sol#L95](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L95)

same for `assetId` again in #L110 and #L129
- [SGLLeverage.sol#L129](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L129)

same for `assetId` again in #L158 and #L160 and #L167. here we save 197 gas
- [SGLLeverage.sol#L160](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L160)

usually the condition in line #L40 is true, so `liquidationQueue` will be read twice from storage, so we can cache it before to save 97 gas
- [SGLLiquidation.sol#L41](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L41)

`liquidationQueue` is being read twice from storage in #L136 and #L140 and #L168 and #L197 so we can cache them before to save 297 gas
- [SGLLiquidation.sol#L140](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L140)

`assetId` is being read from storage in #L160 and #L179 and #L193 so we can cache them before to save 197 gas
- [SGLLiquidation.sol#L179](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L179)

`collateralId` is being read from storage in #L169 and #L175 and so we can cache them before to save 97 gas
- [SGLLiquidation.sol#L175](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L175)

`userBorrowPart[user]` is being read from storage at least three times #L226 and #L244 and #L248 (possibly a forth time #L245). caching them before the first one will reduce gas usage by 900 or possibly 1200 gas
- [SGLLiquidation.sol#L244](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L244)

`userCollateralShare[user]` is being read from storage at least three times #L218 and #L260 and #L263(should change to equals instead of -=) (possibly a forth time #L261). caching them before the first one will reduce gas usage by 900 or possibly 1200 gas
- [SGLLiquidation.sol#L260](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L260)

`assetId` is read three time from storage in #L275 #L281 #L282. cache them before to save 197 gas
- [SGLLiquidation.sol#L281](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L281)

`assetId` is read two time from storage in #L333 #L343. cache them before to save 97 gas
- [SGLLiquidation.sol#L343](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L343)

usually `erc20` variable is not equal to address(0) so the else part will happen and `erc20` will be read for a second time, so we can cache it before to most likely save 97 gas with only risking 3 gas much less times.
- [mTapiocaOFT.sol#L148](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L148)

`connectedOFTs[_srcOft][_dstChainId].rebalanceable` can be cached before #L149 because its used twice in #L149 and #L156, so we can use the cached version for them to save 300 gas
- [Balancer.sol#L156](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L156)

`connectedOFTs[_srcOft][_dstChainId].rebalanceable` is being read twice (if we change -= to =) in #L185 and #L210. cache and use the cached version to save 300 gas
- [Balancer.sol#L210](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L210)

cache `connectedOFTs[_srcOft][_dstChainId].rebalanceable + amount` before #L255 and use that cached variable in #L255 and #L260. saves 300 gas
- [Balancer.sol#L260](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L260)

`assets[assetId].tokenType` can be cached before the if statement for a possible 300 gas save. if `assets[assetId].tokenType == TokenType.Native` condition be false the second condition will be read and another SLOAD will take place but we can cache it before to possibly save gas.
- [YieldBox.sol#L435](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L435)

same thing as the last one in these places too
- [YieldBox.sol#L448](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L448)
- [YieldBox.sol#L459](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L459)

`aoTAPCalls[_aoTAPTokenID][cachedEpoch]` can be cached before #L254 to save 300 gas. in line 259 we should use equals instead of += and use the cached version there
- [AirdropBroker.sol#L259](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L259)

`epoch + 1` should be cached before #L288 and the cached version should be used in #L288 and #L293 resulting in 97 gas save.
- [AirdropBroker.sol#L293](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L293)

`paymentTokenBeneficiary` can be cached before require() #L369 because its being read in #L378 as well which is a loop and we are gonna save 100*i gas for doing this
- [AirdropBroker.sol#L369](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L369)

instead of using a SLOAD for the emit of #L104 we should cache `_computeSGLPoolWeights()` before and use the cached version in #L103 and #104 which results in 97 gas save where ever this modifier is used (used 3 times so 300 gas save)
- [TapiocaOptionLiquidityProvision.sol#L104](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L104)

if `hasVotingPower` be true in #L242 `epoch` will be read twice in #L265 and #L286. so we can cache it before the #L242 for a possible 97 gas save but even if `hasVotingPower` is false there we only lose 3 gas which is nothing in comparison.
- [TapiocaOptionBroker.sol#L286](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L286)

if `participation.hasVotingPower` be true in #L311 `epoch` will be read twice in #L329 and #L344. so we can cache it before the #L311 for a possible 97 gas save but even if `participation.hasVotingPower` is false there we only lose 3 gas which is nothing in comparison.
- [TapiocaOptionBroker.sol#L344](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L344)

`epoch + 1` should be cached before #L423 and the cached version should be used in #L423 and #L431 resulting in 97 gas save.
- [TapiocaOptionBroker.sol#L431](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L431)

instead of putting the value inside `epochTAPValuation`, first put it inside a stack variable and give its value to `epochTAPValuation` in another line and use cached version in #L431 saving 97 gas.
- [TapiocaOptionBroker.sol#L431](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L431)

`lastProcessedWeek` can be cached to possibly save 97 gas if the require() #L434 passes
- [twTAP.sol#L434](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L434)

`mintedInWeek[week - 1]` can be cached before #L204 to save gas because its read twice in #L204 and #L207. reduces one complex SLOAD.
- [TapOFT.sol#L207](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L207)

cache `feesPending` before #L118 because its being read from storage in #L118 and #L125 (change -= to equals). this will result in saving 100 gas
- [GlpStrategy.sol#L125](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L125)


## 3. use existing cached version of state var or argument containing the same value

using state variables when there is a argument that contains the same value or a cached version of state var is available will waste 100 gas for a SLOAD.

can use `tapiocaBar_` instead of `penrose` to use 100 less gas becausse using one less SLOAD
- [Singularity.sol#L98](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L98)

instead of `maximumTargetUtilization` we can use the argument `_maximumTargetUtilization` to save 100 gas
- [Singularity.sol#L518](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L518)

`totalBorrow` is read from storage in #L49 and put into `_totalBorrow`. so we can use `_totalBorrow` instaed in #L71 to save 100 gas
- [SGLCommon.sol#L71](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L71)

just use `uint128(_epochTAPValuation)` instead of doing a SLOAD for `epochTAPValuation` to save 100 gas.
- [AirdropBroker.sol#L293](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L293)


## 4. state variables are read inside the loop every time for no reason

some state variables do not change when the loop restarts and the same thing is being read every time, cache them before the loop and use the cached version instead. saves 100*repeat gas

`liquidationMultiplier` 
- [SGLLiquidation.sol#L126](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L126)

`collateralId`
- [SGLLiquidation.sol#L129](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L129)

`liquidationQueue`
- [SGLLiquidation.sol#L136](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L136)
- [SGLLiquidation.sol#L140](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L140)

`epoch` 
- [TapiocaOptionBroker.sol#L572](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L572)

`swapper`
- [ConvexTricryptoStrategy.sol#L197](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L197)


## 5. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables  (also =-) (ones not caught by the bot)
Using the addition operator instead of plus-equals saves gas (13 or 113 each dependant on the usage see [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8))

- [BigBang.sol#L715](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L715)

- [SGLLendingCommon.sol#L47](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L47)

- [SGLLiquidation.sol#L157](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L157)


## 6. Consider merging sequential for loops

Merging multiple for loops within a function in Solidity can enhance efficiency and reduce gas costs, especially when they share a common iterating variable or perform related operations. By minimizing redundant iterations over the same data set, execution becomes more cost-effective. However, while merging can optimize gas usage and simplify logic, it may also increase code complexity. Therefore, careful balance between optimization and maintainability is essential, along with thorough testing to ensure the refactored code behaves as expected.

the for loops of #L550 and #L564 can be merged
- [Penrose.sol#L550](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L550)

the for loops of #L273 and #L280 can be merged 
- [ConvexTricryptoStrategy.sol#L273](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L273)


## 7. Use of emit inside a loop

Emitting an event inside a loop performs a LOG op N times, where N is the loop length. Consider refactoring the code to emit the event only once at the end of loop

- [SGLLiquidation.sol#L134](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L134)
- [SGLLiquidation.sol#L139](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L139)


## 8. Using `this.<fn>()` wastes gas

Calling an external function internally, through the use of `this` wastes the gas overhead of calling an external function (100 gas). Instead, change the function from `external` to `public` , and remove the `this`

- [BaseTapOFT.sol#L312](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L312)


## 9. Unbounded Gas Consumption Risk Due to External Call Recipients

In the context of Solidity, external function calls without a specified gas limit present a significant risk. The callee contract has the potential to consume all the gas allocated to the transaction, causing an undesired revert and disrupt the function's execution. To mitigate this, it's recommended to explicitly set a gas limit when performing external calls using `addr.call`{gas: }. This limits the gas forwarded to the callee, preventing potential pitfalls and offering better control over transaction execution.

- [Penrose.sol#L444](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L444)

- [BaseTOFT.sol#L380](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L380)

- [TapiocaWrapper.sol#L126](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L126)
- [TapiocaWrapper.sol#L141](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L141)

- [BaseTOFTLeverageModule.sol#L280](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280)

- [BaseSwapper.sol#L99](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L99)

- [Multicall3.sol#L51](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L51)
- [Multicall3.sol#L79](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L79)

- [ConvexTricryptoStrategy.sol#L356](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L356)
- [ConvexTricryptoStrategy.sol#L369](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L369)

26 instances in - [MagnetarV2.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L206)


## 10. Use selfbalance() instead of address(this).balance

Use assembly when getting a contract's balance of ETH.

You can use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas. Additionally, you can use balance(address) instead of address.balance() when getting an external contract's balance of ETH.

Saves 15 gas when checking internal balance, 6 for external

- [TapiocaDeployer.sol#L29](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol#L29)

- [USDOLeverageModule.sol#L227](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L227)

- [USDOOptionsModule.sol#L127](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L127)

- [Balancer.sol#L286](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L286)

- [BaseTOFTLeverageModule.sol#L230](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L230)

- [BaseTOFTOptionsModule.sol#L142](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142)

- [BaseTOFTStrategyModule.sol#L225](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L225)

- [BaseTapOFT.sol#L312](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L312)

- [MagnetarMarketModule.sol#L286](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L286)
- [MagnetarMarketModule.sol#L751](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L751)

- [CompoundStrategy.sol#L105](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol#L105)
- [CompoundStrategy.sol#L107](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol#L107)
- [CompoundStrategy.sol#L151](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol#L151)

- [StargateStrategy.sol#L206](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/stargate/StargateStrategy.sol#L206)


## 11. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [MagnetarV2.sol#L202](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L202)
- [MagnetarV2.sol#L973](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L973)
- [MagnetarV2.sol#L1004](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L1004)

- [MagnetarMarketModule.sol#L401](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L401)
- [MagnetarMarketModule.sol#L414](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L414)
- [MagnetarMarketModule.sol#L508](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L508)
- [MagnetarMarketModule.sol#L571](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L571)


- [ConvexTricryptoStrategy.sol#L195](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L195)
- [ConvexTricryptoStrategy.sol#L255](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L255)
- [ConvexTricryptoStrategy.sol#L273](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L273)
- [ConvexTricryptoStrategy.sol#L280](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L280)

- [BigBang.sol#L215](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L215)
- [BigBang.sol#L527](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L527)
- [BigBang.sol#L603](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L603)
- [BigBang.sol#L665](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L665)
- [BigBang.sol#L666](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L666)

- [Penrose.sol#L437](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L437)
- [Penrose.sol#L492](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L492)
- [Penrose.sol#L512](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L512)
- [Penrose.sol#L546](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L546)
- [Penrose.sol#L550](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L550)
- [Penrose.sol#L564](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L564)
- [Penrose.sol#L569](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L569)

- [SGLLiquidation.sol#L44](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L44)
- [SGLLiquidation.sol#L47](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L47)
- [SGLLiquidation.sol#L107](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L107)
- [SGLLiquidation.sol#L337](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L337)
- [SGLLiquidation.sol#L383](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L383)
- [SGLLiquidation.sol#L384](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L384)

- [Singularity.sol#L187](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L187)

- [USDOLeverageModule.sol#L255](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L255)

- [USDOMarketModule.sol#L244](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L244)

- [USDOOptionsModule.sol#L245](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L245)

- [TapiocaWrapper.sol#L98](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L98)
- [TapiocaWrapper.sol#L140](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L140)

- [BaseTOFTLeverageModule.sol#L285](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L285)

- [BaseTOFTMarketModule.sol#L261](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L261)

- [BaseTOFTOptionsModule.sol#L260](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L260)

- [NativeTokenFactory.sol#L129](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L129)
- [NativeTokenFactory.sol#L141](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L141)

- [YieldBox.sol#L309](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L309)
- [YieldBox.sol#L320](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L320)
- [YieldBox.sol#L339](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L339)
- [YieldBox.sol#L345](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L345)

- [AirdropBroker.sol#L332](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L332)
- [AirdropBroker.sol#L336](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L336)
- [AirdropBroker.sol#L375](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L375)

- [TapiocaOptionLiquidityProvision.sol#L136](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L136)
- [TapiocaOptionLiquidityProvision.sol#L308](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L308)
- [TapiocaOptionLiquidityProvision.sol#L339](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L339)

- [TapiocaOptionBroker.sol#L489](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L489)
- [TapiocaOptionBroker.sol#L565](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L565)

- [twTAP.sol#L196](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L196)
- [twTAP.sol#L413](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L413)
- [twTAP.sol#L487](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L487)
- [twTAP.sol#L507](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L507)

- [BaseTapOFT.sol#L228](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L228)
- [BaseTapOFT.sol#L333](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L333)

- [Vesting.sol#L29](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L29)

- [Multicall3.sol#L47](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L47)
- [Multicall3.sol#L70](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L70)


## 12. using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

- [BigBang.sol#L152](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L152)
- [BigBang.sol#L348](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L348)
- [BigBang.sol#L449](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L449)
- [BigBang.sol#L475](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L475)
- [BigBang.sol#L481](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L481)
- [BigBang.sol#L487](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L487)
- [BigBang.sol#L495](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L495)
- [BigBang.sol#L604](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L604)
- [BigBang.sol#L680](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L680)
- [BigBang.sol#L734](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L734)

- [Market.sol#L171](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L171)
- [Market.sol#L182](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L182)
- [Market.sol#L192](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L192)
- [Market.sol#L197](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L197)
- [Market.sol#L202](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L202)
- [Market.sol#L210](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L210)
- [Market.sol#L219](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L219)
- [Market.sol#L228](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L228)
- [Market.sol#L233](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L233)
- [Market.sol#L344](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L344)

- [USDO.sol#L89](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L89)

- [SGLLeverage.sol#L159](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L159)

- [SGLLiquidation.sol#L233](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L233)
- [SGLLiquidation.sol#L338](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L338)
- [SGLLiquidation.sol#L397](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L397)

- [Singularity.sol#L131](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L131)
- [Singularity.sol#L480](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L480)
- [Singularity.sol#L498](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L498)
- [Singularity.sol#L506](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L506)
- [Singularity.sol#L521](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L521)
- [Singularity.sol#L533](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L533)
- [Singularity.sol#L545](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L545)
- [Singularity.sol#L553](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L553)
- [Singularity.sol#L565](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L565)

- [USDOLeverageModule.sol#L119](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L119)

- [USDOMarketModule.sol#L123](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L123)
- [USDOMarketModule.sol#L197](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L197)

- [USDOOptionsModule.sol#L123](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L123)
- [USDOOptionsModule.sol#L216](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L216)

- [BaseTOFT.sol#L364](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L364)

- [Balancer.sol#L149](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L149)

- [BaseTOFTLeverageModule.sol#L135](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L135)

- [BaseTOFTMarketModule.sol#L186](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L186)
- [BaseTOFTMarketModule.sol#L226](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L226)

- [BaseTOFTOptionsModule.sol#L138](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L138)
- [BaseTOFTOptionsModule.sol#L231](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L231)

- [BaseTOFTStrategyModule.sol#L56](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L56)
- [BaseTOFTStrategyModule.sol#L98](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L98)
- [BaseTOFTStrategyModule.sol#L181](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L181)

- [AirdropBroker.sol#L203](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L203)
- [AirdropBroker.sol#L392](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L392)
- [AirdropBroker.sol#L467](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L467)

- [TapiocaOptionLiquidityProvision.sol#L177](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L177)
- [TapiocaOptionLiquidityProvision.sol#L178](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L178)
- [TapiocaOptionLiquidityProvision.sol#L181](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L181)
- [TapiocaOptionLiquidityProvision.sol#L264](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L264)
- [TapiocaOptionLiquidityProvision.sol#L281](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L281)
- [TapiocaOptionLiquidityProvision.sol#L288](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L288)
- [TapiocaOptionLiquidityProvision.sol#L301](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L301)

- [TapiocaOptionBroker.sol#L419](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L419)

- [twTAP.sol#L489](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L489)
- [twTAP.sol#L511](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L511)

- [TapOFT.sol#L201](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L201)
- [TapOFT.sol#L222](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L222)

- [BaseTapOFT.sol#L104](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L104)

- [twAML.sol#L12](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/twAML.sol#L12)

- [Vesting.sol#L68](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L68)
- [Vesting.sol#L131](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L131)
- [Vesting.sol#L134](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L134)
- [Vesting.sol#L152](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L152)

- [BaseSwapper.sol#L127](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L127)
- [BaseSwapper.sol#L131](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L131)
- [BaseSwapper.sol#L136](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L136)


## 13. Use assembly to check for address(0)

saves 6 gas per instance

- [TapiocaDeployer.sol#L44](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol#L44)

- [BaseSwapper.sol#L19](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L19)
- [BaseSwapper.sol#L112](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L112)

- [BigBang.sol#L134](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L134)
- [BigBang.sol#L135](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L135)
- [BigBang.sol#L136](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L136)

- [Market.sol#L177](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L177)
- [Market.sol#L187](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L187)

- [MarketERC20.sol#L144](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L144)
- [MarketERC20.sol#L179](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L179)

- [BaseUSDO.sol#L106](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L106)
- [BaseUSDO.sol#L354](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L354)

- [Penrose.sol#L242](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L242)
- [Penrose.sol#L243](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L243)
- [Penrose.sol#L282](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L282)

- [SGLLiquidation.sol#L40](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L40)

- [Singularity.sol#L101](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L101)
- [Singularity.sol#L102](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L102)
- [Singularity.sol#L103](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L103)
- [Singularity.sol#L581](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L581)
- [Singularity.sol#L586](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L586)
- [Singularity.sol#L591](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L591)
- [Singularity.sol#L611](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L611)

- [BaseTOFT.sol#L371](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L371)
- [BaseTOFT.sol#L396](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L396)

- [mTapiocaOFT.sol#L94](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L94)
- [mTapiocaOFT.sol#L144](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L144)

- [TapiocaOFT.sol#L74](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/TapiocaOFT.sol#L74)

- [Balancer.sol#L112](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L112)
- [Balancer.sol#L127](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L127)
- [Balancer.sol#L128](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L128)
- [Balancer.sol#L142](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L142)
- [Balancer.sol#L201](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L201)
- [Balancer.sol#L225](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L225)

- [BaseTOFTLeverageModule.sol#L272](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272)

- [NativeTokenFactory.sol#L59](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L59)

- [YieldBoxURIBuilder.sol#L107](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxURIBuilder.sol#L107)

- [YieldBox.sol#L317](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L317)
- [YieldBox.sol#L340](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L340)
- [YieldBox.sol#L360](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L360)
- [YieldBox.sol#L382](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L382)

- [aoTAP.sol#L140](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/aoTAP.sol#L140)

- [AirdropBroker.sol#L369](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L369)

- [oTAP.sol#L127](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L127)

- [TapiocaOptionBroker.sol#L483](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L483)

- [TapOFT.sol#L118](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L118)
- [TapOFT.sol#L161](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L161)

- [Vesting.sol#L132](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L132)



## 14. Use hardcoded address instead address(this)

Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's `script.sol` and solmate's `LibRlp.sol` contracts can help achieve this. - [Reference](https://twitter.com/transmissions11/status/1518507047943245824)

- [TapiocaDeployer.sol#L29](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol#L29)
- [TapiocaDeployer.sol#L60](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol#L60)

- [UniswapV3Swapper.sol#L186](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L186)
- [UniswapV3Swapper.sol#L200](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L200)

- [UniswapV2Swapper.sol#L155](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV2Swapper.sol#L155)
- [UniswapV2Swapper.sol#L165](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV2Swapper.sol#L165)

- [CurveSwapper.sol#L134](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L134)
- [CurveSwapper.sol#L158](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L158)
- [CurveSwapper.sol#L163](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L163)

- [BaseSwapper.sol#L155](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L155)
- [BaseSwapper.sol#L156](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L156)
- [BaseSwapper.sol#L162](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L162)

- [BigBang.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol) 16 instances

- [USDO.sol#L68](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L68)
- [USDO.sol#L87](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L87)
- [USDO.sol#L99](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L99)
- [USDO.sol#L101](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L101)

- [Penrose.sol#L515](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L515)
- [Penrose.sol#L536](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L536)

- [SGLCommon.sol#L188](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L188)
- [SGLCommon.sol#L192](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L192)
- [SGLCommon.sol#L243](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L243)

- [SGLLendingCommon.sol#L49](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L49)
- [SGLLendingCommon.sol#L79](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L79)

- [SGLLeverage.sol#L47](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L47)
- [SGLLeverage.sol#L71](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L71)
- [SGLLeverage.sol#L74](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L74)
- [SGLLeverage.sol#L75](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L75)

- [SGLLiquidation.sol#L167](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L167)
- [SGLLiquidation.sol#L179](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L179)
- [SGLLiquidation.sol#L193](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L193)
- [SGLLiquidation.sol#L198](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L198)
- [SGLLiquidation.sol#L275](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L275)
- [SGLLiquidation.sol#L281](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L281)
- [SGLLiquidation.sol#L282](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L282)
- [SGLLiquidation.sol#L288](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L288)
- [SGLLiquidation.sol#L331](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L331)
- [SGLLiquidation.sol#L350](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L350)

- [Singularity.sol#L188](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L188)

- [USDOLeverageModule.sol#L161](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L161)
- [USDOLeverageModule.sol#L164](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L164)
- [USDOLeverageModule.sol#L167](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L167)
- [USDOLeverageModule.sol#L182](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L182)
- [USDOLeverageModule.sol#L198](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L198)
- [USDOLeverageModule.sol#L201](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L201)
- [USDOLeverageModule.sol#L212](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L212)
- [USDOLeverageModule.sol#L219](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L219)
- [USDOLeverageModule.sol#L220](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L220)
- [USDOLeverageModule.sol#L227](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L227)
- [USDOLeverageModule.sol#L229](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L229)

- [USDOMarketModule.sol#L160](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L160)
- [USDOMarketModule.sol#L163](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L163)
- [USDOMarketModule.sol#L166](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L166)
- [USDOMarketModule.sol#L180](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L180)

- [USDOOptionsModule.sol#L127](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L127)
- [USDOOptionsModule.sol#L162](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L162)
- [USDOOptionsModule.sol#L167](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L167)
- [USDOOptionsModule.sol#L172](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L172)
- [USDOOptionsModule.sol#L191](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L191)
- [USDOOptionsModule.sol#L227](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L227)

- [BaseTOFT.sol#L359](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L359)
- [BaseTOFT.sol#L460](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L460)

- [TapiocaWrapper.sol#L198](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L198)
- [TapiocaWrapper.sol#L216](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L216)

- [Balancer.sol#L286](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L286)
- [Balancer.sol#L305](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L305)

- [BaseTOFTLeverageModule.sol#L176](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L176)
- [BaseTOFTLeverageModule.sol#L179](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L179)
- [BaseTOFTLeverageModule.sol#L182](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L182)
- [BaseTOFTLeverageModule.sol#L197](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L197)
- [BaseTOFTLeverageModule.sol#L212](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L212)
- [BaseTOFTLeverageModule.sol#L221](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L221)
- [BaseTOFTLeverageModule.sol#L230](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L230)
- [BaseTOFTLeverageModule.sol#L232](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L232)

- [BaseTOFTMarketModule.sol#L152](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L152)
- [BaseTOFTMarketModule.sol#L155](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L155)
- [BaseTOFTMarketModule.sol#L158](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L158)
- [BaseTOFTMarketModule.sol#L172](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L172)

- [BaseTOFTOptionsModule.sol#L142](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142)
- [BaseTOFTOptionsModule.sol#L177](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L177)
- [BaseTOFTOptionsModule.sol#L182](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L182)
- [BaseTOFTOptionsModule.sol#L187](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L187)
- [BaseTOFTOptionsModule.sol#L206](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L206)
- [BaseTOFTOptionsModule.sol#L242](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L242)

- [YieldBox.sol#L148](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L148)
- [YieldBox.sol#L361](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L361)
- [YieldBox.sol#L383](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L383)
- [YieldBox.sol#L489](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L489)

- [AirdropBroker.sol#L379](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L379)
- [AirdropBroker.sol#L511](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L511)

- [TapiocaOptionLiquidityProvision.sol#L186](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L186)
- [TapiocaOptionLiquidityProvision.sol#L242](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L242)

- [TapiocaOptionBroker.sol#L230](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L230)
- [TapiocaOptionBroker.sol#L342](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L342)
- [TapiocaOptionBroker.sol#L493](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L493)
- [TapiocaOptionBroker.sol#L532](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L532)

- [twTAP.sol#L260](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L260)
- [twTAP.sol#L448](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L448)

- [BaseTapOFT.sol#L134](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L134)
- [BaseTapOFT.sol#L144](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L144)
- [BaseTapOFT.sol#L147](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L147)
- [BaseTapOFT.sol#L232](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L232)
- [BaseTapOFT.sol#L235](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L235)
- [BaseTapOFT.sol#L312](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L312)
- [BaseTapOFT.sol#L313](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol#L313)

- [LTap.sol#L42](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/LTap.sol#L42)


## 15. The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement

Using a double if statement instead of logical AND (&&) can provide similar short-circuiting behavior whereas double if is slightly more efficient.

- [BaseUSDO.sol#L370](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L370)

- [BoringMath.sol#L27](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/BoringMath.sol#L27)

- [YieldBoxRebase.sol#L35](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxRebase.sol#L35)
- [YieldBoxRebase.sol#L58](https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxRebase.sol#L58)

- [BaseTOFT.sol#L412](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L412)

- [TapiocaWrapper.sol#L121](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L121)
- [TapiocaWrapper.sol#L144](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L144)

- [Balancer.sol#L226](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L226)

- [TapiocaOptionLiquidityProvision.sol#L315](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L315)

- [AaveStrategy.sol#L171](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/aave/AaveStrategy.sol#L171)

- [MagnetarV2.sol#L1046](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L1046)

- [MagnetarMarketModule.sol#L402](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402)
- [MagnetarMarketModule.sol#L612](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L612)


## 16. Divisions which do not divide by -X cannot overflow or overflow so such operations can be unchecked to save gas

Make such found divisions are unchecked when ensured it is safe to do so

- [BigBang.sol#L188](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L188)
- [BigBang.sol#L521](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L521)
- [BigBang.sol#L645](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L645)
- [BigBang.sol#L646](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L646)
- [BigBang.sol#L747](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L747)
- [BigBang.sol#L813](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L813)

- [Market.sol#L393](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L393)
- [Market.sol#L418](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L418)
- [Market.sol#L439](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L439)
- [Market.sol#L489](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L489)

- [SGLCommon.sol#L106](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L106)

- [SGLLendingCommon.sol#L63](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L63)

- [SGLLiquidation.sol#L84](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L84)
- [SGLLiquidation.sol#L130](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L130)
- [SGLLiquidation.sol#L182](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L182)
- [SGLLiquidation.sol#L257](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L257)
- [SGLLiquidation.sol#L278](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L278)
- [SGLLiquidation.sol#L279](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L279)

- [Balancer.sol#L339](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L339)

- [AirdropBroker.sol#L535](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L535)

- [TapiocaOptionBroker.sol#L555](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L555)

- [twTAP.sol#L151](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L151)
- [twTAP.sol#L236](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L236)
- [twTAP.sol#L323](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L323)

- [TapOFT.sol#L254](https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L254)


## 17. Use assembly hashing instead of keccak256()

From a gas standpoint, the assembly version of the keccak256 hashing function can be more efficient than the high-level Solidity version. This is because Solidity has additional overhead when handling function calls and memory management, which can increase the gas cost.
