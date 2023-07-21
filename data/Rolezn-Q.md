## QA Summary<a name="QA Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#low1-add-to-blacklist-function) | Add to `blacklist` function | 1 |
| [LOW&#x2011;2](#low2-ierc20-approve-is-deprecated) | IERC20 `approve()` Is Deprecated | 26 |
| [LOW&#x2011;3](#low3-approve-typeuint256max-not-work-with-some-tokens) | Approve `type(uint256).max` not work with some tokens | 11 |
| [LOW&#x2011;4](#low4-constant-decimal-values) | Constant decimal values | 10 |
| [LOW&#x2011;5](#low5-double-type-casts-create-complexity-within-the-code) | Double type casts create complexity within the code | 6 |
| [LOW&#x2011;6](#low6-the-erc1155sol-contract-does-not-implement-any-security-features-such-as-access-control-or-event-logging) | The `ERC1155.sol` contract does not implement any security features, such as `access control` or `event logging` | 4 |
| [LOW&#x2011;7](#low7-function-can-risk-gas-exhaustion-on-large-receipt-calls-due-to-multiple-mandatory-loops) | Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops | 3 |
| [LOW&#x2011;8](#low8-hardcoded-string-that-is-repeatedly-used-can-be-replaced-with-a-constant) | Hardcoded String that is repeatedly used can be replaced with a constant | 6 |
| [LOW&#x2011;9](#low9-init-functions-are-susceptible-to-frontrunning) | Init functions are susceptible to front-running | 2 |
| [LOW&#x2011;10](#low10-lack-of-checkseffectsinteractions) | Lack of checks-effects-interactions | 2 |
| [LOW&#x2011;11](#low11-minting-tokens-to-the-zero-address-should-be-avoided) | Minting tokens to the zero address should be avoided | 3 |
| [LOW&#x2011;12](#low12-constructor-is-missing-zero-address-check) | Constructor is missing zero address check | 16 |
| [LOW&#x2011;13](#low13-missing-contractexistence-checks-before-lowlevel-calls) | Missing Contract-existence Checks Before Low-level Calls | 11 |
| [LOW&#x2011;14](#low14-contracts-are-not-using-their-oz-upgradeable-counterparts) | Contracts are not using their OZ Upgradeable counterparts | 60 |
| [LOW&#x2011;15](#low15-remove-unused-code) | Remove unused code | 3 |
| [LOW&#x2011;16](#low16-upgrade-openzeppelin-contract-dependency) | Upgrade OpenZeppelin Contract Dependency | 6 |
| [LOW&#x2011;17](#low17-use-safecast-to-safely-downcast-variables) | Use SafeCast to safely downcast variables | 64 |

Total: 234 contexts over 17 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:||
| [NC&#x2011;1](#nc1-blocktimestamp-is-already-used-when-emitting-events-no-need-to-input-timestamp) | `block.timestamp` is already used when emitting events, no need to input timestamp | 1 |
| [NC&#x2011;2](#nc2-mint-events-missing-key-information) | Mint events missing key information | 5 |
| [NC&#x2011;3](#nc3-enum-values-should-be-used-in-place-of-constant-array-indexes) | Enum values should be used in place of constant array indexes | 8 | |
| [NC&#x2011;4](#nc4-function-names-should-differ-to-make-the-code-more-readable) | Function names should differ to make the code more readable | 10 |
| [NC&#x2011;5](#nc5-function-writing-that-does-not-comply-with-the-solidity-style-guide) | Function writing that does not comply with the Solidity Style Guide | 10 |
| [NC&#x2011;6](#nc6-generate-perfect-code-headers-for-better-solidity-code-layout-and-readability) | Generate perfect code headers for better solidity code layout and readability | 4 |
| [NC&#x2011;7](#nc7-large-assembly-blocks-should-have-extensive-comments) | Large assembly blocks should have extensive comments | 9 |
| [NC&#x2011;8](#nc8-numeric-values-having-to-do-with-time-should-use-time-units-for-readability) | Numeric values having to do with time should use time units for readability | 5 |
| [NC&#x2011;9](#nc9-redundant-cast) | Redundant Cast | 2 |
| [NC&#x2011;10](#nc10-remove-unused-error-definition) | Remove unused `error` definition | 1 |
| [NC&#x2011;11](#nc11-uint-variables-should-have-the-bit-size-defined-explicitly) | `uint` variables should have the bit size defined explicitly | 3 |
| [NC&#x2011;12](#nc12-use-named-function-calls) | Use named function calls | 5 |
| [NC&#x2011;13](#nc13-use-safepermit-in-place-of-permit) | Use safePermit in place of permit | 7 |
| [NC&#x2011;14](#nc14-use-smtchecker) | Use SMTChecker | 1 |
| [NC&#x2011;15](#nc15-utilizing-delegatecall-within-a-loop) | Utilizing `delegatecall` within a loop | 2 |

Total: 73 contexts over 15 issues

## Low Risk Issues

### <a href="#qa-summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Add to `blacklist` function

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```


#### <ins>Recommended Mitigation Steps</ins>
Add to Blacklist function and modifier.



### <a href="#qa-summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> IERC20 `approve()` Is Deprecated

`approve()` is subject to a known front-running attack. It is deprecated in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#IERC20-approve-address-uint256-

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BigBang.sol

450: asset.approve(address(yieldBox), totalFees);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L450

```solidity
File: BigBang.sol

761: asset.approve(address(yieldBox), amount);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L761

```solidity
File: USDOLeverageModule.sol

217: IERC20(swapData.tokenOut).approve(externalData.tOft, amountOut);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L217

```solidity
File: MagnetarMarketModule.sol

157: IERC20(collateralAddress).approve(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L157

```solidity
File: MagnetarMarketModule.sol

234: IERC20(assetAddress).approve(address(yieldBox), depositAmount);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L234

```solidity
File: MagnetarMarketModule.sol

339: IERC20(bbCollateralAddress).approve(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L339

```solidity
File: MagnetarMarketModule.sol

386: IERC20(sglAssetAddress).approve(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L386

```solidity
File: MagnetarMarketModule.sol

428: IERC20(address(singularity)).approve(address(yieldBox), fraction);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L428

```solidity
File: ConvexTricryptoStrategy.sol

172: rewardToken.approve(address(swapper), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L172

```solidity
File: ConvexTricryptoStrategy.sol

174: rewardToken.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L174

```solidity
File: ConvexTricryptoStrategy.sol

181: wrappedNative.approve(address(lpGetter), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L181

```solidity
File: ConvexTricryptoStrategy.sol

183: wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L183

```solidity
File: TricryptoLPStrategy.sol

143: rewardToken.approve(address(swapper), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L143

```solidity
File: TricryptoLPStrategy.sol

144: rewardToken.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L144

```solidity
File: TricryptoLPStrategy.sol

152: wrappedNative.approve(address(lpGetter), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L152

```solidity
File: TricryptoLPStrategy.sol

154: wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L154

```solidity
File: TricryptoNativeStrategy.sol

134: rewardToken.approve(address(swapper), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L134

```solidity
File: TricryptoNativeStrategy.sol

135: rewardToken.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L135

```solidity
File: TricryptoNativeStrategy.sol

143: wrappedNative.approve(address(lpGetter), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L143

```solidity
File: TricryptoNativeStrategy.sol

145: wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L145

```solidity
File: GlpStrategy.sol

175: weth.approve(address(glpManager), wethAmount);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L175

```solidity
File: StargateStrategy.sol

151: stgTokenReward.approve(address(swapper), 0);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L151

```solidity
File: StargateStrategy.sol

153: stgTokenReward.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L153

```solidity
File: Balancer.sol

321: erc20.approve(address(router), _amount);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L321

```solidity
File: BaseTOFTLeverageModule.sol

215: IERC20(erc20).approve(externalData.swapper, amount);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L215

```solidity
File: BaseTOFTStrategyModule.sol

184: _erc20.approve(address(yieldBox), _amount);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L184



</details>

#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeIncreaseAllowance()` / `safeDecreaseAllowance()` instead.



### <a href="#qa-summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Approve `type(uint256).max` not work with some tokens

Source: https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ConvexTricryptoStrategy.sol

174: rewardToken.approve(_swapper, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L174

```solidity
File: ConvexTricryptoStrategy.sol

105: wrappedNative.approve(_lpGetter, type(uint256).max);
183: wrappedNative.approve(_lpGetter, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L105

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L183



```solidity
File: TricryptoLPStrategy.sol

144: rewardToken.approve(_swapper, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L144

```solidity
File: TricryptoLPStrategy.sol

82: wrappedNative.approve(_lpGetter, type(uint256).max);
154: wrappedNative.approve(_lpGetter, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L82

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L154



```solidity
File: TricryptoNativeStrategy.sol

135: rewardToken.approve(_swapper, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L135

```solidity
File: TricryptoNativeStrategy.sol

81: wrappedNative.approve(_lpGetter, type(uint256).max);
145: wrappedNative.approve(_lpGetter, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L81

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L145



```solidity
File: StargateStrategy.sol

94: stgTokenReward.approve(_swapper, type(uint256).max);
153: stgTokenReward.approve(_swapper, type(uint256).max);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L94

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L153





</details>





### <a href="#qa-summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Constant decimal values

The use of fixed decimal values such as 1e18 or 1e8 in Solidity contracts can lead to inaccuracies, bugs, and vulnerabilities, particularly when interacting with tokens having different decimal configurations. Not all ERC20 tokens follow the standard 18 decimal places, and assumptions about decimal places can lead to miscalculations.

Resolution: Always retrieve and use the decimals() function from the token contract itself when performing calculations involving token amounts. This ensures that your contract correctly handles tokens with any number of decimal places, mitigating the risk of numerical errors or under/overflows that could jeopardize contract integrity and user funds.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: AirdropBroker.sol

434: uint256 eligibleAmount = uint256(PHASE_2_AMOUNT_PER_USER[_role]) * 1e18;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L434

```solidity
File: Market.sol

295: uint256 x = (numerator * 1e18) / denominator;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L295

```solidity
File: BigBang.sol

188: debtRateAgainstEthMarket) / 1e18;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L188

```solidity
File: BigBang.sol

533: elapsedTime) /
            1e18;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L533

```solidity
File: SGLCommon.sol

102: elapsedTime) /
            1e18;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L102

```solidity
File: ARBTriCryptoOracle.sol

121: uint256 _btcPrice = uint256(BTC_FEED.latestAnswer()) * 1e10;
122: uint256 _wbtcPrice = uint256(WBTC_FEED.latestAnswer()) * 1e10;
123: uint256 _ethPrice = uint256(ETH_FEED.latestAnswer()) * 1e10;
124: uint256 _usdtPrice = uint256(USDT_FEED.latestAnswer()) * 1e10;
127: ? (_wbtcPrice * _btcPrice) / 1e18

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L121

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L122

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L123

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L124

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L127





</details>





### <a href="#qa-summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Double type casts create complexity within the code

Double type casting should be avoided in Solidity contracts to prevent unintended consequences and ensure accurate data representation. Performing multiple type casts in succession can lead to unexpected truncation, rounding errors, or loss of precision, potentially compromising the contract's functionality and reliability. Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging. To ensure precise and consistent data handling, developers should use appropriate data types and avoid unnecessary or excessive type casting, promoting a more robust and dependable contract execution.

#### <ins>Proof Of Concept</ins>

```solidity
File: CurveSwapper.sol

152: address tokenIn = curvePool.coins(uint256(uint128(i)));
153: address tokenOut = curvePool.coins(uint256(uint128(j)));

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/CurveSwapper.sol#L152-L153

```solidity
File: YieldBoxURIBuilder.sol

37: uint256(uint160(asset.contractAddress)).toHexString(20),
90: uint256(uint160(asset.contractAddress)).toHexString(20),
106: uint256(uint160(asset.contractAddress)).toHexString(20),

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L37

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L90

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L106



```solidity
File: YieldBoxURIBuilder.sol

124: uint256(uint160(address(asset.strategy))).toHexString(20),

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L124








### <a href="#qa-summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> The `ERC1155.sol` contract does not implement any security features, such as `access control` or `event logging`

This means that anyone can mint tokens, transfer tokens, and burn tokens.

#### <ins>Proof Of Concept</ins>


```solidity
File: YieldBox.sol

29: import "@boringcrypto/boring-solidity/contracts/interfaces/IERC1155.sol";
34: import "./ERC1155.sol";

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L29

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L34



```solidity
File: YieldBoxRebase.sol

6: import "@boringcrypto/boring-solidity/contracts/interfaces/IERC1155.sol";
12: import "./ERC1155.sol";

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxRebase.sol#L6

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxRebase.sol#L12











### <a href="#qa-summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops

The function contains loops over arrays that the user cannot influence. 
In any call for the function, users spend gas to iterate over the array. There are loops in the following functions that iterate all array values. Which could risk gas exhaustion on large array calls.

This risk is small, but could be lessened somewhat by allowing separate calls for small loops.
Consider allowing partial calls via array arguments, or detecting expensive call bundles and only partially iterating over them. Since the logic already filters out calls, this would make the later loops smaller.

#### <ins>Proof Of Concept</ins>


```solidity
File: BalancerStrategy.sol

149: function _vaultDeposit(uint256 amount) private {
        uint256 lpBalanceBefore = pool.balanceOf(address(this));

        (address[] memory poolTokens, , ) = vault.getPoolTokens(poolId);

        int256 index = -1;
        uint256[] memory maxAmountsIn = new uint256[](poolTokens.length);
        for (uint256 i = 0; i < poolTokens.length; i++) {
            if (poolTokens[i] == address(wrappedNative)) {
                maxAmountsIn[i] = amount;
                index = int256(i);
            } else {
                maxAmountsIn[i] = 0;
            }
        }
        ...
    }

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L149

```solidity
File: BalancerStrategy.sol

218: function _vaultWithdraw(uint256 amount) private returns (uint256) {
        uint256 wrappedNativeBalanceBefore = wrappedNative.balanceOf(
            address(this)
        );
        (address[] memory poolTokens, , ) = vault.getPoolTokens(poolId);
        int256 index = -1;
        uint256[] memory minAmountsOut = new uint256[](poolTokens.length);
        for (uint256 i = 0; i < poolTokens.length; i++) {
            if (poolTokens[i] == address(wrappedNative)) {
                minAmountsOut[i] = amount;
                index = int256(i);
            } else {
                minAmountsOut[i] = 0;
            }
        }
        ...
    }

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L218

```solidity
File: BalancerStrategy.sol

271: function updateCache() public returns (uint256) {
        uint256 lpBalance = pool.balanceOf(address(this));

        (address[] memory poolTokens, , ) = vault.getPoolTokens(poolId);
        uint256 index;
        uint256[] memory minAmountsOut = new uint256[](poolTokens.length);
        for (uint256 i = 0; i < poolTokens.length; i++) {
            if (poolTokens[i] == address(wrappedNative)) {
                index = i;
            }
            minAmountsOut[i] = 0;
        }
        ...
    }
}

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L271







### <a href="#qa-summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Hardcoded String that is repeatedly used can be replaced with a constant

Some strings are repeatedly used in the contracts. For better maintainability, please consider replacing it with a `constant`.
 
#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: TapiocaOptionLiquidityProvision.sol

72: ERC721Permit("TapiocaOptionLiquidityProvision")
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L72


```solidity
File: MagnetarMarketModule.sol

483: "0x"
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L483

```solidity
File: MagnetarMarketModule.sol

536: "0x"
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L536

```solidity
File: MagnetarMarketModule.sol

547: "0x"
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L547

```solidity
File: YieldBoxURIBuilder.sol

88: details.tokenType = "ERC1155";
```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L88

```solidity
File: YieldBoxURIBuilder.sol

92: details.symbol = "ERC1155";
```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L92



</details>





### <a href="#qa-summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Init functions are susceptible to front-running

The `init` functions below are not called by another contract atomically after the contract is deployed, so it's possible for a malicious user to call `initialize()` which, if it's noticed in time, would require the project to re-deploy the contract in order to properly initialize. Consider creating a factory contract, which will `new` and `init` each contract atomically.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BigBang.sol

function init(bytes calldata data) external onlyOnce {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L101

```solidity
File: Singularity.sol

function init(bytes calldata data) external onlyOnce {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L62


</details>




### <a href="#qa-summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Lack of checks-effects-interactions

It's recommended to execute external calls after state changes, to prevent reentrancy bugs.

Consider moving the external calls after the state changes on the following instances.

#### <ins>Proof Of Concept</ins>

```solidity
File: twTAP.sol

260: function participate(
        address _participant,
        uint256 _amount,
        uint256 _duration
    ) external returns (uint256 tokenId) {
        require(_duration >= EPOCH_DURATION, "twTAP: Lock not a week");

        
        tapOFT.transferFrom(msg.sender, address(this), _amount); // <== @audit

        
        TWAMLPool memory pool = twAML;

        uint256 magnitude = computeMagnitude(_duration, pool.cumulative);
        bool divergenceForce;
        uint256 multiplier = computeTarget(
            dMIN,
            dMAX,
            magnitude,
            pool.cumulative
        );

        
        bool hasVotingPower = _amount >=
            computeMinWeight(pool.totalDeposited, MIN_WEIGHT_FACTOR);
        if (hasVotingPower) {
            pool.totalParticipants++; 
            pool.averageMagnitude =
                (pool.averageMagnitude + magnitude) /
                pool.totalParticipants; 

            
            divergenceForce = _duration > pool.cumulative;

            if (divergenceForce) {
                pool.cumulative += pool.averageMagnitude;
            } else {
                
                if (pool.cumulative > pool.averageMagnitude) {
                    pool.cumulative -= pool.averageMagnitude;
                } else {
                    pool.cumulative = 0;
                }
            }

            
            pool.totalDeposited += _amount;

            twAML = pool; 
            emit AMLDivergence(
                pool.cumulative,
                pool.averageMagnitude,
                pool.totalParticipants
            );
        }

        
        tokenId = ++mintedTWTap;
        _safeMint(_participant, tokenId);

        uint256 expiry = block.timestamp + _duration;
        require(expiry < type(uint56).max, "twTAP: too long");
        
        
        
        
        
        
        
        
        uint256 w0 = currentWeek();
        uint256 w1 = (expiry - creation) / EPOCH_DURATION;

        
        
        uint256 votes = _amount * multiplier;
        participants[tokenId] = Participation({
            averageMagnitude: pool.averageMagnitude,
            hasVotingPower: hasVotingPower,
            divergenceForce: divergenceForce,
            tapReleased: false,
            expiry: uint56(expiry),
            tapAmount: uint88(_amount),
            multiplier: uint24(multiplier),
            lastInactive: uint40(w0),
            lastActive: uint40(w1)
        });

        
        
        
        weekTotals[w0 + 1].netActiveVotes += int256(votes);
        weekTotals[w1 + 1].netActiveVotes -= int256(votes);

        emit Participate(_participant, _amount, multiplier);
        
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L260

```solidity
File: TapiocaOptionLiquidityProvision.sol

186: function lock(
        address _to,
        IERC20 _singularity,
        uint128 _lockDuration,
        uint128 _amount
    ) external returns (uint256 tokenId) {
        require(_lockDuration > 0, "tOLP: lock duration must be > 0");
        require(_amount > 0, "tOLP: amount must be > 0");

        uint256 sglAssetID = activeSingularities[_singularity].sglAssetID;
        require(sglAssetID > 0, "tOLP: singularity not active");

        
        uint256 sharesIn = yieldBox.toShare(sglAssetID, _amount, false);

        yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn); // <== @audit
        activeSingularities[_singularity].totalDeposited += _amount;

        
        tokenId = ++tokenCounter;
        _safeMint(_to, tokenId);

        
        LockPosition storage lockPosition = lockPositions[tokenId];
        lockPosition.lockTime = uint128(block.timestamp);
        lockPosition.sglAssetID = uint128(sglAssetID);
        lockPosition.lockDuration = _lockDuration;
        lockPosition.amount = _amount;

        emit Mint(_to, uint128(sglAssetID), lockPosition);
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L186








### <a href="#qa-summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> Minting tokens to the zero address should be avoided

The core function `mint` is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. `Address(0)` check is missing in both this function and the internal function `_mint`, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: aoTAP.sol


function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _amount
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedAOTAP;
        _safeMint(_to, tokenId);

        AirdropTapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.amount = _amount;

        emit Mint(_to, tokenId, option);
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L109

```solidity
File: oTAP.sol


function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        TapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.tOLP = _tOLP;

        emit Mint(_to, tokenId, option);
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L96

```solidity
File: USDO.sol


function mint(address _to, uint256 _amount) external notPaused {
        require(allowedMinter[_getChainId()][msg.sender], "USDO: unauthorized");
        _mint(_to, _amount);
        emit Minted(_to, _amount);
    }

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L109


</details>





### <a href="#qa-summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> Constructor is missing zero address check

In Solidity, constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a check, there's a risk that an address parameter could be mistakenly set to the zero address (0x0). This could occur due to a mistake or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it are irretrievable. Therefore, it's crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: Vesting.sol

67: constructor: address _owner
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L67

```solidity
File: AirdropBroker.sol

98: constructor: address _paymentTokenBeneficiary
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L98

```solidity
File: aoTAP.sol

39: constructor: address _owner
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L39

```solidity
File: TapiocaOptionBroker.sol

81: constructor: address _paymentTokenBeneficiary
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L81

```solidity
File: TapiocaOptionLiquidityProvision.sol

67: constructor: address _owner
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L67

```solidity
File: LTap.sol

32: constructor: IERC20 _tapToken
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L32

```solidity
File: Penrose.sol

86: constructor: YieldBox _yieldBox
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L86

```solidity
File: BaseUSDOStorage.sol

70: constructor: IYieldBoxBase _yieldBox
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L70

```solidity
File: ARBTriCryptoOracle.sol

44: constructor: ICurvePool pool
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L44

```solidity
File: GLPOracle.sol

10: constructor: IGmxGlpManager glpManager_
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/GLPOracle.sol#L10

```solidity
File: SGOracle.sol

30: constructor: IStargatePool pool
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L30

```solidity
File: CurveSwapper.sol

36: constructor: ICurvePool _curvePool
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/CurveSwapper.sol#L36

```solidity
File: UniswapV2Swapper.sol

30: constructor: IYieldBox _yieldBox
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L30

```solidity
File: UniswapV3Swapper.sol

45: constructor: IYieldBox _yieldBox
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L45

```solidity
File: BaseTOFTStorage.sol

45: constructor: address _erc20
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L45

```solidity
File: YieldBox.sol

91: constructor: IWrappedNative wrappedNative_
```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L91



</details>




### <a href="#qa-summary">[LOW&#x2011;13]</a><a name="LOW&#x2011;13"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


<details>


```solidity
File: Penrose.sol

444: (success[i], result[i]) = mc[i].call(data[i]);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L444


```solidity
File: MagnetarV2.sol

1045: (bool success, bytes memory returnData) = target.call(actionCalldata);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1045

```solidity
File: Multicall3.sol

51: (result.success, result.returnData) = calli.target.call(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L51

```solidity
File: Multicall3.sol

79: (result.success, result.returnData) = calli.target.call{value: val}(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L79

```solidity
File: BaseSwapper.sol

99: (bool success, bytes memory data) = token.call(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L99

```solidity
File: ConvexTricryptoStrategy.sol

356: (bool successEmtptyApproval, ) = token.call(
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L356

```solidity
File: ConvexTricryptoStrategy.sol

369: (bool success, bytes memory data) = token.call(
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L369

```solidity
File: TapiocaWrapper.sol

120: (success, result) = payable(_toft).call{value: msg.value}(_bytecode);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L120

```solidity
File: TapiocaWrapper.sol

141: (success, results[i]) = payable(_call[i].toft).call{
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L141

```solidity
File: BaseTOFT.sol

380: (bool sent, ) = to.call{value: amount}("");
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L380

```solidity
File: BaseTOFTLeverageModule.sol

280: (bool sent, ) = to.call{value: amount}("");
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280

</details>


#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`





### <a href="#qa-summary">[LOW&#x2011;14]</a><a name="LOW&#x2011;14"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

<details>

```solidity
File: Vesting.sol

4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
5: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L4-L5

```solidity
File: twTAP.sol

6: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L6-L7

```solidity
File: AirdropBroker.sol

4: import "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";
6: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
8: import "@openzeppelin/contracts/security/Pausable.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L4-L8

```solidity
File: aoTAP.sol

6: import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L6-L7

```solidity
File: oTAP.sol

5: import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L5-L6

```solidity
File: TapiocaOptionBroker.sol

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
6: import "@openzeppelin/contracts/security/Pausable.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L5-L6

```solidity
File: TapiocaOptionLiquidityProvision.sol

5: import "@openzeppelin/contracts/token/ERC1155/IERC1155Receiver.sol";
7: import "@openzeppelin/contracts/token/ERC1155/IERC1155.sol";
8: import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
9: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
10: import "@openzeppelin/contracts/security/Pausable.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L5-L10

```solidity
File: BaseTapOFT.sol

5: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L5

```solidity
File: LTap.sol

5: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L5

```solidity
File: TapOFT.sol

4: import {ERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L4

```solidity
File: MarketERC20.sol

5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
6: import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L5-L6

```solidity
File: BaseUSDO.sol

8: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
9: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L8-L9

```solidity
File: BaseUSDOStorage.sol

8: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
9: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L8-L9

```solidity
File: MagnetarV2.sol

5: import "@openzeppelin/contracts/access/Ownable.sol";
6: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L5-L6

```solidity
File: MagnetarMarketModule.sol

8: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";
9: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
10: import "@openzeppelin/contracts/token/ERC721/IERC721.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L8-L10

```solidity
File: Multicall3.sol

4: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L4

```solidity
File: ARBTriCryptoOracle.sol

5: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L5

```solidity
File: BaseSwapper.sol

4: import "@openzeppelin/contracts/access/Ownable.sol";
5: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
6: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L4-L6

```solidity
File: CurveSwapper.sol

4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/CurveSwapper.sol#L4

```solidity
File: UniswapV3Swapper.sol

9: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L9

```solidity
File: AaveStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L4

```solidity
File: BalancerStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L4

```solidity
File: CompoundStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L4

```solidity
File: ConvexTricryptoStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L4

```solidity
File: TricryptoLPStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L4

```solidity
File: TricryptoNativeStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L4

```solidity
File: LidoEthStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L4

```solidity
File: StargateStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L4

```solidity
File: YearnStrategy.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L4

```solidity
File: Balancer.sol

7: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L7

```solidity
File: TapiocaWrapper.sol

8: import "@openzeppelin/contracts/utils/Create2.sol";
9: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L8-L9

```solidity
File: BaseTOFTStorage.sol

9: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
10: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
11: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L9-L11

```solidity
File: YieldBox.sol

36: import "@openzeppelin/contracts/utils/Strings.sol";

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L36

```solidity
File: YieldBoxPermit.sol

5: import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
6: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
7: import "@openzeppelin/contracts/utils/Counters.sol";

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L5-L7

```solidity
File: YieldBoxURIBuilder.sol

3: import "@openzeppelin/contracts/utils/Strings.sol";

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L3



</details>

#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts







### <a href="#qa-summary">[LOW&#x2011;15]</a><a name="LOW&#x2011;15"> Remove unused code
This code is not used in the main project contract files, remove it or add event-emit
Code that is not in use, suggests that they should not be present and could potentially contain insecure functionalities.

#### <ins>Proof Of Concept</ins>


```solidity
File: SGLCommon.sol

function _getAmountForBorrowPart
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L254

```solidity
File: BaseTOFT.sol

function _nonblockingLzReceive
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L442

```solidity
File: YearnStrategy.sol

function _deposited
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L121





### <a href="#qa-summary">[LOW&#x2011;16]</a><a name="LOW&#x2011;16"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).
Release: https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0

#### <ins>Proof Of Concept</ins>

Require dependencies to be at least version of 4.9.0 to include critical vulnerability patches.

<details>

```solidity
File: package.json

    "@openzeppelin/contracts": "^4.8.2"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/tap-token-audit/package.json#L25

```solidity
File: package.json

        "@openzeppelin/contracts": "^4.8.2"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/tapioca-bar-audit/package.json#L46

```solidity
File: package.json

        "@openzeppelin/contracts": "^4.5.0"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/tapioca-periph-audit/package.json#L27

```solidity
File: package.json

    "@openzeppelin/contracts": "^4.5.0"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/tapioca-yieldbox-strategies-audit/package.json#L49

```solidity
File: package.json

        "@openzeppelin/contracts": "^4.5.0"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/tapiocaz-audit/package.json#L32

```solidity
File: package.json

        "@openzeppelin/contracts": "^4.8.2"
```

https://github.com/code-423n4/2023-07-tapioca/tree/main/../2023-07-tapioca/YieldBox/package.json#L26



</details>

#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.9.0 via `>=4.9.0`





### <a href="#qa-summary">[LOW&#x2011;17]</a><a name="LOW&#x2011;17"> Use SafeCast to safely downcast variables

Downcasting `int`/`uint`s in Solidity can be unsafe due to the potential for data loss and unintended behavior. When downcasting a larger integer type to a smaller one (e.g., `uint256` to `uint128`), the value may exceed the range of the target type, leading to truncation and loss of significant digits. This data loss can result in unexpected state changes, incorrect calculations, or other contract vulnerabilities, ultimately compromising the contracts functionality and reliability. To prevent these risks, developers should carefully consider the range of values their variables may hold and ensure that proper checks are in place to prevent out-of-range values before performing downcasting. Also consider using OZ SafeCast functionality.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: AirdropBroker.sol

287: lastEpochUpdate = uint64(block.timestamp);
292: epochTAPValuation = uint128(_epochTAPValuation);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L287

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L292



```solidity
File: AirdropBroker.sol

398: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
433: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
458: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
473: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
402: uint128(PHASE_1_DISCOUNT),

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L398-L402

```solidity
File: AirdropBroker.sol

433: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
458: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
473: uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
435: uint128 discount = uint128(PHASE_2_DISCOUNT_PER_USER[_role]) * 1e4;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L433

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L458

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L473

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L435



```solidity
File: AirdropBroker.sol

448: address tokenIDToAddress = address(uint160(_tokenID));
460: uint128 discount = uint128(PHASE_3_DISCOUNT);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L448
https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L460



```solidity
File: AirdropBroker.sol

477: uint128(PHASE_4_DISCOUNT),

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L477



```solidity
File: TapiocaOptionBroker.sol

282: uint128(target),

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L282

```solidity
File: TapiocaOptionLiquidityProvision.sol

195: lockPosition.lockTime = uint128(block.timestamp);
196: lockPosition.sglAssetID = uint128(sglAssetID);
200: emit Mint(_to, uint128(sglAssetID), lockPosition);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L195

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L196

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L200



```solidity
File: BaseTapOFT.sol

67: packetType = _payload.toUint8(0);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L67

```solidity
File: Penrose.sol

302: usdoAssetId = uint96(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L302

```solidity
File: BigBang.sol

521: _accrueInfo.debtRate = uint64(annumDebtRate / 31536000);
523: _accrueInfo.lastAccrued = uint64(block.timestamp);
535: _totalBorrow.elastic += uint128(extraAmount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L521

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L523

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L535



```solidity
File: BigBang.sol

822: totalBorrow.elastic -= uint128(borrowAmount);
823: totalBorrow.base -= uint128(borrowPart);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L822-L823

```solidity
File: SGLCommon.sol

79: _accrueInfo.lastAccrued = uint64(block.timestamp);
104: _totalBorrow.elastic += uint128(extraAmount);
108: _accrueInfo.feesEarnedFraction += uint128(feeFraction);
109: _totalAsset.base = _totalAsset.base + uint128(feeFraction);
117: _accrueInfo.interestPerSecond = uint64(
135: _accrueInfo.interestPerSecond = uint64(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L79

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L104

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L108

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L109

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L117

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L135



```solidity
File: SGLCommon.sol

210: if (_totalAsset.base + uint128(fraction) < 1000) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L210

```solidity
File: SGLCommon.sol

238: _totalAsset.elastic -= uint128(share);
239: _totalAsset.base -= uint128(fraction);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L238-L239

```solidity
File: SGLLendingCommon.sol

76: _totalAsset.elastic -= uint128(share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L76

```solidity
File: SGLLendingCommon.sol

96: totalAsset.elastic = totalShare + uint128(share);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L96

```solidity
File: SGLLiquidation.sol

154: _totalBorrow.elastic -= uint128(allBorrowAmount);
155: _totalBorrow.base -= uint128(allBorrowPart);
195: totalAsset.elastic += uint128(returnedShare - callerShare);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L154

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L155

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L195



```solidity
File: SGLLiquidation.sol

266: totalBorrow.elastic -= uint128(borrowAmount);
267: totalBorrow.base -= uint128(borrowPart);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L266-L267

```solidity
File: SGLLiquidation.sol

284: totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L284

```solidity
File: Singularity.sol

117: accrueInfo.interestPerSecond = uint64(startingInterestPerSecond);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L117

```solidity
File: BaseUSDO.sol

492: packetType = _payload.toUint8(0);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L492

```solidity
File: UniswapV3Swapper.sol

101: uint128(amountIn),

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L101

```solidity
File: UniswapV3Swapper.sol

129: uint128(amountOut),

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L129

```solidity
File: StargateStrategy.sol

202: uint16(lpRouterPid),
254: uint16(lpRouterPid),

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L202

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L254


```solidity
File: BaseTOFT.sol

564: packetType = _payload.toUint8(0);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L564

```solidity
File: BoringMath.sol

7: c = uint128(a);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/BoringMath.sol#L7

```solidity
File: BoringMath.sol

12: c = uint64(a);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/BoringMath.sol#L12

```solidity
File: BoringMath.sol

17: c = uint32(a);

```

https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/BoringMath.sol#L17



</details>





## Non Critical Issues



### <a href="#qa-summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> block.timestamp is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

```solidity
File: Penrose.sol

247: emit ProtocolWithdrawal(markets_, block.timestamp);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L247






### <a href="#qa-summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Mint events missing key information

Some events are missing key information when emitted.
In the following events, they should contain the minted amount and beneficiary to whom it was minted.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: TapOFT.sol

162: emit MinterUpdated(minter, _minter);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L162

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

127: emit SetMinterStatus(_for, _status);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L127

```solidity
File: USDO.sol

112: emit Minted(_to, _amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L112



</details>





### <a href="#qa-summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Enum values should be used in place of constant array indexes

Create a commented enum value to use in place of constant array indexes, this makes the code far easier to understand

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: BigBang.sol

628: _users[0] = user;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L628

```solidity
File: SGLLiquidation.sol

359: _users[0] = user;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L359

```solidity
File: BaseSwapper.sol

171: path[0] = tokenIn;
172: path[1] = tokenOut;

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L171-L172

```solidity
File: UniswapV2Swapper.sol

75: amountOut = amounts[1];
160: amountOut = amounts[1];

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L75

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L160



```solidity
File: UniswapV2Swapper.sol

96: amountIn = amounts[0];

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L96


```solidity
File: AaveStrategy.sol

147: tokens[0] = address(receiptToken);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L147



</details>




### <a href="#qa-summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Function names should differ to make the code more readable

In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging. Using distinct and descriptive function names not only clarifies the purpose and behavior of each function, but also helps prevent unintended function calls or incorrect overriding. By adopting a clear and consistent naming convention, developers can create more comprehensible and maintainable smart contracts.

#### <ins>Proof Of Concept</ins>


```solidity
File: Vesting.sol

79: function claimable
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L79

```solidity
File: Vesting.sol

85: function claimable
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L85

```solidity
File: Vesting.sol

90: function vested
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L90

```solidity
File: Vesting.sol

96: function vested
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L96

```solidity
File: SGOracle.sol

14: function decimals
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L14

```solidity
File: SGOracle.sol

42: function decimals
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L42

```solidity
File: BaseSwapper.sol

25: function buildSwapData
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L25

```solidity
File: BaseSwapper.sol

46: function buildSwapData
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L46

```solidity
File: TapiocaDeployer.sol

56: function computeAddress
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/TapiocaDeployer/TapiocaDeployer.sol#L56

```solidity
File: TapiocaDeployer.sol

67: function computeAddress
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/TapiocaDeployer/TapiocaDeployer.sol#L67






### <a href="#qa-summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: 

twTAP.sol
```



```solidity
File: 

TapOFT.sol
```



```solidity
File: 

Penrose.sol
```



```solidity
File: 

MarketERC20.sol
```



```solidity
File: 

SGLStorage.sol
```



```solidity
File: 

USDO.sol
```



```solidity
File: 

GLPOracle.sol
```



```solidity
File: 

GlpStrategy.sol
```



```solidity
File: 

YieldBox.sol
```



```solidity
File: 

YieldBoxPermit.sol
```





</details>






### <a href="#qa-summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Generate perfect code headers for better solidity code layout and readability

It is recommended to use pre-made headers for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```


#### <ins>Proof Of Concept</ins>


```solidity
File: TapOFT.sol

5: TapOFT.sol

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L5

```solidity
File: Penrose.sol

11: Penrose.sol

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L11

```solidity
File: BigBang.sol

7: BigBang.sol

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L7

```solidity
File: USDO.sol

6: USDO.sol

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L6








### <a href="#qa-summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Large assembly blocks should have extensive comments

Assembly blocks are take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code

#### <ins>Proof Of Concept</ins>


```solidity
File: twAML.sol

21: assembly {
            let mm := mulmod(a, b, not(0))
            prod0 := mul(a, b)
            prod1 := sub(sub(mm, prod0), lt(mm, prod0))
        }
33: assembly {
                result := div(prod0, denominator)
            }
50: assembly {
            remainder := mulmod(a, b, denominator)
        }
54: assembly {
            prod1 := sub(prod1, gt(remainder, prod0))
            prod0 := sub(prod0, remainder)
        }
64: assembly {
            denominator := div(denominator, twos)
        }
69: assembly {
            prod0 := div(prod0, twos)
        }
75: assembly {
            twos := add(div(sub(0, twos), twos), 1)
        }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L21

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L33

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L50

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L54

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L64

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L69

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/twAML.sol#L75



```solidity
File: twTAP.sol

573: assembly {
            chainId := chainid()
        }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L573

```solidity
File: TapiocaDeployer.sol

40: assembly {
            addr := create2(amount, add(bytecode, 0x20), mload(bytecode), salt)
        }

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/TapiocaDeployer/TapiocaDeployer.sol#L40







### <a href="#qa-summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Numeric values having to do with time should use time units for readability

There are <a href='https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units'>units</a> for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: TapOFT.sol

49: uint256 public constant WEEK = 604800;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L49

```solidity
File: BigBang.sol

521: _accrueInfo.debtRate = uint64(annumDebtRate / 31536000); //per second

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L521


```solidity
File: Singularity.sol

114: interestElasticity = 7200e36; // Half or double in 28800 seconds (1 hours) if linear

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L114

```solidity
File: UniswapV3Swapper.sol

97: (int24 tick, ) = OracleLibrary.consult(pool, 60);
126: (int24 tick, ) = OracleLibrary.consult(pool, 60);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L97

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L126


</details>





### <a href="#qa-summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: MagnetarMarketModule.sol

772: address(target)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L772

```solidity
File: MagnetarMarketModule.sol

785: address(target)
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L785



</details>





### <a href="#qa-summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Remove unused `error` definition

Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.

#### <ins>Proof Of Concept</ins>


```solidity
File: TapiocaWrapper.sol

error TapiocaWrapper__MngmtFeeTooHigh

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol







### <a href="#qa-summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> `uint` variables should have the bit size defined explicitly

Instead of using `uint` to declare `uint256`, explicitly define `uint256` to ensure there is no confusion

#### <ins>Proof Of Concept</ins>


```solidity
File: twTAP.sol

155: function getParticipation(
        uint _tokenId
    ) public view returns (Participation memory participant) {

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L155

```solidity
File: MarketERC20.sol

75: function _allowedLend(address from, uint share) internal {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L75

```solidity
File: MarketERC20.sol

84: function _allowedBorrow(address from, uint share) internal {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L84






### <a href="#qa-summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Use named function calls

Code base has an extensive use of named function calls, but it somehow missed one instance where this would be appropriate.

It should use named function calls on function call, as such:
```solidity
    library.exampleFunction{value: _data.amount.value}({
        _id: _data.id,
        _amount: _data.amount.value,
        _token: _data.token,
        _example: "",
        _metadata: _data.metadata
    });
```

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: BaseTapOFT.sol

312: this.sendFrom{value: address(this).balance}(
                address(this),
                _srcChainId,
                LzLib.addressToBytes32(to),
                _amount,
                twTapSendBackAdapterParams
            );

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L312

```solidity
File: Multicall3.sol

79: (result.success, result.returnData) = calli.target.call{value: val}(
                calli.callData
            );

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L79

```solidity
File: LidoEthStrategy.sol

133: stEth.submit{value: queued}(address(0));

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L133

```solidity
File: BaseTOFT.sol

380: (bool sent, ) = to.call{value: amount}("");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L380

```solidity
File: BaseTOFTLeverageModule.sol

280: (bool sent, ) = to.call{value: amount}("");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280



</details>





### <a href="#qa-summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Use safePermit in place of permit

OpenZeppelin's `SafePermit` is designed to facilitate secure and seamless token approvals via off-chain signed messages, mitigating the risks associated with on-chain transactions. It follows the ERC-2612 standard, ensuring compatibility with various wallets and dApps, and aligning with established industry guidelines.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: BaseTapOFT.sol

335: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L335

```solidity
File: USDOLeverageModule.sol

289: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L289

```solidity
File: USDOMarketModule.sol

278: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L278

```solidity
File: USDOOptionsModule.sol

279: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L279

```solidity
File: BaseTOFTLeverageModule.sol

319: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L319

```solidity
File: BaseTOFTMarketModule.sol

295: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L295

```solidity
File: BaseTOFTOptionsModule.sol

294: IERC20Permit(approvals[i].target).permit(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L294



</details>




### <a href="#qa-summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19





### <a href="#qa-summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Utilizing `delegatecall` within a loop

Using `delegatecall` in a `for` loop can lead to high gas costs, as `delegatecall` is an expensive operation and its costs compound when used in a loop. Additionally, it can pose security risks including reentrancy attacks, as it executes code in the calling contract's context. The function selector collisions can also lead to unpredictable behaviour. To mitigate these risks, control the loop's iterations, apply a reentrancy guard, strictly audit contracts called via `delegatecall`, and consider alternatives like `call` or proxy patterns if the use case allows. Always thoroughly vet contracts involved in `delegatecall` operations. 

#### <ins>Proof Of Concept</ins>


```solidity
File: BigBang.sol

216: (bool success, bytes memory result) = address(this).delegatecall(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L216

```solidity
File: Singularity.sol

188: (bool success, bytes memory result) = address(this).delegatecall(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L188



