## GAS Summary<a name="GAS Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#gas1-abiencode-is-less-efficient-than-abiencodepacked) | `abi.encode()` is less efficient than `abi.encodepacked()` | 38 | 2014 |
| [GAS&#x2011;2](#gas2-consider-activating-viair-for-deploying) | Consider activating `via-ir` for deploying | 1 | 250 |
| [GAS&#x2011;3](#gas3-use-assembly-to-emit-events) | Use assembly to emit events | 181 | 6878 |
| [GAS&#x2011;4](#gas4-counting-down-in-for-statements-is-more-gas-efficient) | Counting down in `for` statements is more gas efficient | 46 | 11822 |
| [GAS&#x2011;5](#gas5-duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 62 | 1736 |
| [GAS&#x2011;6](#gas6-use-assembly-to-write-address-storage-values) | Use `assembly` to write address storage values | 22 | 1628 |
| [GAS&#x2011;7](#gas7-use-assembly-to-check-for-address0) | Use assembly to check for `address(0)` | 4 | 104 |
| [GAS&#x2011;8](#gas8-use-v492-openzeppelin-contracts) | Use v4.9.2 OpenZeppelin contracts | 3 | 84 |
| [GAS&#x2011;9](#gas30-use-nested-if-and-avoid-multiple-check-combinations) | Use nested `if` and avoid multiple check combinations | 11 | 66 |
| [GAS&#x2011;10](#gas35-using-xor-^-and-and-&-bitwise-equivalents) | Using XOR (^) and AND (&) bitwise equivalents | 193 | 2509 |
| [GAS&#x2011;11](#gas7-using-thisfn-wastes-gas) | Using `this.<fn>()` wastes gas | 8 | 800 |

Total: 588 contexts over 11 issues

## Gas Optimizations

### <a href="#gas-summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: BaseTapOFT.sol

96: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L96

```solidity
File: BaseTapOFT.sol

172: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L172

```solidity
File: BaseTapOFT.sol

266: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L266

```solidity
File: MarketERC20.sol

264: abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L264

```solidity
File: USDOLeverageModule.sol

39: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L39

```solidity
File: USDOLeverageModule.sol

72: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L72

```solidity
File: USDOMarketModule.sol

40: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L40

```solidity
File: USDOMarketModule.sol

78: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L78

```solidity
File: USDOOptionsModule.sol

32: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L32

```solidity
File: USDOOptionsModule.sol

75: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L75

```solidity
File: MagnetarV2.sol

209: string(abi.encode(i))
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L209

```solidity
File: MagnetarV2.sol

293: returnData: abi.encode(amountOut, shareOut)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L293

```solidity
File: MagnetarV2.sol

323: returnData: abi.encode(part, share)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L323

```solidity
File: MagnetarV2.sol

382: returnData: abi.encode(fraction)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L382

```solidity
File: MagnetarV2.sol

399: returnData: abi.encode(amount)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L399

```solidity
File: MagnetarMarketModule.sol

191: bytes memory withdrawAssetBytes = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L191

```solidity
File: MagnetarMarketModule.sol

592: bytes memory withdrawAssetBytes = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L592

```solidity
File: MagnetarMarketModule.sol

653: bytes memory withdrawCollateralBytes = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L653

```solidity
File: UniswapV2Swapper.sol

53: return abi.encode(block.timestamp + 1 hours);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L53

```solidity
File: UniswapV3Swapper.sol

75: return abi.encode(block.timestamp + 1 hours);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L75

```solidity
File: BalancerStrategy.sol

169: joinPoolRequest.userData = abi.encode(1, maxAmountsIn);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L169

```solidity
File: BalancerStrategy.sol

179: joinPoolRequest.userData = abi.encode(2, bptOut, uint256(index));
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L179

```solidity
File: BalancerStrategy.sol

238: exitRequest.userData = abi.encode(
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L238

```solidity
File: BalancerStrategy.sol

255: exitRequest.userData = abi.encode(0, bptIn, index);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L255

```solidity
File: BalancerStrategy.sol

288: exitRequest.userData = abi.encode(0, lpBalance, index);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L288

```solidity
File: GlpStrategy.sol

329: abi.encode(gmxAmount)
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L329

```solidity
File: Balancer.sol

143: ercData = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L143

```solidity
File: Balancer.sol

316: dstNativeAddr: abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L316

```solidity
File: BaseTOFTLeverageModule.sol

56: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L56

```solidity
File: BaseTOFTLeverageModule.sol

89: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L89

```solidity
File: BaseTOFTMarketModule.sol

56: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L56

```solidity
File: BaseTOFTMarketModule.sol

105: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L105

```solidity
File: BaseTOFTOptionsModule.sol

47: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L47

```solidity
File: BaseTOFTOptionsModule.sol

90: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L90

```solidity
File: BaseTOFTStrategyModule.sol

60: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L60

```solidity
File: BaseTOFTStrategyModule.sol

102: bytes memory lzPayload = abi.encode(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L102

```solidity
File: YieldBoxPermit.sol

53: bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));
```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L53

```solidity
File: YieldBoxPermit.sol

82: bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L82



</details>

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized();
            c1.optimized();
        }
    }
    contract Contract0 {
        string a = "Code4rena";
        function not_optimized() public returns(bytes32){
            return keccak256(abi.encode(a));
        }
    }
    contract Contract1 {
        string a = "Code4rena";
        function optimized() public returns(bytes32){
            return keccak256(abi.encodePacked(a));
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 101871                                    | 683             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| not_optimized                             | 2661            | 2661 | 2661   | 2661 | 1       |


| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 99465                                     | 671             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| optimized                                 | 2608            | 2608 | 2608   | 2608 | 1       |



### <a href="#gas-summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Consider activating `via-ir` for deploying

The IR-based code generator was introduced with an aim to not only allow code generation to be more transparent and auditable but also to enable more powerful optimization passes that span across functions.

You can enable it on the command line using `--via-ir` or with the option `{"viaIR": true}`.

This will take longer to compile, but you can just simple test it before deploying and if you got a better benchmark then you can add --via-ir to your deploy command

More on: https://docs.soliditylang.org/en/v0.8.17/ir-breaking-changes.html





### <a href="#gas-summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Use assembly to emit events

We can use assembly to emit events efficiently by utilizing `scratch space` and the `free memory pointer`. This will allow us to potentially avoid memory expansion costs.
Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

For example, for a generic `emit` event for `eventSentAmountExample`:
```solidity
// uint256 id, uint256 value, uint256 amount
emit eventSentAmountExample(id, value, amount);
```

We can use the following assembly emit events:

```solidity
        assembly {
            let memptr := mload(0x40)
            mstore(0x00, calldataload(0x44))
            mstore(0x20, calldataload(0xa4))
            mstore(0x40, amount)
            log1(
                0x00,
                0x60,
                // keccak256("eventSentAmountExample(uint256,uint256,uint256)")
                0xa622cf392588fbf2cd020ff96b2f4ebd9c76d7a4bc7f3e6b2f18012312e76bc3
            )
            mstore(0x40, memptr)
        }
```

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: Vesting.sol

120: emit Claimed(msg.sender, _claimable);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L120

```solidity
File: Vesting.sol

145: emit UserRegistered(_user, _amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L145

```solidity
File: twTAP.sol

346: emit Participate(_participant, _amount, multiplier);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L346

```solidity
File: twTAP.sol

567: emit ExitPosition(_tokenId, releasedAmount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L567

```solidity
File: AirdropBroker.sol

217: emit Participate(cachedEpoch, aoTAPTokenID);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L217

```solidity
File: AirdropBroker.sol

293: emit NewEpoch(epoch, epochTAPValuation);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L293

```solidity
File: AirdropBroker.sol

315: emit SetTapOracle(_tapOracle, _tapOracleData);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L315

```solidity
File: AirdropBroker.sol

352: emit SetPaymentToken(_paymentToken, _oracle, _oracleData);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L352

```solidity
File: aoTAP.sol

123: emit Mint(_to, tokenId, option);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L123

```solidity
File: aoTAP.sol

135: emit Burn(msg.sender, _tokenId, options[_tokenId]);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L135

```solidity
File: oTAP.sol

110: emit Mint(_to, tokenId, option);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L110

```solidity
File: oTAP.sol

122: emit Burn(msg.sender, _tokenId, options[_tokenId]);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L122

```solidity
File: TapiocaOptionBroker.sol

344: emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L344

```solidity
File: TapiocaOptionBroker.sol

426: uint256 epochTAP = tapOFT.emitForWeek();
427: _emitToGauges(epochTAP);
431: emit NewEpoch(epoch, epochTAP, epochTAPValuation);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L426

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L427

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L431



```solidity
File: TapiocaOptionBroker.sol

453: emit SetTapOracle(_tapOracle, _tapOracleData);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L453

```solidity
File: TapiocaOptionBroker.sol

466: emit SetPaymentToken(_paymentToken, _oracle, _oracleData);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L466

```solidity
File: TapiocaOptionLiquidityProvision.sol

200: emit Mint(_to, uint128(sglAssetID), lockPosition);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L200

```solidity
File: TapiocaOptionLiquidityProvision.sol

249: emit Burn(_to, lockPosition.sglAssetID, lockPosition);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L249

```solidity
File: TapiocaOptionLiquidityProvision.sol

269: emit SetSGLPoolWeight(address(singularity), weight);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L269

```solidity
File: TapiocaOptionLiquidityProvision.sol

292: emit RegisterSingularity(address(singularity), assetID);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L292

```solidity
File: TapiocaOptionLiquidityProvision.sol

328: emit UnregisterSingularity(address(singularity), sglAssetID);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L328

```solidity
File: BaseTapOFT.sol

143: emit CallFailedStr(_srcChainId, _payload, _reason);
146: emit CallFailedBytes(_srcChainId, _payload, _reason);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L143

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L146



```solidity
File: BaseTapOFT.sol

241: emit CallFailedStr(_srcChainId, _payload, _reason);
243: emit CallFailedBytes(_srcChainId, _payload, _reason);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L241

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L243



```solidity
File: BaseTapOFT.sol

320: emit CallFailedStr(_srcChainId, _payload, _reason);
322: emit CallFailedBytes(_srcChainId, _payload, _reason);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L320

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L322



```solidity
File: TapOFT.sol

154: emit PausedUpdated(paused, val);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L154

```solidity
File: TapOFT.sol

162: emit MinterUpdated(minter, _minter);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L162

```solidity
File: TapOFT.sol

212: emit Emitted(week, emission);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L212

```solidity
File: TapOFT.sol

228: emit Minted(msg.sender, _to, _amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L228

```solidity
File: TapOFT.sol

235: emit Burned(msg.sender, _amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L235

```solidity
File: Penrose.sol

247: emit ProtocolWithdrawal(markets_, block.timestamp);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L247

```solidity
File: Penrose.sol

258: emit BigBangEthMarketDebtRate(_rate);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L258

```solidity
File: Penrose.sol

265: emit BigBangEthMarketSet(_market);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L265

```solidity
File: Penrose.sol

274: emit PausedUpdated(paused, val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L274

```solidity
File: Penrose.sol

283: emit ConservatorUpdated(conservator, _conservator);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L283

```solidity
File: Penrose.sol

310: emit UsdoTokenUpdated(_usdoToken, usdoAssetId);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L310

```solidity
File: Penrose.sol

332: emit RegisterSingularityMasterContract(mcAddress, contractType_);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L332

```solidity
File: Penrose.sol

354: emit RegisterBigBangMasterContract(mcAddress, contractType_);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L354

```solidity
File: Penrose.sol

375: emit RegisterSingularity(_contract, mc);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L375

```solidity
File: Penrose.sol

387: emit RegisterSingularity(_contract, mc);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L387

```solidity
File: Penrose.sol

408: emit RegisterBigBang(_contract, mc);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L408

```solidity
File: Penrose.sol

420: emit RegisterBigBang(_contract, mc);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L420

```solidity
File: Penrose.sol

457: emit FeeToUpdate(feeTo_);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L457

```solidity
File: Penrose.sol

466: emit SwapperUpdate(address(swapper), enable);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L466

```solidity
File: Penrose.sol

539: emit LogYieldBoxFeesDeposit(feeShares, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L539

```solidity
File: Market.sol

144: emit LogBorrowingFee(borrowOpeningFee, _val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L144

```solidity
File: Market.sol

152: emit LogBorrowCapUpdated(totalBorrowCap, _cap);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L152

```solidity
File: Market.sol

173: emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);
179: emit OracleUpdated();
184: emit OracleDataUpdated();
188: emit ConservatorUpdated(conservator, _conservator);
229: emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L173

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L179

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L184

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L188

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L229



```solidity
File: Market.sol

248: emit PausedUpdated(paused, val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L248

```solidity
File: Market.sol

346: emit LogExchangeRate(rate);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L346

```solidity
File: MarketERC20.sol

150: emit Transfer(msg.sender, to, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L150

```solidity
File: MarketERC20.sol

185: emit Transfer(from, to, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L185

```solidity
File: MarketERC20.sol

292: emit ApprovalBorrow(owner, spender, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L292

```solidity
File: MarketERC20.sol

297: emit Approval(owner, spender, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L297

```solidity
File: BigBang.sol

477: emit MinDebtRateUpdated(minDebtRate, _minDebtRate);
483: emit MaxDebtRateUpdated(maxDebtRate, _maxDebtRate);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L477

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L483



```solidity
File: BigBang.sol

540: emit LogAccrue(extraAmount, _accrueInfo.debtRate);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L540

```solidity
File: BigBang.sol

557: emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L557

```solidity
File: BigBang.sol

587: emit LogRemoveCollateral(user, address(swapper), collateralShare);
588: emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L587-L588

```solidity
File: BigBang.sol

716: emit LogRemoveCollateral(from, to, share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L716

```solidity
File: BigBang.sol

738: emit LogRepay(from, to, amount, part);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L738

```solidity
File: BigBang.sol

766: emit LogBorrow(from, to, amount, feeAmount, part);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L766

```solidity
File: SGLCommon.sol

151: emit LogAccrue(0, 0, startingInterestPerSecond, 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L151

```solidity
File: SGLCommon.sol

215: emit Transfer(address(0), to, fraction);
218: emit LogAddAsset(skim ? address(yieldBox) : from, to, share, fraction);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L215

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L218



```solidity
File: SGLCommon.sol

237: emit Transfer(from, address(0), fraction);
242: emit LogRemoveAsset(from, to, share, fraction);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L237

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L242



```solidity
File: SGLLendingCommon.sol

37: emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L37

```solidity
File: SGLLendingCommon.sol

48: emit LogRemoveCollateral(from, to, share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L48

```solidity
File: SGLLendingCommon.sol

71: emit LogBorrow(from, to, amount, feeAmount, part);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L71

```solidity
File: SGLLendingCommon.sol

97: emit LogRepay(skim ? address(yieldBox) : from, to, amount, part);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L97

```solidity
File: SGLLiquidation.sol

321: emit LogRemoveCollateral(user, address(swapper), collateralShare);
322: emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L321-L322

```solidity
File: Singularity.sol

466: emit Transfer(address(0), _feeTo, _feesEarnedFraction);
468: emit LogWithdrawFees(_feeTo, _feesEarnedFraction);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L466

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L468



```solidity
File: Singularity.sol

587: emit BidExecutionSwapperUpdated(_bidExecutionSwapper);
592: emit UsdoSwapperUpdated(_usdoSwapper);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L587

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L592



```solidity
File: BaseUSDO.sol

89: emit MaxFlashMintUpdated(maxFlashMint, _val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L89

```solidity
File: BaseUSDO.sol

98: emit FlashMintFeeUpdated(flashMintFee, _val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L98

```solidity
File: BaseUSDO.sol

107: emit ConservatorUpdated(conservator, _conservator);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L107

```solidity
File: BaseUSDO.sol

117: emit PausedUpdated(paused, val);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L117

```solidity
File: BaseUSDO.sol

127: emit SetMinterStatus(_for, _status);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L127

```solidity
File: BaseUSDO.sol

136: emit SetBurnerStatus(_for, _status);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L136

```solidity
File: USDO.sol

112: emit Minted(_to, _amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L112

```solidity
File: USDO.sol

121: emit Burned(_from, _amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L121

```solidity
File: USDOLeverageModule.sol

59: emit SendToChain(lzData.lzSrcChainId, msg.sender, senderBytes, 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L59

```solidity
File: USDOLeverageModule.sol

90: emit SendToChain(lzData.lzDstChainId, msg.sender, senderBytes, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L90

```solidity
File: USDOLeverageModule.sol

187: emit ReceiveFromChain(_srcChainId, leverageFor, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L187

```solidity
File: USDOMarketModule.sol

57: emit SendToChain(lzDstChainId, from, LzLib.addressToBytes32(to), 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L57

```solidity
File: USDOMarketModule.sol

188: emit ReceiveFromChain(_srcChainId, to, lendParams.depositAmount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L188

```solidity
File: USDOOptionsModule.sol

135: emit ReceiveFromChain(lzDstChainId, from, 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L135

```solidity
File: UniswapV3Swapper.sol

62: emit PoolFee(poolFee, _newFee);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L62

```solidity
File: AaveStrategy.sol

123: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L123

```solidity
File: AaveStrategy.sol

130: emit MultiSwapper(address(swapper), _swapper);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L130

```solidity
File: AaveStrategy.sol

204: emit AmountDeposited(queued);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L204

```solidity
File: AaveStrategy.sol

243: emit AmountDeposited(queued);
246: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L243

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L246



```solidity
File: AaveStrategy.sol

274: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L274

```solidity
File: BalancerStrategy.sol

110: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L110

```solidity
File: BalancerStrategy.sol

142: emit AmountDeposited(queued);
144: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L142

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L144



```solidity
File: BalancerStrategy.sol

215: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L215

```solidity
File: CompoundStrategy.sol

90: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L90

```solidity
File: CompoundStrategy.sol

129: emit AmountDeposited(queued);
132: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L129

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L132



```solidity
File: CompoundStrategy.sol

161: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L161

```solidity
File: ConvexTricryptoStrategy.sol

164: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L164

```solidity
File: ConvexTricryptoStrategy.sol

171: emit MultiSwapper(address(swapper), _swapper);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L171

```solidity
File: ConvexTricryptoStrategy.sol

180: emit LPGetterSet(address(lpGetter), _lpGetter);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L180

```solidity
File: ConvexTricryptoStrategy.sol

214: emit AmountDeposited(queued);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L214

```solidity
File: ConvexTricryptoStrategy.sol

304: emit AmountDeposited(queued);
307: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L304

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L307



```solidity
File: ConvexTricryptoStrategy.sol

349: emit AmountDeposited(queued);
351: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L349

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L351



```solidity
File: TricryptoLPStrategy.sol

135: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L135

```solidity
File: TricryptoLPStrategy.sol

142: emit MultiSwapper(address(swapper), _swapper);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L142

```solidity
File: TricryptoLPStrategy.sol

151: emit LPGetterSet(address(lpGetter), _lpGetter);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L151

```solidity
File: TricryptoLPStrategy.sol

193: emit AmountDeposited(lpAmount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L193

```solidity
File: TricryptoLPStrategy.sol

222: emit AmountDeposited(queued);
225: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L222

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L225



```solidity
File: TricryptoLPStrategy.sol

253: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L253

```solidity
File: TricryptoNativeStrategy.sol

126: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L126

```solidity
File: TricryptoNativeStrategy.sol

133: emit MultiSwapper(address(swapper), _swapper);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L133

```solidity
File: TricryptoNativeStrategy.sol

142: emit LPGetterSet(address(lpGetter), _lpGetter);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L142

```solidity
File: TricryptoNativeStrategy.sol

176: emit AmountDeposited(queued);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L176

```solidity
File: TricryptoNativeStrategy.sol

209: emit AmountDeposited(queued);
212: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L209

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L212



```solidity
File: TricryptoNativeStrategy.sol

244: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L244

```solidity
File: LidoEthStrategy.sol

94: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L94

```solidity
File: LidoEthStrategy.sol

134: emit AmountDeposited(queued);
137: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L134

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L137



```solidity
File: LidoEthStrategy.sol

166: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L166

```solidity
File: StargateStrategy.sol

143: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L143

```solidity
File: StargateStrategy.sol

150: emit MultiSwapper(address(swapper), _swapper);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L150

```solidity
File: StargateStrategy.sol

228: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L228

```solidity
File: StargateStrategy.sol

237: emit AmountDeposited(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L237

```solidity
File: StargateStrategy.sol

268: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L268

```solidity
File: YearnStrategy.sol

91: emit DepositThreshold(depositThreshold, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L91

```solidity
File: YearnStrategy.sol

125: emit AmountDeposited(queued);
128: emit AmountQueued(amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L125

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L128



```solidity
File: YearnStrategy.sol

149: emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L149

```solidity
File: Balancer.sol

211: emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L211

```solidity
File: Balancer.sol

243: emit ConnectedChainUpdated(_srcOft, _dstChainId, _dstOft);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L243

```solidity
File: TapiocaWrapper.sol

101: emit HarvestFees(msg.sender);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L101

```solidity
File: TapiocaWrapper.sol

177: emit CreateOFT(iOFT, _erc20);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L177

```solidity
File: mTapiocaOFT.sol

135: emit BalancerStatusUpdated(_balancer, balancers[_balancer], _status);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L135

```solidity
File: mTapiocaOFT.sol

151: emit Rebalancing(msg.sender, _amount, _isNative);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L151

```solidity
File: BaseTOFTLeverageModule.sol

76: emit SendToChain(lzData.lzSrcChainId, msg.sender, senderBytes, 0);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L76

```solidity
File: BaseTOFTLeverageModule.sol

107: emit SendToChain(lzData.lzDstChainId, msg.sender, senderBytes, amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L107

```solidity
File: BaseTOFTLeverageModule.sol

202: emit ReceiveFromChain(_srcChainId, leverageFor, amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L202

```solidity
File: BaseTOFTMarketModule.sol

75: emit SendToChain(lzDstChainId, from, toAddress, 0);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L75

```solidity
File: BaseTOFTMarketModule.sol

123: emit SendToChain(lzDstChainId, _from, toAddress, borrowParams.amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L123

```solidity
File: BaseTOFTMarketModule.sol

177: emit ReceiveFromChain(_srcChainId, _from, borrowParams.amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L177

```solidity
File: BaseTOFTOptionsModule.sol

150: emit ReceiveFromChain(lzDstChainId, from, 0);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L150

```solidity
File: BaseTOFTStrategyModule.sol

79: emit SendToChain(lzDstChainId, _from, toAddress, amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L79

```solidity
File: BaseTOFTStrategyModule.sol

119: emit SendToChain(lzDstChainId, msg.sender, toAddress, amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L119

```solidity
File: BaseTOFTStrategyModule.sol

170: emit ReceiveFromChain(_srcChainId, onBehalfOf, amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L170

```solidity
File: BaseTOFTStrategyModule.sol

234: emit ReceiveFromChain(_srcChainId, _from, _amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L234

```solidity
File: NativeTokenFactory.sol

62: emit OwnershipTransferred(tokenId, owner[tokenId], newOwner);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L62

```solidity
File: NativeTokenFactory.sol

80: emit OwnershipTransferred(tokenId, owner[tokenId], _pendingOwner);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L80

```solidity
File: NativeTokenFactory.sol

99: emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);
100: emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);
101: emit OwnershipTransferred(tokenId, address(0), msg.sender);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L99-L101

```solidity
File: YieldBox.sol

157: emit Deposited(msg.sender, from, to, assetId, amount, share, amountOut, shareOut, false);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L157

```solidity
File: YieldBox.sol

185: emit Deposited(msg.sender, from, to, assetId, 1, 1, 1, 1, true);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L185

```solidity
File: YieldBox.sol

212: emit Deposited(msg.sender, msg.sender, to, assetId, amount, share, amountOut, shareOut, false);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L212

```solidity
File: YieldBox.sol

258: emit Withdraw(msg.sender, from, to, assetId, 1, 1, 1, 1);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L258

```solidity
File: YieldBox.sol

293: emit Withdraw(msg.sender, from, to, assetId, amount, share, amountOut, shareOut);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L293

```solidity
File: YieldBox.sol

328: emit TransferBatch(msg.sender, from, to, ids, values);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L328

```solidity
File: YieldBox.sol

350: emit TransferSingle(msg.sender, from, to, assetId, share_);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L350

```solidity
File: YieldBox.sol

373: emit ApprovalForAll(_owner, operator, approved);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L373

```solidity
File: YieldBox.sol

397: emit ApprovalForAsset(_owner, operator, assetId, approved);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L397



</details>





### <a href="#gas-summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Counting down in `for` statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: twTAP.sol

196: for (uint256 i = 0; i < len; ) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L196

```solidity
File: twTAP.sol

413: for (uint256 i = 0; i < len; ) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L413

```solidity
File: twTAP.sol

487: for (uint256 i = 0; i < len; ++i) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L487

```solidity
File: twTAP.sol

507: for (uint256 i = 0; i < len; ) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L507

```solidity
File: AirdropBroker.sol

332: for (uint256 i = 0; i < _users.length; i++) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L332


```solidity
File: AirdropBroker.sol

375: for (uint256 i = 0; i < len; ++i) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L375

```solidity
File: TapiocaOptionBroker.sol

489: for (uint256 i = 0; i < len; ++i) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L489

```solidity
File: TapiocaOptionBroker.sol

565: for (uint256 i = 0; i < len; ++i) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L565

```solidity
File: TapiocaOptionLiquidityProvision.sol

136: for (uint256 i = 0; i < len; ++i) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L136

```solidity
File: TapiocaOptionLiquidityProvision.sol

308: for (uint256 i = 0; i < sglLength; i++) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L308

```solidity
File: TapiocaOptionLiquidityProvision.sol

339: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L339

```solidity
File: BaseTapOFT.sol

228: for (uint i = 0; i < len; ) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L228

```solidity
File: BaseTapOFT.sol

333: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L333

```solidity
File: Penrose.sol

437: for (uint256 i = 0; i < len; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L437

```solidity
File: Penrose.sol

492: for (uint256 i = 0; i < length; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L492

```solidity
File: Penrose.sol

550: for (uint256 i = 0; i < _masterContractLength; ) {
569: for (uint256 j = 0; j < clonesOfLength; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L550

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L569



```solidity
File: BigBang.sol

215: for (uint256 i = 0; i < calls.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L215

```solidity
File: BigBang.sol

666: for (uint256 i = 0; i < users.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L666

```solidity
File: SGLLiquidation.sol

45: for (uint256 i = 0; i < maxBorrowParts.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L45

```solidity
File: SGLLiquidation.sol

107: for (uint256 i = 0; i < users.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L107

```solidity
File: SGLLiquidation.sol

384: for (uint256 i = 0; i < users.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L384

```solidity
File: Singularity.sol

187: for (uint256 i = 0; i < calls.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L187

```solidity
File: USDOLeverageModule.sol

255: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L255

```solidity
File: USDOMarketModule.sol

244: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L244

```solidity
File: USDOOptionsModule.sol

245: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L245

```solidity
File: MagnetarV2.sol

202: for (uint256 i = 0; i < length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L202

```solidity
File: MagnetarV2.sol

973: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L973

```solidity
File: MagnetarV2.sol

1004: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1004

```solidity
File: Multicall3.sol

47: for (uint256 i = 0; i < length; ) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L47

```solidity
File: Multicall3.sol

70: for (uint256 i = 0; i < length; ) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L70

```solidity
File: BalancerStrategy.sol

156: for (uint256 i = 0; i < poolTokens.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L156

```solidity
File: BalancerStrategy.sol

225: for (uint256 i = 0; i < poolTokens.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L225

```solidity
File: BalancerStrategy.sol

277: for (uint256 i = 0; i < poolTokens.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L277

```solidity
File: ConvexTricryptoStrategy.sol

195: for (uint256 i = 0; i < tokens.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L195

```solidity
File: ConvexTricryptoStrategy.sol

255: for (uint256 i = 0; i < tempData.tokens.length; i++) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L255


```solidity
File: TapiocaWrapper.sol

98: for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L98

```solidity
File: TapiocaWrapper.sol

140: for (uint256 i = 0; i < _call.length; i++) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L140

```solidity
File: BaseTOFTLeverageModule.sol

285: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L285

```solidity
File: BaseTOFTMarketModule.sol

261: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L261

```solidity
File: BaseTOFTOptionsModule.sol

260: for (uint256 i = 0; i < approvals.length; ) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L260

```solidity
File: NativeTokenFactory.sol

129: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L129

```solidity
File: NativeTokenFactory.sol

141: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L141

```solidity
File: YieldBox.sol

309: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L309

```solidity
File: YieldBox.sol

320: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L320

```solidity
File: YieldBox.sol

339: for (uint256 i = 0; i < len; i++) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L339



</details>


#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.AddNum();
            c1.AddNum();
        }
    }
    
    contract Contract0 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=0;i<=9;i++){
                _num = _num +1;
            }
            num = _num;
        }
    }
    
    contract Contract1 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=9;i>=0;i--){
                _num = _num +1;
            }
            num = _num;
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 77011                                     | 311             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |


| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 73811                                     | 295             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       |



#### <a href="#gas-summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: twTAP.sol

365: require(msg.sender == address(tapOFT), "twTAP: only tapOFT");
390: require(msg.sender == address(tapOFT), "twTAP: only tapOFT");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L365

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L390



```solidity
File: AirdropBroker.sol

158: require(aoTapOption.expiry > block.timestamp, "adb: Option expired");
233: require(aoTapOption.expiry > block.timestamp, "adb: Option expired");
174: require(eligibleTapAmount >= _tapAmount, "adb: Too high");
255: require(eligibleTapAmount >= _tapAmount, "adb: Too high");
392: require(_eligibleAmount > 0, "adb: Not eligible");
467: require(_eligibleAmount > 0, "adb: Not eligible");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L158

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L233

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L174

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L255

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L392

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L467



```solidity
File: TapiocaOptionBroker.sol

175: require(isPositionActive, "tOB: Option expired");
376: require(isPositionActive, "tOB: Option expired");
187: require(eligibleTapAmount >= _tapAmount, "tOB: Too high");
388: require(eligibleTapAmount >= _tapAmount, "tOB: Too high");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L175

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L376

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L187

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L388



```solidity
File: BaseTapOFT.sol

222: require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");
307: require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L222

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L307



```solidity
File: MarketERC20.sol

142: require(srcBalance >= amount, "ERC20: balance too low");
167: require(srcBalance >= amount, "ERC20: balance too low");
144: require(to != address(0), "ERC20: no zero address");
179: require(to != address(0), "ERC20: no zero address");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L142

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L167

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L144

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L179



```solidity
File: BigBang.sol

344: require(penrose.swappers(swapper), "SGL: Invalid swapper");
391: require(penrose.swappers(swapper), "SGL: Invalid swapper");
371: require(amountOut >= minAmountOut, "SGL: not enough");
413: require(amountOut >= minAmountOut, "SGL: not enough");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L344

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L391

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L371

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L413



```solidity
File: SGLLeverage.sol

104: require(penrose.swappers(swapper), "SGL: Invalid swapper");
155: require(penrose.swappers(swapper), "SGL: Invalid swapper");
126: require(amountOut >= minAmountOut, "SGL: not enough");
182: require(amountOut >= minAmountOut, "SGL: not enough");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L104

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L155

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L126

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLeverage.sol#L182



```solidity
File: Singularity.sol

627: revert(_getRevertMsg(returnData));
640: revert(_getRevertMsg(returnData));

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L627

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L640



```solidity
File: USDO.sol

68: require(token == address(this), "USDO: token not valid");
87: require(token == address(this), "USDO: token not valid");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L68

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L87



```solidity
File: USDOLeverageModule.sol

269: revert(reason);
284: revert(reason);
300: revert(reason);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L269

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L284

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L300



```solidity
File: USDOMarketModule.sol

258: revert(reason);
273: revert(reason);
289: revert(reason);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L258

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L273

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L289



```solidity
File: USDOOptionsModule.sol

259: revert(reason);
274: revert(reason);
290: revert(reason);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L259

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L274

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L290



```solidity
File: mTapiocaOFT.sol

93: require(!balancers[msg.sender], "TOFT_auth");
106: require(!balancers[msg.sender], "TOFT_auth");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L93

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L106



```solidity
File: BaseTOFTLeverageModule.sol

299: revert(reason);
314: revert(reason);
330: revert(reason);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L299

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L314

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L330



```solidity
File: BaseTOFTMarketModule.sol

275: revert(reason);
290: revert(reason);
306: revert(reason);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L275

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L290

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L306



```solidity
File: BaseTOFTOptionsModule.sol

274: revert(reason);
289: revert(reason);
305: revert(reason);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L274

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L289

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L305



```solidity
File: BaseTOFTStrategyModule.sol

56: require(amount > 0, "TOFT_0");
98: require(amount > 0, "TOFT_0");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L56

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L98



```solidity
File: NativeTokenFactory.sol

117: require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");
139: require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L117

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L139



```solidity
File: YieldBox.sol

360: require(operator != address(0), "YieldBox: operator not set");
382: require(operator != address(0), "YieldBox: operator not set");
361: require(operator != address(this), "YieldBox: can't approve yieldBox");
383: require(operator != address(this), "YieldBox: can't approve yieldBox");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L360

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L382

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L361

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L383



```solidity
File: YieldBoxPermit.sol

51: require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");
80: require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");
58: require(signer == owner, "YieldBoxPermit: invalid signature");
87: require(signer == owner, "YieldBoxPermit: invalid signature");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L51

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L80

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L58

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L87





</details>



### <a href="#gas-summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: TapiocaOptionLiquidityProvision.sol

131: _singularities = singularities;
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L131

```solidity
File: TapiocaOptionLiquidityProvision.sol

304: _singularities = singularities;
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L304

```solidity
File: Market.sol

316: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L316

```solidity
File: Market.sol

412: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L412

```solidity
File: BigBang.sol

513: _accrueInfo = accrueInfo;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L513

```solidity
File: BigBang.sol

525: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L525

```solidity
File: SGLCommon.sol

48: _accrueInfo = accrueInfo;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L48

```solidity
File: SGLCommon.sol

49: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L49

```solidity
File: SGLCommon.sol

50: _totalAsset = totalAsset;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L50

```solidity
File: SGLCommon.sol

203: _totalAsset = totalAsset;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L203

```solidity
File: SGLCommon.sol

232: _totalAsset = totalAsset;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L232

```solidity
File: SGLLendingCommon.sol

74: _totalAsset = totalAsset;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L74

```solidity
File: SGLLiquidation.sol

79: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L79

```solidity
File: SGLLiquidation.sol

105: _totalBorrow = totalBorrow;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L105

```solidity
File: Seer.sol

42: _name = __name;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/Seer.sol#L42

```solidity
File: Seer.sol

43: _symbol = __symbol;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/Seer.sol#L43

```solidity
File: ARBTriCryptoOracle.sol

53: _name = __name;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L53

```solidity
File: ARBTriCryptoOracle.sol

54: _symbol = __symbol;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L54

```solidity
File: SGOracle.sol

36: _name = __name;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L36

```solidity
File: SGOracle.sol

37: _symbol = __symbol;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L37

```solidity
File: GlpStrategy.sol

168: _feesPending = feesPending;
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L168

```solidity
File: BaseTOFTStorage.sol

62: _decimalCache = _decimal;
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L62



</details>





### <a href="#gas-summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
    if iszero(_addr) {
        mstore(0x00, "AddressZero")
        revert(0x00, 0x20)
    }
}
```

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: TapOFT.sol

function setMinter(address _minter) external onlyOwner {
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L160

```solidity
File: Penrose.sol

function setConservator(address _conservator) external onlyOwner {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L281

```solidity
File: Singularity.sol

function setLiquidationQueueConfig(
        ILiquidationQueue _liquidationQueue,
        address _bidExecutionSwapper,
        address _usdoSwapper
    ) external onlyOwner {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L576


```solidity
File: BaseUSDO.sol

function setConservator(address _conservator) external onlyOwner {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L105



</details>





### <a href="#gas-summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Use nested `if` and avoid multiple check combinations

Using nested `if`, is cheaper than using `&&` multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BaseUSDO.sol

370: if (!success && !_forwardRevert) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L370

```solidity
File: MagnetarV2.sol

1046: if (!success && !allowFailure) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1046

```solidity
File: MagnetarMarketModule.sol

402: if (lendAmount == 0 && depositData.deposit) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

```solidity
File: AaveStrategy.sol

171: if (daysPassed && balanceOfStkAave > 0) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L171

```solidity
File: Balancer.sol

226: if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L226

```solidity
File: TapiocaWrapper.sol

121: if (_revertOnFailure && !success) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L121

```solidity
File: TapiocaWrapper.sol

144: if (_call[i].revertOnFailure && !success) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L144

```solidity
File: BaseTOFT.sol

412: if (!success && !_forwardRevert) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L412

```solidity
File: BoringMath.sol

27: if (roundUp && (result * div) / mul < value) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/BoringMath.sol#L27

```solidity
File: YieldBoxRebase.sol

35: if (roundUp && (share * totalAmount) / totalShares_ < amount) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxRebase.sol#L35

```solidity
File: YieldBoxRebase.sol

58: if (roundUp && (amount * totalShares_) / totalAmount < share) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxRebase.sol#L58



</details>


#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
    
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
    
        function testGas() public {
            c0.checkAge(19);
            c1.checkAgeOptimized(19);
        }
    }
    
    contract Contract0 {
    
        function checkAge(uint8 _age) public returns(string memory){
            if(_age>18 && _age<22){
                return "Eligible";
            }
        }
    
    }
    
    contract Contract1 {
    
        function checkAgeOptimized(uint8 _age) public returns(string memory){
            if(_age>18){
                if(_age<22){
                    return "Eligible";
                }
            }
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76923                                     | 416             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAge                                  | 651             | 651 | 651    | 651 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 76323                                     | 413             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| checkAgeOptimized                         | 645             | 645 | 645    | 645 | 1       |





### <a href="#gas-summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Using XOR (^) and AND (&) bitwise equivalents

Given 4 variables a, b, c and d represented as such:

    0 0 0 0 0 1 1 0 <- a
    0 1 1 0 0 1 1 0 <- b
    0 0 0 0 0 0 0 0 <- c
    1 1 1 1 1 1 1 1 <- d

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: twAML.sol

32: if (prod1 == 0) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L32

```solidity
File: twAML.sol

138: if (_cumulative == 0) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L138

```solidity
File: Vesting.sol

111: if (start == 0 || seeded == 0) revert NotStarted();
113: if (_claimable == 0) revert NothingToClaim();

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L111

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L113



```solidity
File: Vesting.sol

132: if (_user == address(0)) revert AddressNotValid();
133: if (_amount == 0) revert AmountNotValid();

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L132-L133

```solidity
File: Vesting.sol

153: if (_seededAmount == 0) revert NoTokens();

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L153

```solidity
File: Vesting.sol

168: if (start == 0) return 0;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L168

```solidity
File: twTAP.sol

179: if (votes == 0) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L179

```solidity
File: twTAP.sol

365: require(msg.sender == address(tapOFT), "twTAP: only tapOFT");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L365

```solidity
File: twTAP.sol

390: require(msg.sender == address(tapOFT), "twTAP: only tapOFT");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L390

```solidity
File: twTAP.sol

434: lastProcessedWeek == currentWeek(),

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L434

```solidity
File: twTAP.sol

474: msg.sender == tokenOwner ||
475: _to == tokenOwner ||

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L474-L475

```solidity
File: AirdropBroker.sol

176: tapAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L176

```solidity
File: AirdropBroker.sol

207: if (cachedEpoch == 1) {
209: } else if (cachedEpoch == 2) {
211: } else if (cachedEpoch == 3) {
213: } else if (cachedEpoch == 4) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L207

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L209

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L211

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L213



```solidity
File: AirdropBroker.sol

257: uint256 chosenAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L257

```solidity
File: AirdropBroker.sol

329: require(_users.length == _amounts.length, "adb: invalid input");
331: if (_phase == 1) {
335: } else if (_phase == 4) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L329

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L331

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L335



```solidity
File: aoTAP.sol

140: require(broker == address(0), "AOTAP: only once");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L140

```solidity
File: oTAP.sol

127: require(broker == address(0), "OTAP: only once");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L127

```solidity
File: TapiocaOptionBroker.sol

189: tapAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L189

```solidity
File: TapiocaOptionBroker.sol

390: uint256 chosenAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L390

```solidity
File: TapiocaOptionLiquidityProvision.sol

283: activeSingularities[singularity].sglAssetID == 0,

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L283

```solidity
File: TapiocaOptionLiquidityProvision.sol

310: if (i == sglLastIndex) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L310

```solidity
File: BaseTapOFT.sol

60: if (packetType == PT_LOCK_TWTAP) {
62: } else if (packetType == PT_UNLOCK_TWTAP) {
64: } else if (packetType == PT_CLAIM_REWARDS) {
68: if (packetType == PT_SEND) {
70: } else if (packetType == PT_SEND_AND_CALL) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L60

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L62

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L64

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L68

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L70



```solidity
File: TapOFT.sol

176: if (timestamp == 0) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L176

```solidity
File: TapOFT.sol

221: require(msg.sender == minter, "unauthorized");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L221

```solidity
File: Penrose.sol

238: markets_.length == swappers_.length &&
239: swappers_.length == swapData_.length,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L238-L239

```solidity
File: Penrose.sol

272: require(msg.sender == conservator, "Penrose: unauthorized");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L272

```solidity
File: Penrose.sol

509: if (feeShares == 0) return;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L509

```solidity
File: Market.sol

246: require(msg.sender == conservator, "Market: unauthorized");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L246

```solidity
File: Market.sol

314: if (borrowPart == 0) return (0, 0, 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L314

```solidity
File: Market.sol

408: if (borrowPart == 0) return true;
410: if (collateralShare == 0) return false;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L408

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L410



```solidity
File: Market.sol

447: if (borrowed == 0) return 0;
448: if (startTVLInAsset == 0) return 0;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L447-L448

```solidity
File: Market.sol

485: if (numerator == 0 || denominator == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L485

```solidity
File: MarketERC20.sol

140: if (amount != 0 || msg.sender == to) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140

```solidity
File: MarketERC20.sol

277: require(signer == owner, "ERC20Permit: invalid signature");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L277

```solidity
File: BigBang.sol

156: _isEthMarket = collateralId == penrose.wethAssetId();

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L156

```solidity
File: BigBang.sol

182: if (totalBorrow.elastic == 0) return minDebtRate;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L182

```solidity
File: BigBang.sol

472: _isEthMarket = collateralId == penrose.wethAssetId();

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L472

```solidity
File: BigBang.sol

516: if (elapsedTime == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L516

```solidity
File: BigBang.sol

550: if (share == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L550

```solidity
File: BigBang.sol

751: totalBorrowCap == 0 || totalBorrow.elastic <= totalBorrowCap,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L751

```solidity
File: SGLBorrow.sol

26: if (amount == 0) return (0, 0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLBorrow.sol#L26

```solidity
File: SGLCommon.sol

61: utilization = fullAssetAmount == 0
68: if (elapsedTime == 0) {
81: if (_totalBorrow.base == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L61

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L68

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L81



```solidity
File: SGLCommon.sol

182: bytes32 _asset_sig = _assetId == assetId ? ASSET_SIG : COLLATERAL_SIG;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L182

```solidity
File: SGLCommon.sol

207: fraction = allShare == 0

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L207

```solidity
File: SGLCommon.sol

229: if (totalAsset.base == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L229

```solidity
File: SGLLendingCommon.sol

23: if (share == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L23

```solidity
File: SGLLendingCommon.sol

67: totalBorrowCap == 0 || totalBorrow.base <= totalBorrowCap,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L67

```solidity
File: SGLLiquidation.sol

76: if (borrowPart == 0) return 0;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L76

```solidity
File: SGLLiquidation.sol

115: if (borrowAmount == 0) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L115

```solidity
File: Singularity.sol

168: bytes32 sig = _assetId == assetId ? ASSET_SIG : COLLATERAL_SIG;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L168

```solidity
File: Singularity.sol

602: if (_module == Module.Borrow) {
604: } else if (_module == Module.Collateral) {
606: } else if (_module == Module.Liquidation) {
608: } else if (_module == Module.Leverage) {
611: if (module == address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L602

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L604

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L606

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L608

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L611



```solidity
File: BaseUSDO.sol

115: require(msg.sender == conservator, "USDO: unauthorized");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L115

```solidity
File: BaseUSDO.sol

346: if (_module == Module.Leverage) {
348: } else if (_module == Module.Market) {
350: } else if (_module == Module.Options) {
354: if (module == address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L346

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L348

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L350

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L354



```solidity
File: BaseUSDO.sol

407: if (packetType == PT_YB_SEND_SGL_LEND_OR_REPAY) {
423: } else if (packetType == PT_LEVERAGE_MARKET_UP) {
439: } else if (packetType == PT_MARKET_REMOVE_ASSET) {
451: } else if (packetType == PT_MARKET_MULTIHOP_BUY) {
463: } else if (packetType == PT_TAP_EXERCISE) {
479: } else if (packetType == PT_SEND_FROM) {
493: if (packetType == PT_SEND) {
495: } else if (packetType == PT_SEND_AND_CALL) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L407

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L423

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L439

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L451

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L463

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L479

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L493

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L495



```solidity
File: USDO.sol

68: require(token == address(this), "USDO: token not valid");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L68

```solidity
File: USDO.sol

87: require(token == address(this), "USDO: token not valid");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L87

```solidity
File: MagnetarV2.sol

186: fraction = allShare == 0 ? share : (share * totalAssetBase) / allShare;

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L186

```solidity
File: MagnetarV2.sol

218: if (_action.id == PERMIT_ALL) {
225: } else if (_action.id == PERMIT) {
232: } else if (_action.id == TOFT_WRAP) {
249: } else if (_action.id == TOFT_SEND_FROM) {
275: } else if (_action.id == YB_DEPOSIT_ASSET) {
295: } else if (_action.id == MARKET_ADD_COLLATERAL) {
309: } else if (_action.id == MARKET_BORROW) {
325: } else if (_action.id == YB_WITHDRAW_TO) {
367: } else if (_action.id == MARKET_LEND) {
384: } else if (_action.id == MARKET_REPAY) {
401: } else if (_action.id == TOFT_SEND_AND_BORROW) {
438: } else if (_action.id == TOFT_SEND_AND_LEND) {
475: } else if (_action.id == TOFT_DEPOSIT_TO_STRATEGY) {
493: } else if (_action.id == TOFT_RETRIEVE_FROM_STRATEGY) {
528: } else if (_action.id == MARKET_YBDEPOSIT_AND_LEND) {
547: } else if (_action.id == MARKET_YBDEPOSIT_COLLATERAL_AND_BORROW) {
584: } else if (_action.id == MARKET_REMOVE_ASSET) {
601: } else if (_action.id == MARKET_DEPOSIT_REPAY_REMOVE_COLLATERAL) {
622: } else if (_action.id == MARKET_BUY_COLLATERAL) {
636: } else if (_action.id == MARKET_SELL_COLLATERAL) {
649: } else if (_action.id == TAP_EXERCISE_OPTION) {
661: } else if (_action.id == MARKET_MULTIHOP_BUY) {
661: } else if (_action.id == MARKET_MULTIHOP_BUY) {
693: } else if (_action.id == TOFT_REMOVE_AND_REPAY) {
714: require(msg.value == valAccumulator, "MagnetarV2: value mismatch");

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L218

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L225

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L232

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L249

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L275

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L295

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L309

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L325

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L367

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L384

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L401

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L438

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L475

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L493

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L528

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L547

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L584

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L601

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L622

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L636

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L649

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L661

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L661

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L693

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L714



```solidity
File: MagnetarV2.sol

1053: if (_module == Module.Market) {
1057: if (module == address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1053

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1057



```solidity
File: MagnetarV2Storage.sol

337: require(_from == msg.sender, "MagnetarV2: operator not approved");

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L337

```solidity
File: MagnetarMarketModule.sol

402: if (lendAmount == 0 && depositData.deposit) {
457: participateData.tOLPTokenId == tOLPTokenId,

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L457



```solidity
File: MagnetarMarketModule.sol

528: ownerOfTapTokenId == user || ownerOfTapTokenId == address(this),
531: if (ownerOfTapTokenId == user) {
557: tOLPId == removeAndRepayData.unlockData.tokenId,
581: .withdraw || removeAndRepayData.repayAssetOnBB

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L528

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L531

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L557

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L581



```solidity
File: MagnetarMarketModule.sol

690: if (dstChainId == 0) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L690

```solidity
File: Multicall3.sol

90: require(msg.value == valAccumulator, "Multicall3: value mismatch");

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L90

```solidity
File: BaseSwapper.sol

103: success && (data.length == 0 || abi.decode(data, (bool))),

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L103

```solidity
File: BaseSwapper.sol

127: if (amounts.amountIn > 0 || amounts.amountOut > 0) {
132: amountIn = amounts.amountIn == 0
137: amountOut = amounts.amountOut == 0

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L127

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L132

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L137



```solidity
File: UniswapV2Swapper.sol

147: if (data.length == 0) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L147

```solidity
File: UniswapV3Swapper.sol

175: if (data.length == 0) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L175

```solidity
File: ConvexTricryptoStrategy.sol

190: if (data.length == 0) return;

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L190

```solidity
File: ConvexTricryptoStrategy.sol

246: tempData.rewardContracts.length == tempData.tokens.length,

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L246

```solidity
File: ConvexTricryptoStrategy.sol

377: success && (data.length == 0 || abi.decode(data, (bool))),

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L377

```solidity
File: GlpStrategy.sol

91: require(msg.sender == address(gmxWethPool), "Not the pool");

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L91

```solidity
File: GlpStrategy.sol

186: if (available == 0) {
194: if (vestable == 0) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L186

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L194



```solidity
File: GlpStrategy.sol

265: if (vestable == 0) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L265

```solidity
File: GlpStrategy.sol

294: if (vestable == 0) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L294

```solidity
File: GlpStrategy.sol

318: if (gmxAmount == 0) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L318

```solidity
File: Balancer.sol

206: if (msg.value == 0) revert FeeAmountNotSet();

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L206

```solidity
File: Balancer.sol

226: if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L226

```solidity
File: TapiocaWrapper.sol

86: if (tapiocaOFTs.length == 0) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L86

```solidity
File: BaseTOFT.sol

85: if (_decimalCache == 0) return 18;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L85

```solidity
File: BaseTOFT.sol

371: if (erc20 == address(0)) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L371

```solidity
File: BaseTOFT.sol

386: if (_module == Module.Leverage) {
388: } else if (_module == Module.Strategy) {
390: } else if (_module == Module.Market) {
392: } else if (_module == Module.Options) {
396: if (module == address(0)) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L386

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L388

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L390

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L392

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L396



```solidity
File: BaseTOFT.sol

450: if (packetType == PT_YB_SEND_STRAT) {
467: } else if (packetType == PT_YB_RETRIEVE_STRAT) {
480: } else if (packetType == PT_LEVERAGE_MARKET_DOWN) {
496: } else if (packetType == PT_YB_SEND_SGL_BORROW) {
512: } else if (packetType == PT_MARKET_REMOVE_COLLATERAL) {
524: } else if (packetType == PT_MARKET_MULTIHOP_SELL) {
536: } else if (packetType == PT_TAP_EXERCISE) {
551: } else if (packetType == PT_SEND_FROM) {
565: if (packetType == PT_SEND) {
567: } else if (packetType == PT_SEND_AND_CALL) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L450

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L467

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L480

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L496

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L512

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L524

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L536

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L551

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L565

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L567



```solidity
File: mTapiocaOFT.sol

94: if (erc20 == address(0)) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L94

```solidity
File: mTapiocaOFT.sol

144: bool _isNative = erc20 == address(0);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L144

```solidity
File: TapiocaOFT.sol

74: if (erc20 == address(0)) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/TapiocaOFT.sol#L74

```solidity
File: BaseTOFTLeverageModule.sol

272: if (erc20 == address(0)) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272

```solidity
File: NativeTokenFactory.sol

77: require(msg.sender == _pendingOwner, "NTF: caller != pending owner");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L77

```solidity
File: NativeTokenFactory.sol

117: require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L117

```solidity
File: NativeTokenFactory.sol

139: require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L139

```solidity
File: YieldBox.sol

131: if (share == 0) {
142: if (asset.tokenType == TokenType.ERC20) {
148: if (asset.contractAddress == address(this)) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L131

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L142

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L148



```solidity
File: YieldBox.sol

175: require(asset.tokenType == TokenType.ERC721, "YieldBox: not ERC721");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L175

```solidity
File: YieldBox.sol

199: require(asset.tokenType == TokenType.ERC20 && asset.contractAddress == address(wrappedNative), "YieldBox: not wrappedNative");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L199

```solidity
File: YieldBox.sol

235: if (asset.tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L235

```solidity
File: YieldBox.sol

280: if (share == 0) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L280

```solidity
File: YieldBox.sol

435: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L435

```solidity
File: YieldBox.sol

448: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L448

```solidity
File: YieldBox.sol

459: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L459

```solidity
File: YieldBox.sol

487: if (tokenType == TokenType.Native) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L487

```solidity
File: YieldBoxPermit.sol

58: require(signer == owner, "YieldBoxPermit: invalid signature");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L58

```solidity
File: YieldBoxPermit.sol

87: require(signer == owner, "YieldBoxPermit: invalid signature");

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L87

```solidity
File: YieldBoxURIBuilder.sol

24: if (asset.strategy == NO_STRATEGY) {
27: if (asset.tokenType == TokenType.ERC20) {
30: } else if (asset.tokenType == TokenType.ERC1155) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L24

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L27

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L30



```solidity
File: YieldBoxURIBuilder.sol

54: if (asset.strategy == NO_STRATEGY) {
57: if (asset.tokenType == TokenType.ERC20) {
60: } else if (asset.tokenType == TokenType.ERC1155) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L54

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L57

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L60



```solidity
File: YieldBoxURIBuilder.sol

69: if (asset.tokenType == TokenType.ERC1155) {
71: } else if (asset.tokenType == TokenType.ERC20) {

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L69

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L71



```solidity
File: YieldBoxURIBuilder.sol

86: if (asset.tokenType == TokenType.ERC1155) {
93: } else if (asset.tokenType == TokenType.ERC20) {
107: : abi.encodePacked(',"totalSupply":', totalSupply.toString(), ',"fixedSupply":', owner == address(0) ? "true" : "false")
121: asset.tokenType == TokenType.ERC1155 ? "" : ',"decimals":',
122: asset.tokenType == TokenType.ERC1155 ? "" : details.decimals.toString(),
129: asset.tokenType == TokenType.ERC1155 ? string(abi.encodePacked(',"tokenId":', asset.tokenId.toString())) : "",

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L86

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L93

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L107

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L121

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L122

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L129





</details>


#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(1,2);
            c1.optimized(1,2);
        }
    }
    
    contract Contract0 {
        function not_optimized(uint8 a,uint8 b) public returns(bool){
            return ((a==1) || (b==1));
        }
    }
    
    contract Contract1 {
        function optimized(uint8 a,uint8 b) public returns(bool){
            return ((a ^ 1) & (b ^ 1)) == 0;
        }
    }

#### Gas Test Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 46099                                     | 261             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 456             | 456 | 456    | 456 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 42493                                     | 243             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 430             | 430 | 430    | 430 | 1       |



### <a href="#gas-summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Using `this.<fn>()` wastes gas

Calling an external function internally, through the use of `this` wastes the gas overhead of calling an external function (100 gas).

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BaseTapOFT.sol

312: this.sendFrom{value: address(this).balance}(

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L312

```solidity
File: USDOLeverageModule.sol

171: this.leverageUpInternal.selector,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L171

```solidity
File: USDOMarketModule.sol

170: this.lendInternal.selector,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L170

```solidity
File: USDOOptionsModule.sol

176: this.exerciseInternal.selector,

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L176

```solidity
File: BaseTOFTLeverageModule.sol

186: this.leverageDownInternal.selector,

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L186

```solidity
File: BaseTOFTMarketModule.sol

162: this.borrowInternal.selector,

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L162

```solidity
File: BaseTOFTOptionsModule.sol

191: this.exerciseInternal.selector,

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L191

```solidity
File: BaseTOFTStrategyModule.sol

154: this.depositToYieldbox.selector,

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L154



</details>




