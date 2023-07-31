|ID|Issues|Instances|
|---|:---|:---:|
| [G-00] | Use `assembly` to write address storage values | 16 |
| [G-01] | Do not calculate constants | 9 |
| [G-02] | Pre-calculate the results into `constant` instead of calculate `keccak256`/`abi.encode**` in runtime. | 3 |
| [G-03] | Use `calldata` instead of `memory` for function parameters | 40 |
| [G-04] | Use indexed events for value types as they are less costly compared to non-indexed ones | 18 |
| [G-05] | `!= 0` is less gas than `> 0` for unsigned integers | 17 |
| [G-06] | Use `selfbalance()` instead of `address(this).balance` | 11 |
| [G-07] | Amounts should be checked for `0` before calling a `transfer` | 10 |
| [G-08] | Remove unused parameter variables | 3 |
| [G-09] | Use `string.concat()` on string instead of `abi.encodePacked()` to save gas | 2 |
| [G-10] | Use assembly to check for `address(0)` | 24 |


## [G-00] Use `assembly` to write address storage values

### description:

Where it does not affect readability,
using assembly for simple setters allows to save gas not only on deployment,
but also on function calls.

**There are `16` instances of this issue:**

- [owner = \_owner](/tap-token-audit/contracts/option-airdrop/aoTAP.sol#L42) should use `assembly` update address to save gas.

- [owner = \_owner](/tap-token-audit/contracts/Vesting.sol#L72) should use `assembly` update address to save gas.

- [owner = \_owner](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L75) should use `assembly` update address to save gas.

- [paymentTokenBeneficiary = \_paymentTokenBeneficiary](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L89) should use `assembly` update address to save gas.

- [owner = \_owner](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L94) should use `assembly` update address to save gas.

- [paymentTokenBeneficiary = \_paymentTokenBeneficiary](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L105) should use `assembly` update address to save gas.

- [owner = \_owner](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L109) should use `assembly` update address to save gas.

- [broker = msg.sender](/tap-token-audit/contracts/options/oTAP.sol#L128) should use `assembly` update address to save gas.

- [broker = msg.sender](/tap-token-audit/contracts/option-airdrop/aoTAP.sol#L141) should use `assembly` update address to save gas.

- [minter = \_minter](/tap-token-audit/contracts/tokens/TapOFT.sol#L163) should use `assembly` update address to save gas.

- [paymentTokenBeneficiary = \_paymentTokenBeneficiary](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L360) should use `assembly` update address to save gas.

- [paymentTokenBeneficiary = \_paymentTokenBeneficiary](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L474) should use `assembly` update address to save gas.

- [stgEthPool = \_stgEthPool](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L79) should use `assembly` update address to save gas.

- [feeRecipient = owner](/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L82) should use `assembly` update address to save gas.

- [feeRecipient = recipient](/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L114) should use `assembly` update address to save gas.

- [erc20 = \_erc20](/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol#L61) should use `assembly` update address to save gas.

### recommendation:

Using `assembly` update address to save gas.

For example:

```
contract Contract1 {
    address owner;

    function assemblyUpdateOwner(address newOwner) public {
        assembly {
            sstore(owner.slot, newOwner)
        }
    }
}
```

### locations:

- /tap-token-audit/contracts/option-airdrop/aoTAP.sol#L42
- /tap-token-audit/contracts/Vesting.sol#L72
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L75
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L89
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L94
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L105
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L109
- /tap-token-audit/contracts/options/oTAP.sol#L128
- /tap-token-audit/contracts/option-airdrop/aoTAP.sol#L141
- /tap-token-audit/contracts/tokens/TapOFT.sol#L163
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L360
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L474
- /tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L79
- /tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L82
- /tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L114
- /tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol#L61

### severity:

Optimization

## [G-01] Do not calculate constants

### description:

Due to how constant variables are implemented (replacements at compile-time),
an expression assigned to a constant variable is recomputed each time that the variable is used,
which wastes some gas.

See: [ethereum/solidity#9232](https://github.com/ethereum/solidity/issues/9232):

> each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)

> since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library )

**There are `9` instances of this issue:**

- [TapOFT.INITIAL_SUPPLY](/tap-token-audit/contracts/tokens/TapOFT.sol#L41) should use hardcode instead of calculation.

- [AirdropBroker.PHASE_1_DISCOUNT](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L69) should use hardcode instead of calculation.

- [TapiocaOptionBroker.dMAX](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L76) should use hardcode instead of calculation.

- [TapiocaOptionBroker.dMIN](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L77) should use hardcode instead of calculation.

- [TwTAP.dMAX](/tap-token-audit/contracts/governance/twTAP.sol#L82) should use hardcode instead of calculation.

- [TwTAP.dMIN](/tap-token-audit/contracts/governance/twTAP.sol#L83) should use hardcode instead of calculation.

- [AirdropBroker.PHASE_3_DISCOUNT](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L85) should use hardcode instead of calculation.

- [AirdropBroker.PHASE_4_DISCOUNT](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L93) should use hardcode instead of calculation.

- [TwTAP.DIST_PRECISION](/tap-token-audit/contracts/governance/twTAP.sol#L95) should use hardcode instead of calculation.

### recommendation:

Pre-calculate the results(hardcode) instead of calculation in runtime.

### locations:

- /tap-token-audit/contracts/tokens/TapOFT.sol#L41
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L69
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L76
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L77
- /tap-token-audit/contracts/governance/twTAP.sol#L82
- /tap-token-audit/contracts/governance/twTAP.sol#L83
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L85
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L93
- /tap-token-audit/contracts/governance/twTAP.sol#L95

### severity:

Optimization

## [G-02] Pre-calculate the results into `constant` instead of calculate `keccak256`/`abi.encode**` in runtime.

### description:

It should be saved to an `constant` variable, and the `constant` used instead.
If the hash is being used as a part of a function selector,
the cast to bytes4 should only be Pre-calculated

**There is `3` instance of this issue:**

- [bytes4(keccak256(bytes)(onERC1155Received(address,address,uint256,uint256,bytes)))](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L364-L369) should use pre-calculate results instead of calculation in runtime.

- [(successEmtptyApproval) = token.call(abi.encodeWithSelector(bytes4(keccak256(bytes)(approve(address,uint256))),to,0))](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L356-L362) should use pre-calculate results instead of calculation in runtime.

- [(success,data) = token.call(abi.encodeWithSelector(bytes4(keccak256(bytes)(approve(address,uint256))),to,value))](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L369-L375) should use pre-calculate results instead of calculation in runtime.

### recommendation:

Pre-calculate the results(hardcode) into `constant` instead of calculate `keccak256`/`abi.encode**` in runtime.

### locations:

- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L364-L369
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L356-L362
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L369-L375

### severity:

Optimization

## [G-03] Use `calldata` instead of `memory` for function parameters

### description:

On external functions, when using the `memory` keyword with a function argument, what's happening is a `memory` acts as an intermediate.

When the function gets called externally, the array values are kept in `calldata` and copied to memory during ABI decoding (using the opcode `calldataload` and `mstore`).
And during the for loop, the values in the array are accessed in memory using a `mload`. That is inefficient. Reading directly from `calldata` using `calldataload` instead of going via `memory` saves the gas from the intermediate memory operations that carry the values.

More detail see [this](https://ethereum.stackexchange.com/questions/74442/when-should-i-use-calldata-and-when-should-i-use-memory)

**There are `40` instances of this issue:**

- [BaseTapOFT.claimRewards(address,uint256,address[],uint16,address,bytes,IRewardClaimSendFromParams[])](/tap-token-audit/contracts/tokens/BaseTapOFT.sol#L163-L196) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTapOFT.claimRewards(address,uint256,address[],uint16,address,bytes,IRewardClaimSendFromParams[]).rewardTokens](/tap-token-audit/contracts/tokens/BaseTapOFT.sol#L166)

- [TwTAP.claimAndSendRewards(uint256,IERC20[])](/tap-token-audit/contracts/governance/twTAP.sol#L361-L367) read-only `memory` parameters below should be changed to `calldata` :

  - [TwTAP.claimAndSendRewards(uint256,IERC20[]).\_rewardTokens](/tap-token-audit/contracts/governance/twTAP.sol#L363)

- [USDOLeverageModule.multiHop(bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L93-L131) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOLeverageModule.multiHop(bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L93)

- [USDOOptionsModule.sendFromDestination(bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L103-L136) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOOptionsModule.sendFromDestination(bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L103)

- [USDOMarketModule.remove(bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L104-L132) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOMarketModule.remove(bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L104)

- [MarketERC20.constructor(string)](/tapioca-bar-audit/contracts/markets/MarketERC20.sol#L109) read-only `memory` parameters below should be changed to `calldata` :

  - [MarketERC20.constructor(string).name](/tapioca-bar-audit/contracts/markets/MarketERC20.sol#L109)

- [USDOLeverageModule.leverageUp(address,uint16,bytes,uint64,bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L133-L188) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOLeverageModule.leverageUp(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L136)
  - [USDOLeverageModule.leverageUp(address,uint16,bytes,uint64,bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L138)

- [USDOMarketModule.lend(address,uint16,bytes,uint64,bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L134-L189) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOMarketModule.lend(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L137)
  - [USDOMarketModule.lend(address,uint16,bytes,uint64,bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L139)

- [USDOOptionsModule.exercise(address,uint16,bytes,uint64,bytes)](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L138-L204) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOOptionsModule.exercise(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L141)
  - [USDOOptionsModule.exercise(address,uint16,bytes,uint64,bytes).\_payload](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L143)

- [USDOLeverageModule.leverageUpInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address)](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L190-L252) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOLeverageModule.leverageUpInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).swapData](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L192)
  - [USDOLeverageModule.leverageUpInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).externalData](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L193)
  - [USDOLeverageModule.leverageUpInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).lzData](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L194)

- [USDOMarketModule.lendInternal(address,IUSDOBase.ILendOrRepayParams,ICommonData.IApproval[],ICommonData.IWithdrawParams)](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L191-L241) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOMarketModule.lendInternal(address,IUSDOBase.ILendOrRepayParams,ICommonData.IApproval[],ICommonData.IWithdrawParams).lendParams](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L193)
  - [USDOMarketModule.lendInternal(address,IUSDOBase.ILendOrRepayParams,ICommonData.IApproval[],ICommonData.IWithdrawParams).approvals](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L194)
  - [USDOMarketModule.lendInternal(address,IUSDOBase.ILendOrRepayParams,ICommonData.IApproval[],ICommonData.IWithdrawParams).withdrawParams](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L195)

- [USDOOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[])](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L206-L242) read-only `memory` parameters below should be changed to `calldata` :

  - [USDOOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[]).tapSendData](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L212-L213)
  - [USDOOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[]).approvals](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L214)

- [BaseUSDO.initMultiHopBuy(address,uint256,uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageLZData,IUSDOBase.ILeverageExternalContractsData,bytes,ICommonData.IApproval[])](/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L215-L240) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseUSDO.initMultiHopBuy(address,uint256,uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageLZData,IUSDOBase.ILeverageExternalContractsData,bytes,ICommonData.IApproval[]).approvals](/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L223)

- [Penrose.executeMarketFn(address[],bytes[],bool)](/tapioca-bar-audit/contracts/Penrose.sol#L424-L450) read-only `memory` parameters below should be changed to `calldata` :

  - [Penrose.executeMarketFn(address[],bytes[],bool).data](/tapioca-bar-audit/contracts/Penrose.sol#L426)

- [Penrose.\_getMasterContractLength(IPenrose.MasterContract[])](/tapioca-bar-audit/contracts/Penrose.sol#L542-L577) read-only `memory` parameters below should be changed to `calldata` :

  - [Penrose.\_getMasterContractLength(IPenrose.MasterContract[]).array](/tapioca-bar-audit/contracts/Penrose.sol#L543)

- [CompoundStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L97) read-only `memory` parameters below should be changed to `calldata` :

  - [CompoundStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L97)

- [YearnStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L98) read-only `memory` parameters below should be changed to `calldata` :

  - [YearnStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L98)

- [LidoEthStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L101) read-only `memory` parameters below should be changed to `calldata` :

  - [LidoEthStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L101)

- [BalancerStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L117) read-only `memory` parameters below should be changed to `calldata` :

  - [BalancerStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L117)

- [AaveStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L138-L206) read-only `memory` parameters below should be changed to `calldata` :

  - [AaveStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L138)

- [TricryptoNativeStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L151-L179) read-only `memory` parameters below should be changed to `calldata` :

  - [TricryptoNativeStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L151)

- [StargateStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L159-L190) read-only `memory` parameters below should be changed to `calldata` :

  - [StargateStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L159)

- [TricryptoLPStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L160-L196) read-only `memory` parameters below should be changed to `calldata` :

  - [TricryptoLPStrategy.compound(bytes).](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L160)

- [ConvexTricryptoStrategy.compound(bytes)](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L189-L215) read-only `memory` parameters below should be changed to `calldata` :

  - [ConvexTricryptoStrategy.compound(bytes).data](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L189)

- [BaseTOFTStrategyModule.retrieveFromStrategy(address,uint256,uint256,uint256,uint16,address,bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L89-L120) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTStrategyModule.retrieveFromStrategy(address,uint256,uint256,uint256,uint16,address,bytes).airdropAdapterParam](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L96)

- [BaseTOFTLeverageModule.multiHop(bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L111-L146) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTLeverageModule.multiHop(bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L111)

- [BaseTOFTOptionsModule.sendFromDestination(bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L118-L151) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTOptionsModule.sendFromDestination(bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L118)

- [BaseTOFTStrategyModule.strategyDeposit(address,uint16,bytes,uint64,bytes,IERC20)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L122-L171) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTStrategyModule.strategyDeposit(address,uint16,bytes,uint64,bytes,IERC20).\_srcAddress](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L125)
  - [BaseTOFTStrategyModule.strategyDeposit(address,uint16,bytes,uint64,bytes,IERC20).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L127)

- [BaseTOFTMarketModule.borrow(address,uint16,bytes,uint64,bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L126-L178) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTMarketModule.borrow(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L129)
  - [BaseTOFTMarketModule.borrow(address,uint16,bytes,uint64,bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L131)

- [BaseTOFTLeverageModule.leverageDown(address,uint16,bytes,uint64,bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L148-L203) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTLeverageModule.leverageDown(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L151)
  - [BaseTOFTLeverageModule.leverageDown(address,uint16,bytes,uint64,bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L153)

- [BaseTOFTOptionsModule.exercise(address,uint16,bytes,uint64,bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L153-L219) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTOptionsModule.exercise(address,uint16,bytes,uint64,bytes).\_srcAddress](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L156)
  - [BaseTOFTOptionsModule.exercise(address,uint16,bytes,uint64,bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L158)

- [BaseTOFT.initMultiSell(address,uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageLZData,IUSDOBase.ILeverageExternalContractsData,bytes,ICommonData.IApproval[])](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L156-L179) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFT.initMultiSell(address,uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageLZData,IUSDOBase.ILeverageExternalContractsData,bytes,ICommonData.IApproval[]).approvals](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L163)

- [Balancer.rebalance(address,uint16,uint256,uint256,bytes)](/tapiocaz-audit/contracts/Balancer.sol#L172-L212) read-only `memory` parameters below should be changed to `calldata` :

  - [Balancer.rebalance(address,uint16,uint256,uint256,bytes).\_ercData](/tapiocaz-audit/contracts/Balancer.sol#L177)

- [BaseTOFTMarketModule.borrowInternal(bytes32,ITapiocaOFT.IBorrowParams,ICommonData.IWithdrawParams,ICommonData.IApproval[])](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L180-L202) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTMarketModule.borrowInternal(bytes32,ITapiocaOFT.IBorrowParams,ICommonData.IWithdrawParams,ICommonData.IApproval[]).borrowParams](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L182)
  - [BaseTOFTMarketModule.borrowInternal(bytes32,ITapiocaOFT.IBorrowParams,ICommonData.IWithdrawParams,ICommonData.IApproval[]).withdrawParams](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L183)
  - [BaseTOFTMarketModule.borrowInternal(bytes32,ITapiocaOFT.IBorrowParams,ICommonData.IWithdrawParams,ICommonData.IApproval[]).approvals](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L184)

- [BaseTOFTStrategyModule.strategyWithdraw(uint16,bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L188-L235) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTStrategyModule.strategyWithdraw(uint16,bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L190)

- [BaseTOFTMarketModule.remove(bytes)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L204-L258) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTMarketModule.remove(bytes).\_payload](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L204)

- [BaseTOFTLeverageModule.leverageDownInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L205-L267) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTLeverageModule.leverageDownInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).swapData](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L207)
  - [BaseTOFTLeverageModule.leverageDownInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).externalData](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L208)
  - [BaseTOFTLeverageModule.leverageDownInternal(uint256,IUSDOBase.ILeverageSwapData,IUSDOBase.ILeverageExternalContractsData,IUSDOBase.ILeverageLZData,address).lzData](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L209)

- [Balancer.initConnectedOFT(address,uint16,address,bytes)](/tapiocaz-audit/contracts/Balancer.sol#L219-L244) read-only `memory` parameters below should be changed to `calldata` :

  - [Balancer.initConnectedOFT(address,uint16,address,bytes).\_ercData](/tapiocaz-audit/contracts/Balancer.sol#L223)

- [BaseTOFTOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[])](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L221-L257) read-only `memory` parameters below should be changed to `calldata` :

  - [BaseTOFTOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[]).tapSendData](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L227-L228)
  - [BaseTOFTOptionsModule.exerciseInternal(address,uint256,address,uint256,address,ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,ICommonData.IApproval[]).approvals](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L229)

- [BaseTOFT.retrieveFromStrategy(address,uint256,uint256,uint256,uint16,address,bytes)](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L256-L279) read-only `memory` parameters below should be changed to `calldata` :
  - [BaseTOFT.retrieveFromStrategy(address,uint256,uint256,uint256,uint16,address,bytes).airdropAdapterParam](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L263)

### recommendation:

Use `calldata` instead of `memory` for external functions where the function argument is read-only.

### locations:

- /tap-token-audit/contracts/tokens/BaseTapOFT.sol#L163-L196
- /tap-token-audit/contracts/governance/twTAP.sol#L361-L367
- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L93-L131
- /tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L103-L136
- /tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L104-L132
- /tapioca-bar-audit/contracts/markets/MarketERC20.sol#L109
- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L133-L188
- /tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L134-L189
- /tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L138-L204
- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L190-L252
- /tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L191-L241
- /tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L206-L242
- /tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L215-L240
- /tapioca-bar-audit/contracts/Penrose.sol#L424-L450
- /tapioca-bar-audit/contracts/Penrose.sol#L542-L577
- /tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L97
- /tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L98
- /tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L101
- /tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L117
- /tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L138-L206
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L151-L179
- /tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L159-L190
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L160-L196
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L189-L215
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L89-L120
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L111-L146
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L118-L151
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L122-L171
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L126-L178
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L148-L203
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L153-L219
- /tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L156-L179
- /tapiocaz-audit/contracts/Balancer.sol#L172-L212
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L180-L202
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L188-L235
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L204-L258
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L205-L267
- /tapiocaz-audit/contracts/Balancer.sol#L219-L244
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L221-L257
- /tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L256-L279

### severity:

Optimization

## [G-04] Use indexed events for value types as they are less costly compared to non-indexed ones

### description:

Using the `indexed` keyword for [value types](https://docs.soliditylang.org/en/v0.8.20/types.html#value-types) (`bool/int/address/string/bytes`) saves gas costs, as seen in [this example](https://gist.github.com/0xxfu/c292a65ecb61cae6fd2090366ea0877e).

However, this is only the case for value types, whereas indexing [reference types](https://docs.soliditylang.org/en/v0.8.20/types.html#reference-types) (`array/struct`) are more expensive than their unindexed version.

**There are `18` instances of this issue (Excluded instances in `bot report`):**

- The following variables should be indexed in [Vesting.UserRegistered(address,uint256)](/tap-token-audit/contracts/Vesting.sol#L60):

  - [amount](/tap-token-audit/contracts/Vesting.sol#L60)

- The following variables should be indexed in [Vesting.Claimed(address,uint256)](/tap-token-audit/contracts/Vesting.sol#L62):

  - [amount](/tap-token-audit/contracts/Vesting.sol#L62)

- The following variables should be indexed in [TapOFT.Emitted(uint256,uint256)](/tap-token-audit/contracts/tokens/TapOFT.sol#L78):

  - [week](/tap-token-audit/contracts/tokens/TapOFT.sol#L78)

  - [amount](/tap-token-audit/contracts/tokens/TapOFT.sol#L78)

- The following variables should be indexed in [TapOFT.Minted(address,address,uint256)](/tap-token-audit/contracts/tokens/TapOFT.sol#L80):

  - [\_amount](/tap-token-audit/contracts/tokens/TapOFT.sol#L80)

- The following variables should be indexed in [TapOFT.Burned(address,uint256)](/tap-token-audit/contracts/tokens/TapOFT.sol#L82):

  - [\_amount](/tap-token-audit/contracts/tokens/TapOFT.sol#L82)

- The following variables should be indexed in [TapOFT.GovernanceChainIdentifierUpdated(uint256,uint256)](/tap-token-audit/contracts/tokens/TapOFT.sol#L84):

  - [\_old](/tap-token-audit/contracts/tokens/TapOFT.sol#L84)

  - [\_new](/tap-token-audit/contracts/tokens/TapOFT.sol#L84)

- The following variables should be indexed in [TapOFT.PausedUpdated(bool,bool)](/tap-token-audit/contracts/tokens/TapOFT.sol#L86):

  - [newState](/tap-token-audit/contracts/tokens/TapOFT.sol#L86)

  - [oldState](/tap-token-audit/contracts/tokens/TapOFT.sol#L86)

- The following variables should be indexed in [TapiocaOptionLiquidityProvision.UpdateTotalSingularityPoolWeights(uint256)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L91-L93):

  - [totalSingularityPoolWeights](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L92)

- The following variables should be indexed in [TapiocaOptionBroker.Participate(uint256,uint256,uint256,LockPosition,uint256)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L100-L106):

  - [totalDeposited](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L103)

  - [discount](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L105)

- The following variables should be indexed in [TapiocaOptionBroker.AMLDivergence(uint256,uint256,uint256,uint256)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L107-L112):

  - [cumulative](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L109)

  - [totalParticipants](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L111)

  - [averageMagnitude](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L110)

- The following variables should be indexed in [TapiocaOptionBroker.NewEpoch(uint256,uint256,uint256)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L120-L124):

  - [epochTAPValuation](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L123)

  - [extractedTAP](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L122)

- The following variables should be indexed in [AirdropBroker.NewEpoch(uint256,uint256)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L123):

  - [epochTAPValuation](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L123)

- The following variables should be indexed in [AirdropBroker.SetPaymentToken(ERC20,IOracle,bytes)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L125):

  - [oracleData](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L125)

- The following variables should be indexed in [AirdropBroker.SetTapOracle(IOracle,bytes)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L126):

  - [oracleData](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L126)

- The following variables should be indexed in [TapiocaOptionBroker.SetPaymentToken(ERC20,IOracle,bytes)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L130):

  - [oracleData](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L130)

- The following variables should be indexed in [TapiocaOptionBroker.SetTapOracle(IOracle,bytes)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L131):

  - [oracleData](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L131)

- The following variables should be indexed in [TwTAP.Participate(address,uint256,uint256)](/tap-token-audit/contracts/governance/twTAP.sol#L134-L138):

  - [multiplier](/tap-token-audit/contracts/governance/twTAP.sol#L137)

  - [tapAmount](/tap-token-audit/contracts/governance/twTAP.sol#L136)

- The following variables should be indexed in [TwTAP.AMLDivergence(uint256,uint256,uint256)](/tap-token-audit/contracts/governance/twTAP.sol#L139-L143):

  - [cumulative](/tap-token-audit/contracts/governance/twTAP.sol#L140)

  - [totalParticipants](/tap-token-audit/contracts/governance/twTAP.sol#L142)

  - [averageMagnitude](/tap-token-audit/contracts/governance/twTAP.sol#L141)

### recommendation:

Using the `indexed` keyword for values types `bool/int/address/string/bytes` in event

### locations:

- /tap-token-audit/contracts/Vesting.sol#L60
- /tap-token-audit/contracts/Vesting.sol#L62
- /tap-token-audit/contracts/tokens/TapOFT.sol#L78
- /tap-token-audit/contracts/tokens/TapOFT.sol#L80
- /tap-token-audit/contracts/tokens/TapOFT.sol#L82
- /tap-token-audit/contracts/tokens/TapOFT.sol#L84
- /tap-token-audit/contracts/tokens/TapOFT.sol#L86
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L91-L93
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L100-L106
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L107-L112
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L120-L124
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L123
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L125
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L126
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L130
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L131
- /tap-token-audit/contracts/governance/twTAP.sol#L134-L138
- /tap-token-audit/contracts/governance/twTAP.sol#L139-L143

### severity:

Optimization

## [G-05] `!= 0` is less gas than `> 0` for unsigned integers

### description:

`!= 0` costs less gas compared to `> 0` for unsigned integers in require statements
with the optimizer enabled (6 gas)

While it may seem that `> 0` is cheaper than `!=`, this is only true without the
optimizer enabled and outside a require statement.
If you enable the optimizer at 10k and you’re in a `require` statement,
this will save gas.

**There are `17` instances of this issue:**

- [require(bool)(denominator > 0)](/tap-token-audit/contracts/twAML.sol#L12) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_duration > 0,Vesting: no vesting)](/tap-token-audit/contracts/Vesting.sol#L68) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(duration > 0,TapOFT: Small duration)](/tap-token-audit/contracts/tokens/BaseTapOFT.sol#L104) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [start > 0](/tap-token-audit/contracts/Vesting.sol#L131) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [start > 0](/tap-token-audit/contracts/Vesting.sol#L152) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_lockDuration > 0,tOLP: lock duration must be > 0)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L177) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_amount > 0,tOLP: amount must be > 0)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L178) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(sglAssetID > 0,tOLP: singularity not active)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L181) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(cachedEpoch > 0,adb: Airdrop not started)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L203) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_amount > 0,amount not valid)](/tap-token-audit/contracts/tokens/TapOFT.sol#L222) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(assetID > 0,tOLP: invalid asset ID)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L281) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [weight > 0](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L288) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(sglAssetID > 0,tOLP: not registered)](/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L301) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_eligibleAmount > 0,adb: Not eligible)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L392) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [require(bool,string)(\_eligibleAmount > 0,adb: Not eligible)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L467) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [amount > 0](/tap-token-audit/contracts/governance/twTAP.sol#L489) should use `!= 0` instead of `> 0` for unsigned integer comparison.

- [amount > 0](/tap-token-audit/contracts/governance/twTAP.sol#L511) should use `!= 0` instead of `> 0` for unsigned integer comparison.

### recommendation:

Use `!= 0` instead of `> 0` for unsigned integer comparison.

### locations:

- /tap-token-audit/contracts/twAML.sol#L12
- /tap-token-audit/contracts/Vesting.sol#L68
- /tap-token-audit/contracts/tokens/BaseTapOFT.sol#L104
- /tap-token-audit/contracts/Vesting.sol#L131
- /tap-token-audit/contracts/Vesting.sol#L152
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L177
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L178
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L181
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L203
- /tap-token-audit/contracts/tokens/TapOFT.sol#L222
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L281
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L288
- /tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol#L301
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L392
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L467
- /tap-token-audit/contracts/governance/twTAP.sol#L489
- /tap-token-audit/contracts/governance/twTAP.sol#L511

### severity:

Optimization

## [G-06] Use `selfbalance()` instead of `address(this).balance`

### description:

You can use `selfbalance()` instead of `address(this).balance` when
getting your contract’s balance of ETH to save gas.
Additionally, you can use `balance(address)` instead of address.balance() when
getting an external contract’s balance of ETH.

**There are `11` instances of this issue:**

- Should use `selfbalance()` instead of [this.sendFrom{value: address(this).balance}(address(this),\_srcChainId,LzLib.addressToBytes32(to),\_amount,twTapSendBackAdapterParams)](/tap-token-audit/contracts/tokens/BaseTapOFT.sol#L312-L318)

- Should use `selfbalance()` instead of [ISendFrom(address(this)).sendFrom{value: address(this).balance}(from,lzDstChainId,LzLib.addressToBytes32(from),amount,callParams)](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L127-L133)

- Should use `selfbalance()` instead of [ITapiocaOFT(externalData.tOft).sendToYBAndBorrow{value: address(this).balance}(address(this),leverageFor,lzData.lzSrcChainId,lzData.srcAirdropAdapterParam,ITapiocaOFT.IBorrowParams(amountOut,0,externalData.magnetar,externalData.srcMarket),ICommonData.IWithdrawParams(false,0,false,0,0x),ICommonData.ISendOptions(lzData.srcExtraGasLimit,lzData.zroPaymentAddress),approvals)](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L226-L251)

- Should use `selfbalance()` instead of [INative(address(wrappedNative)).deposit{value: address(this).balance}()](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L105)

- Should use `selfbalance()` instead of [result = address(this).balance](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L107)

- Should use `selfbalance()` instead of [INative(address(wrappedNative)).deposit{value: address(this).balance}()](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L150-L152)

- Should use `selfbalance()` instead of [result = address(this).balance](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L206)

- Should use `selfbalance()` instead of [ISendFrom(address(this)).sendFrom{value: address(this).balance}(from,lzDstChainId,LzLib.addressToBytes32(from),amount,callParams)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142-L148)

- Should use `selfbalance()` instead of [\_lzSend(\_srcChainId,lzSendBackPayload,address(this),\_zroPaymentAddress,,address(this).balance)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L219-L226)

- Should use `selfbalance()` instead of [IUSDOBase(swapData.tokenOut).sendAndLendOrRepay{value: address(this).balance}(address(this),leverageFor,lzData.lzSrcChainId,lzData.zroPaymentAddress,IUSDOBase.ILendOrRepayParams(true,amountOut,repayableAmount,externalData.magnetar,externalData.srcMarket,false,0,ITapiocaOptionLiquidityProvision.IOptionsLockData(false,address(0),0,0,0),ITapiocaOptionsBroker.IOptionsParticipateData(false,address(0),0)),approvals,ICommonData.IWithdrawParams(false,0,false,0,0x),LzLib.buildDefaultAdapterParams(lzData.srcExtraGasLimit))](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L229-L266)

- Should use `selfbalance()` instead of [address(this).balance < \_amount](/tapiocaz-audit/contracts/Balancer.sol#L286)

### recommendation:

Using `selfbalance()` instead of `address(this).balance`, for example:

```
function assemblyInternalBalance() public returns (uint256) {
    assembly {
        let c := selfbalance()
        mstore(0x00, c)
        return(0x00, 0x20)
    }
}
```

### locations:

- /tap-token-audit/contracts/tokens/BaseTapOFT.sol#L312-L318
- /tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L127-L133
- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L226-L251
- /tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L105
- /tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L107
- /tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L150-L152
- /tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L206
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142-L148
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L219-L226
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L229-L266
- /tapiocaz-audit/contracts/Balancer.sol#L286

### severity:

Optimization

## [G-07] Amounts should be checked for `0` before calling a `transfer`

### description:

According to the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted.

Checking non-zero transfer values can avoid an expensive external call and save gas.
While this is done at some places, it’s not consistently done in the solution.

**There are `10` instances of this issue:**

- Adding a non-zero-value check for [tapToken.transferFrom(msg.sender,address(this),amount)](/tap-token-audit/contracts/tokens/LTap.sol#L42) at the beginning of [LTap.deposit(uint256)](/tap-token-audit/contracts/tokens/LTap.sol#L41-L44)

- Adding a non-zero-value check for [tapToken.transfer(msg.sender,amount)](/tap-token-audit/contracts/tokens/LTap.sol#L50) at the beginning of [LTap.redeem()](/tap-token-audit/contracts/tokens/LTap.sol#L46-L51)

- Adding a non-zero-value check for [tapOFT.transferFrom(msg.sender,address(this),\_amount)](/tap-token-audit/contracts/governance/twTAP.sol#L260) at the beginning of [TwTAP.participate(address,uint256,uint256)](/tap-token-audit/contracts/governance/twTAP.sol#L252-L348)

- Adding a non-zero-value check for [paymentToken.transfer(paymentTokenBeneficiary,paymentToken.balanceOf(address(this)))](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L377-L380) at the beginning of [AirdropBroker.collectPaymentTokens(address[])](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L365-L383)

- Adding a non-zero-value check for [rewardToken.safeTransferFrom(msg.sender,address(this),\_amount)](/tap-token-audit/contracts/governance/twTAP.sol#L448) at the beginning of [TwTAP.distributeReward(uint256,uint256)](/tap-token-audit/contracts/governance/twTAP.sol#L429-L449)

- Adding a non-zero-value check for [paymentToken.transfer(paymentTokenBeneficiary,paymentToken.balanceOf(address(this)))](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L491-L494) at the beginning of [TapiocaOptionBroker.collectPaymentTokens(address[])](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L479-L497)

- Adding a non-zero-value check for [\_paymentToken.transferFrom(msg.sender,address(this),discountedPaymentAmount)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L509-L513) at the beginning of [AirdropBroker.\_processOTCDeal(ERC20,PaymentTokenOracle,uint256,uint256)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L487-L515)

- Adding a non-zero-value check for [tapOFT.transfer(msg.sender,tapAmount)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L514) at the beginning of [AirdropBroker.\_processOTCDeal(ERC20,PaymentTokenOracle,uint256,uint256)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L487-L515)

- Adding a non-zero-value check for [\_paymentToken.transferFrom(msg.sender,address(this),discountedPaymentAmount)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L530-L534) at the beginning of [TapiocaOptionBroker.\_processOTCDeal(ERC20,PaymentTokenOracle,uint256,uint256)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L508-L536)

- Adding a non-zero-value check for [tapOFT.transfer(\_to,releasedAmount)](/tap-token-audit/contracts/governance/twTAP.sol#L565) at the beginning of [TwTAP.\_releaseTap(uint256,address)](/tap-token-audit/contracts/governance/twTAP.sol#L522-L568)

### recommendation:

Consider adding a non-zero-value check at the beginning of function.

### locations:

- /tap-token-audit/contracts/tokens/LTap.sol#L42
- /tap-token-audit/contracts/tokens/LTap.sol#L50
- /tap-token-audit/contracts/governance/twTAP.sol#L260
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L377-L380
- /tap-token-audit/contracts/governance/twTAP.sol#L448
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L491-L494
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L509-L513
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L514
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L530-L534
- /tap-token-audit/contracts/governance/twTAP.sol#L565

### severity:

Optimization

## [G-08] Remove unused parameter variables

### description:

Unused parameters variables are gas consuming,
since the initial value assignment costs gas.
And are a bad code practice.
Removing those variables can save deployment and called gas. and improve code quality.

**There are `3` instances of this issue:**

- The param variables in [USDO.maxFlashLoan(address)](/tapioca-bar-audit/contracts/usd0/USDO.sol#L57-L59) are unused.

  - [USDO.maxFlashLoan(address).address](/tapioca-bar-audit/contracts/usd0/USDO.sol#L57)

- The param variables in [BigBang.repay(address,address,bool,uint256)](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L263-L274) are unused.

  - [BigBang.repay(address,address,bool,uint256).bool](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L266)

- The param variables in [BigBang.refreshPenroseFees(address)](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L442-L462) are unused.
  - [BigBang.refreshPenroseFees(address).](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L443)

### recommendation:

Remove the unused parameter variables.

### locations:

- /tapioca-bar-audit/contracts/usd0/USDO.sol#L57-L59
- /tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L263-L274
- /tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L442-L462

### severity:

Optimization

## [G-09] Use `string.concat()` on string instead of `abi.encodePacked()` to save gas

### description:

Starting with version 0.8.12,
Solidity has the `string.concat()` function,
which allows one to concatenate a list of strings, without extra padding.
Using this function rather than `abi.encodePacked()` makes the intended operation more clear,
leading to less reviewer confusion and saving more gas.

**There are `2` instances of this issue:**

- should use `string.concat()` on string instead of [string(abi.encodePacked(tm,collateral.safeSymbol(),/,asset.safeSymbol(),-,oracle.symbol(oracleData)))](/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol#L155-L165)

- should use `string.concat()` on string instead of [string(abi.encodePacked(Tapioca Singularity ,collateral.safeName(),/,asset.safeName(),-,oracle.name(oracleData)))](/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol#L170-L180)

### recommendation:

Use `string.concat()` on string instead of `abi.encodePacked()`

### locations:

- /tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol#L155-L165
- /tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol#L170-L180

### severity:

Optimization

## [G-10] Use assembly to check for `address(0)`

### description:

[Inline Assembly](https://docs.soliditylang.org/en/latest/assembly.html) more gas efficient and [Saving Gas with Simple Inlining](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/).

**There are `24` instances of this issue:**

- [address(liquidationQueue) != address(0)](/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol#L40) should use assembly to check for `address(0)`

- [require(bool,string)(address(\_collateral) != address(0) && address(\_asset) != address(0) && address(\_oracle) != address(0),SGL: bad pair)](/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L100-L105) should use assembly to check for `address(0)`

- [require(bool,string)(\_conservator != address(0),USDO: address not valid)](/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L106) should use assembly to check for `address(0)`

- [require(bool,string)(address(\_collateral) != address(0) && address(\_asset) != address(0) && address(\_oracle) != address(0),BigBang: bad pair)](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L133-L138) should use assembly to check for `address(0)`

- [require(bool,string)(to != address(0),ERC20: no zero address)](/tapioca-bar-audit/contracts/markets/MarketERC20.sol#L144) should use assembly to check for `address(0)`

- [address(\_oracle) != address(0)](/tapioca-bar-audit/contracts/markets/Market.sol#L177) should use assembly to check for `address(0)`

- [require(bool,string)(to != address(0),ERC20: no zero address)](/tapioca-bar-audit/contracts/markets/MarketERC20.sol#L179) should use assembly to check for `address(0)`

- [\_conservator != address(0)](/tapioca-bar-audit/contracts/markets/Market.sol#L187) should use assembly to check for `address(0)`

- [require(bool,string)(address(swappers\_[0]) != address(0),Penrose: zero address)](/tapioca-bar-audit/contracts/Penrose.sol#L242) should use assembly to check for `address(0)`

- [require(bool,string)(address(markets\_[0]) != address(0),Penrose: zero address)](/tapioca-bar-audit/contracts/Penrose.sol#L243) should use assembly to check for `address(0)`

- [require(bool,string)(\_conservator != address(0),Penrose: address not valid)](/tapioca-bar-audit/contracts/Penrose.sol#L282) should use assembly to check for `address(0)`

- [module == address(0)](/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L354) should use assembly to check for `address(0)`

- [address(\_liquidationQueue) != address(0)](/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L581) should use assembly to check for `address(0)`

- [\_bidExecutionSwapper != address(0)](/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L586) should use assembly to check for `address(0)`

- [\_usdoSwapper != address(0)](/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L591) should use assembly to check for `address(0)`

- [module == address(0)](/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L611) should use assembly to check for `address(0)`

- [require(bool,string)(\_glpRewardRouter.gmx() == address(0),Bad GLP reward router)](/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L62) should use assembly to check for `address(0)`

- [require(bool,string)(\_gmx != address(0),Bad GMX reward router)](/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L66) should use assembly to check for `address(0)`

- [erc20 == address(0)](/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol#L74) should use assembly to check for `address(0)`

- [erc20 == address(0)](/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol#L94) should use assembly to check for `address(0)`

- [\_router == address(0)](/tapiocaz-audit/contracts/Balancer.sol#L127) should use assembly to check for `address(0)`

- [\_routerETH == address(0)](/tapiocaz-audit/contracts/Balancer.sol#L128) should use assembly to check for `address(0)`

- [ITapiocaOFT(\_srcOft).erc20() == address(0)](/tapiocaz-audit/contracts/Balancer.sol#L142) should use assembly to check for `address(0)`

- [address(tapiocaOFTsByErc20[\_erc20]) != address(0x0)](/tapiocaz-audit/contracts/TapiocaWrapper.sol#L160) should use assembly to check for `address(0)`

- [erc20 == address(0)](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272) should use assembly to check for `address(0)`

- [erc20 == address(0)](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L371) should use assembly to check for `address(0)`

- [module == address(0)](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L396) should use assembly to check for `address(0)`

- [require(bool,string)(newOwner != address(0) || renounce,NTF: zero address)](/YieldBox/contracts/NativeTokenFactory.sol#L59) should use assembly to check for `address(0)`

- [owner == address(0)](/YieldBox/contracts/YieldBoxURIBuilder.sol#L104-L108) should use assembly to check for `address(0)`

- [require(bool,string)(to != address(0),No 0 address)](/YieldBox/contracts/YieldBox.sol#L317) should use assembly to check for `address(0)`

- [require(bool,string)(tos[i] != address(0),YieldBox: to not set)](/YieldBox/contracts/YieldBox.sol#L340) should use assembly to check for `address(0)`

- [require(bool,string)(operator != address(0),YieldBox: operator not set)](/YieldBox/contracts/YieldBox.sol#L360) should use assembly to check for `address(0)`

- [require(bool,string)(operator != address(0),YieldBox: operator not set)](/YieldBox/contracts/YieldBox.sol#L382) should use assembly to check for `address(0)`

### recommendation:

Use assembly to check for `address(0)`:

```
function addrNotZero(address _addr) public pure {
        assembly {
            if iszero(_addr) {
                mstore(0x00, "zero address")
                revert(0x00, 0x20)
            }
        }
}
```

### locations:

- /tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol#L40
- /tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L100-L105
- /tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L106
- /tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L133-L138
- /tapioca-bar-audit/contracts/markets/MarketERC20.sol#L144
- /tapioca-bar-audit/contracts/markets/Market.sol#L177
- /tapioca-bar-audit/contracts/markets/MarketERC20.sol#L179
- /tapioca-bar-audit/contracts/markets/Market.sol#L187
- /tapioca-bar-audit/contracts/Penrose.sol#L242
- /tapioca-bar-audit/contracts/Penrose.sol#L243
- /tapioca-bar-audit/contracts/Penrose.sol#L282
- /tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L354
- /tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L581
- /tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L586
- /tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L591
- /tapioca-bar-audit/contracts/markets/singularity/Singularity.sol#L611
- /tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L62
- /tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol#L66
- /tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol#L74
- /tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol#L94
- /tapiocaz-audit/contracts/Balancer.sol#L127
- /tapiocaz-audit/contracts/Balancer.sol#L128
- /tapiocaz-audit/contracts/Balancer.sol#L142
- /tapiocaz-audit/contracts/TapiocaWrapper.sol#L160
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272
- /tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L371
- /tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L396
- /YieldBox/contracts/NativeTokenFactory.sol#L59
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L104-L108
- /YieldBox/contracts/YieldBox.sol#L317
- /YieldBox/contracts/YieldBox.sol#L340
- /YieldBox/contracts/YieldBox.sol#L360
- /YieldBox/contracts/YieldBox.sol#L382

### severity:

Optimization
