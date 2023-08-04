|ID|Title|Category|Severity|Instances|
|-|:-|:-:|:-:|:-:|
| [[1](#l-1--void-constructor)] | Void constructor | Coding Style | Low | 5 |
| [[2](#l-2--use-safetransferownership-instead-of-transferownership-function)] | Use `safeTransferOwnership` instead of `transferOwnership` function | Control Flow | Low | 6 |
| [[3](#l-3--gas-griefingtheft-is-possible-on-unsafe-external-call)] | Gas griefing/theft is possible on unsafe external call | Volatile Code | Low | 4 |
| [[4](#l-4--unbounded-loop)] | Unbounded loop | Control Flow | Low | 24 |
| [[5](#l-5--lack-of-support-for-erc1155-nft)] | Lack of support for ERC1155 NFT | Volatile Code | Low | 1 |
| [[6](#l-6--draft-openzeppelin-dependencies)] | Draft Openzeppelin Dependencies | Coding Style | Low | 7 |
| [[7](#l-7--owner-can-renounce-ownership)] | Owner can renounce Ownership | Centralization / Privilege | Low | 2 |
| [[8](#l-8--unused-receive-function-will-lock-ether-in-contract)] | Unused `receive()` Function Will Lock Ether In Contract | Volatile Code | Low | 10 |
| [[9](#l-9--onlyowner-functions-not-accessible-if-owner-renounces-ownership)] | `onlyOwner` functions not accessible if owner renounces ownership | Centralization / Privilege | Low | 52 |
| [[10](#l-10--missing-contract-existence-checks-before-low-level-calls)] | Missing Contract-existence Checks Before Low-level Calls | Control Flow | Low | 4 |
| [[11](#l-11--minting-tokens-to-the-zero-address-should-be-avoided)] | Minting tokens to the zero address should be avoided | Control Flow | Low | 10 |
| [[12](#l-12--functions-return-bool-true-but-cannot-return-false)] | Functions return bool true but cannot return false | Logical Issue | Low | 6 |
| [[13](#l-13--off-by-one-timestamp-error)] | off-by-one timestamp error | Logical Issue | Low | 2 |
| [[14](#l-14--transferownership-should-be-two-step)] | TransferOwnership Should Be Two Step | Logical Issue | Low | 1 |
| [[15](#l-15--hardcode-the-address-causes-no-future-updates)] | Hardcode the address causes no future updates | Volatile Code | Low | 1 |
| [[16](#l-16--abiencodepacked-should-not-be-used-with-dynamic-types-when-passing-the-result-to-a-hash-function-such-as-keccak256)] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | Control Flow | Low | 1 |
| [[17](#l-17--unlocked-compiler-version)] | Unlocked Compiler Version | Control Flow | Low | 1 |
| [[18](#l-18--condition-will-not-revert-when-blocktimestamp-is-==-to-the-compared-variable)] | Condition will not revert when block.timestamp is `==` to the compared variable | Control Flow | Low | 4 |
| [[19](#l-19--for-loops-in-public-or-external-functions-should-be-avoided-due-to-high-gas-costs-and-possible-dos)] | For loops in public or external functions should be avoided due to high gas costs and possible DOS | Control Flow | Low | 6 |
| [[20](#l-20--possible-reentrancy-with-callback-on-transfer-tokens)] | Possible reentrancy with callback on transfer tokens | Control Flow | Low | 4 |
| [[21](#l-21--functions-calling-contracts-with-transfer-hooks-are-missing-reentrancy-guards)] | Functions calling contracts with transfer hooks are missing reentrancy guards | Control Flow | Low | 41 |
| [[22](#l-22--prefer-safetransferfrom-for-erc721-transfers)] | Prefer safeTransferFrom() for ERC721 transfers | Control Flow | Low | 1 |
| [[23](#nc-23--burn-functions-should-be-protected-with-a-modifier)] | Burn functions should be protected with a modifier | Control Flow | Informational | 2 |
| [[24](#nc-24--unused-state-variables)] | Unused State variables | Gas Optimization | Informational | 65 |
| [[25](#nc-25--event-is-not-properly-indexed)] | Event is not properly indexed | Coding Style | Informational | 134 |
| [[26](#nc-26--unused-error)] | Unused Error | Gas Optimization | Informational | 1 |
| [[27](#nc-27--public-functions-not-called-by-the-contract-should-be-declared-external-instead)] | public functions not called by the contract should be declared external instead | Gas Optimization | Informational | 39 |
| [[28](#nc-28--events-may-be-emitted-out-of-order-due-to-reentranc)] | Events may be emitted out of order due to reentranc | Logical Issue | Informational | 19 |
| [[29](#nc-29--internal-functions-not-called-by-the-contract-should-be-removed)] | internal functions not called by the contract should be removed | Gas Optimization | Informational | 54 |
| [[30](#nc-30--unused-event)] | Unused Event | Gas Optimization | Informational | 31 |
| [[31](#nc-31--unused-mapping)] | Unused Mapping | Gas Optimization | Informational | 2 |
| [[32](#nc-32--emitting-storage-values-instead-of-the-memory-one)] | Emitting storage values instead of the memory one. | Gas Optimization | Informational | 25 |

## **L-1** | Void constructor

### **Description** :-

Detect the call to a constructor that is not implemented.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```
```solidity
148:constructor() MarketERC20("Tapioca Singularity") {}
```
#### **Context**:-
[SGLStorage.sol#L148](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L148)
```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```
```solidity
109:constructor(string memory name) EIP712(name, "1") {}
```
#### **Context**:-
[MarketERC20.sol#L109](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L109)
```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```
```solidity
98:constructor() MarketERC20("Tapioca BigBang") {}
```
#### **Context**:-
[BigBang.sol#L98](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L98)
```solidity
File:tap-token-audit/contracts/options/oTAP.sol
```
```solidity
37:constructor() ERC721("Option TAP", "oTAP") ERC721Permit("Option TAP") {}
```
#### **Context**:-
[oTAP.sol#L37](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L37)
```solidity
File:YieldBox/contracts/YieldBoxPermit.sol
```
```solidity
40:constructor(string memory name) EIP712(name, "1") {}
```
#### **Context**:-
[YieldBoxPermit.sol#L40](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L40)

## **L-2** | Use `safeTransferOwnership` instead of `transferOwnership` function

### **Description** :-

`transferOwnership` function is used to change Ownership from Ownable.sol. Use a 2 structure `transferOwnership` which is safer. `safeTransferOwnership`, use it is more secure due to 2-stage ownership transfer.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
79:        transferOwnership
```
#### **Context**:-
[BaseUSDO.sol#L79](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L79)
```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
68:        _transferOwnership
```
#### **Context**:-
[TapiocaWrapper.sol#L68](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L68)
```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```
```solidity
134:        transferOwnership
```
#### **Context**:-
[TapOFT.sol#L134](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L134)
```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```
```solidity
126:        transferOwnership
```
#### **Context**:-
[twTAP.sol#L126](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L126)
```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
```
```solidity
45:        transferOwnership
```
#### **Context**:-
[MagnetarV2.sol#L45](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L45)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
56:    function transferOwnership
```
#### **Context**:-
[NativeTokenFactory.sol#L56](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L56)

## **L-3** | Gas griefing/theft is possible on unsafe external call

### **Description** :-

`return` data `(bool success,)` has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
120:        (success, result) = payable(_toft).call{value: msg.value}
```
#### **Context**:-
[TapiocaWrapper.sol#L120](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L120)
```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
```
```solidity
280:        (bool sent, ) = to.call{value: amount}
```
#### **Context**:-
[BaseTOFTLeverageModule.sol#L280](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```
```solidity
380:        (bool sent, ) = to.call{value: amount}
```
#### **Context**:-
[BaseTOFT.sol#L380](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L380)
```solidity
File:tapioca-periph-audit/contracts/Multicall/Multicall3.sol
```
```solidity
79:            (result.success, result.returnData) = calli.target.call{value: val}
```
#### **Context**:-
[Multicall3.sol#L79](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L79)

## **L-4** | Unbounded loop

### **Description** :-

While looping large collections, it's possible to run out of gas - causing a DOS condition

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol
```
```solidity
244:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[USDOMarketModule.sol#L244](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L244)
```solidity
File:tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol
```
```solidity
245:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[USDOOptionsModule.sol#L245](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L245)
```solidity
File:tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol
```
```solidity
255:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[USDOLeverageModule.sol#L255](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L255)
```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol
```
```solidity
45:for (uint256 i = 0; i < maxBorrowParts.length; i++) {
```
#### **Context**:-
[SGLLiquidation.sol#L45](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L45)
```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol
```
```solidity
107:for (uint256 i = 0; i < users.length; i++) {
```
#### **Context**:-
[SGLLiquidation.sol#L107](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L107)
```solidity
384:for (uint256 i = 0; i < users.length; i++) {
```
#### **Context**:-
[SGLLiquidation.sol#L384](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L384)
```solidity
File:tapioca-bar-audit/contracts/markets/singularity/Singularity.sol
```
```solidity
187:for (uint256 i = 0; i < calls.length; i++) {
```
#### **Context**:-
[Singularity.sol#L187](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L187)
```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```
```solidity
215:for (uint256 i = 0; i < calls.length; i++) {
```
#### **Context**:-
[BigBang.sol#L215](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L215)
```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```
```solidity
666:for (uint256 i = 0; i < users.length; i++) {
```
#### **Context**:-
[BigBang.sol#L666](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L666)
```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
98:for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {
```
#### **Context**:-
[TapiocaWrapper.sol#L98](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L98)
```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
140:for (uint256 i = 0; i < _call.length; i++) {
```
#### **Context**:-
[TapiocaWrapper.sol#L140](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L140)
```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol
```
```solidity
261:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[BaseTOFTMarketModule.sol#L261](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L261)
```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol
```
```solidity
260:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[BaseTOFTOptionsModule.sol#L260](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L260)
```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
```
```solidity
285:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[BaseTOFTLeverageModule.sol#L285](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L285)
```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```
```solidity
332:for (uint256 i = 0; i < _users.length; i++) {
```
#### **Context**:-
[AirdropBroker.sol#L332](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L332)
```solidity
336:for (uint256 i = 0; i < _users.length; i++) {
```
#### **Context**:-
[AirdropBroker.sol#L336](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L336)
```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```
```solidity
333:for (uint256 i = 0; i < approvals.length; ) {
```
#### **Context**:-
[BaseTapOFT.sol#L333](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L333)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```
```solidity
156:for (uint256 i = 0; i < poolTokens.length; i++) {
```
#### **Context**:-
[BalancerStrategy.sol#L156](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L156)
```solidity
225:for (uint256 i = 0; i < poolTokens.length; i++) {
```
#### **Context**:-
[BalancerStrategy.sol#L225](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L225)
```solidity
277:for (uint256 i = 0; i < poolTokens.length; i++) {
```
#### **Context**:-
[BalancerStrategy.sol#L277](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L277)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
195:for (uint256 i = 0; i < tokens.length; i++) {
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L195](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L195)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
255:for (uint256 i = 0; i < tempData.tokens.length; i++) {
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L255](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L255)
```solidity
273:for (uint256 i = 0; i < tempData.tokens.length; i++) {
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L273](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L273)
```solidity
280:for (uint256 i = 0; i < tempData.tokens.length; i++) {
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L280](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L280)

## **L-5** | Lack of support for ERC1155 NFT

### **Description** :-

support ERC1155 transfer as well given the vast popular ERC1155 NFT in NFT community and NFT marketplace.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:YieldBox/contracts/YieldBox.sol
```
```solidity
181:ERC721(asset.contractAddress).safeTransferFrom(from, address(asset.strategy), asset.tokenId);
```
#### **Context**:-
[YieldBox.sol#L181](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L181)

## **L-6** | Draft Openzeppelin Dependencies

### **Description** :-

A draft status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even minor releases. Consider using a stable version for these libraries instead of a draft.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```
```solidity
9:import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[BaseUSDOStorage.sol#L9](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L9)
```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```
```solidity
5:import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[MarketERC20.sol#L5](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L5)
```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
9:import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[BaseUSDO.sol#L9](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L9)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol
```
```solidity
9:import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[BaseTOFTStorage.sol#L9](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L9)
```solidity
File:tap-token-audit/contracts/tokens/LTap.sol
```
```solidity
5:import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[LTap.sol#L5](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L5)
```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```
```solidity
4:import {ERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[TapOFT.sol#L4](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L4)
```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```
```solidity
5:import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
#### **Context**:-
[BaseTapOFT.sol#L5](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L5)

## **L-7** | Owner can renounce Ownership

### **Description** :-

The contract owner is not prevented from renouncing the ownership while the contract is paused, which would cause any user assets stored in the protocol to be locked indefinitely.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
```
```solidity
30:contract MagnetarV2 is Ownable, MagnetarV2Storage {
```
#### **Context**:-
[MagnetarV2.sol#L30](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L30)
```solidity
File:tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol
```
```solidity
11:abstract contract BaseSwapper is Ownable, ReentrancyGuard, ISwapper {
```
#### **Context**:-
[BaseSwapper.sol#L11](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L11)

## **L-8** | Unused `receive()` Function Will Lock Ether In Contract

### **Description** :-

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```
```solidity
68:receive() external payable {}
```
#### **Context**:-
[BaseUSDOStorage.sol#L68](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L68)
```solidity
File:tapioca-bar-audit/contracts/markets/singularity/Singularity.sol
```
```solidity
644:receive() external payable {}
```
#### **Context**:-
[Singularity.sol#L644](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L644)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol
```
```solidity
43:receive() external payable {}
```
#### **Context**:-
[BaseTOFTStorage.sol#L43](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFTStorage.sol#L43)
```solidity
File:tapiocaz-audit/contracts/Balancer.sol
```
```solidity
342:receive() external payable {}
```
#### **Context**:-
[Balancer.sol#L342](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L342)
```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```
```solidity
330:receive() external payable virtual {}
```
#### **Context**:-
[BaseTapOFT.sol#L330](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L330)
```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```
```solidity
340:receive() external payable virtual {}
```
#### **Context**:-
[MagnetarV2Storage.sol#L340](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L340)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```
```solidity
164:receive() external payable {}
```
#### **Context**:-
[CompoundStrategy.sol#L164](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L164)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```
```solidity
169:receive() external payable {}
```
#### **Context**:-
[LidoEthStrategy.sol#L169](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L169)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```
```solidity
271:receive() external payable {}
```
#### **Context**:-
[StargateStrategy.sol#L271](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L271)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```
```solidity
301:receive() external payable {}
```
#### **Context**:-
[BalancerStrategy.sol#L301](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L301)

## **L-9** | `onlyOwner` functions not accessible if owner renounces ownership

### **Description** :-

The owner is able to perform certain privileged activities, but it's possible to set the owner to address(0). This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, therefore limiting any functionality that needs authority.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
88:function setMaxFlashMintable(uint256 _val) external onlyOwner
```
#### **Context**:-
[BaseUSDO.sol#L88](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L88)
```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
96:function setFlashMintFee(uint256 _val) external onlyOwner
```
#### **Context**:-
[BaseUSDO.sol#L96](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L96)
```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
105:function setConservator(address _conservator) external onlyOwner
```
#### **Context**:-
[BaseUSDO.sol#L105](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L105)
```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
125:function setMinterStatus(address _for, bool _status) external onlyOwner
```
#### **Context**:-
[BaseUSDO.sol#L125](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L125)
```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```
```solidity
134:function setBurnerStatus(address _for, bool _status) external onlyOwner
```
#### **Context**:-
[BaseUSDO.sol#L134](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L134)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
256:function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L256](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L256)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
263:function setBigBangEthMarket(address _market) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L263](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L263)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
281:function setConservator(address _conservator) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L281](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L281)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
291:function setUsdoToken(address _usdoToken) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L291](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L291)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
455:function setFeeTo(address feeTo_) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L455](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L455)
```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```
```solidity
464:function setSwapper(ISwapper swapper, bool enable) external onlyOwner
```
#### **Context**:-
[Penrose.sol#L464](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L464)
```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```
```solidity
142:function setBorrowOpeningFee(uint256 _val) external onlyOwner
```
#### **Context**:-
[Market.sol#L142](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L142)
```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```
```solidity
151:function setBorrowCap(uint256 _cap) external notPaused onlyOwner
```
#### **Context**:-
[Market.sol#L151](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L151)
```solidity
File:tap-token-audit/contracts/tokens/LTap.sol
```
```solidity
53:function setLockedUntil(uint256 _lockedUntil) external onlyOwner
```
#### **Context**:-
[LTap.sol#L53](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L53)
```solidity
File:tap-token-audit/contracts/Vesting.sol
```
```solidity
130:function registerUser(address _user, uint256 _amount) external onlyOwner
```
#### **Context**:-
[Vesting.sol#L130](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L130)
```solidity
File:tap-token-audit/contracts/Vesting.sol
```
```solidity
151:function init(IERC20 _token, uint256 _seededAmount) external onlyOwner
```
#### **Context**:-
[Vesting.sol#L151](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L151)
```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```
```solidity
152:function updatePause(bool val) external onlyOwner
```
#### **Context**:-
[TapOFT.sol#L152](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L152)
```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```
```solidity
160:function setMinter(address _minter) external onlyOwner
```
#### **Context**:-
[TapOFT.sol#L160](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L160)
```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```
```solidity
455:function addRewardToken(IERC20 token) external onlyOwner
```
#### **Context**:-
[twTAP.sol#L455](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L455)
```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```
```solidity
326:function setTwTap(address _twTap) external onlyOwner
```
#### **Context**:-
[BaseTapOFT.sol#L326](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L326)
```solidity
File:tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol
```
```solidity
61:function setPoolFee(uint24 _newFee) external onlyOwner
```
#### **Context**:-
[UniswapV3Swapper.sol#L61](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L61)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```
```solidity
90:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[YearnStrategy.sol#L90](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L90)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```
```solidity
101:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[YearnStrategy.sol#L101](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/yearn/YearnStrategy.sol#L101)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```
```solidity
89:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[CompoundStrategy.sol#L89](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L89)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```
```solidity
100:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[CompoundStrategy.sol#L100](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L100)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```
```solidity
93:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[LidoEthStrategy.sol#L93](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L93)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```
```solidity
104:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[LidoEthStrategy.sol#L104](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L104)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```
```solidity
125:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L125](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L125)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```
```solidity
132:function setMultiSwapper(address _swapper) external onlyOwner
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L132](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L132)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```
```solidity
141:function setTricryptoLPGetter(address _lpGetter) external onlyOwner
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L141](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L141)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```
```solidity
182:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L182](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L182)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```
```solidity
134:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[TricryptoLPStrategy.sol#L134](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L134)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```
```solidity
141:function setMultiSwapper(address _swapper) external onlyOwner
```
#### **Context**:-
[TricryptoLPStrategy.sol#L141](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L141)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```
```solidity
150:function setTricryptoLPGetter(address _lpGetter) external onlyOwner
```
#### **Context**:-
[TricryptoLPStrategy.sol#L150](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L150)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```
```solidity
199:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[TricryptoLPStrategy.sol#L199](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L199)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```
```solidity
142:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[StargateStrategy.sol#L142](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L142)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```
```solidity
149:function setMultiSwapper(address _swapper) external onlyOwner
```
#### **Context**:-
[StargateStrategy.sol#L149](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L149)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```
```solidity
193:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[StargateStrategy.sol#L193](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L193)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```
```solidity
122:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[AaveStrategy.sol#L122](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L122)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```
```solidity
129:function setMultiSwapper(address _swapper) external onlyOwner
```
#### **Context**:-
[AaveStrategy.sol#L129](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L129)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```
```solidity
209:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[AaveStrategy.sol#L209](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L209)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```
```solidity
109:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[BalancerStrategy.sol#L109](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L109)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```
```solidity
120:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[BalancerStrategy.sol#L120](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L120)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```
```solidity
104:function harvestGmx(uint256 priceNum, uint256 priceDenom) public onlyOwner
```
#### **Context**:-
[GlpStrategy.sol#L104](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L104)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```
```solidity
113:function setFeeRecipient(address recipient) external onlyOwner
```
#### **Context**:-
[GlpStrategy.sol#L113](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L113)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
148:function emergencyWithdraw() external onlyOwner
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L148](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L148)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
163:function setDepositThreshold(uint256 amount) external onlyOwner
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L163](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L163)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
170:function setMultiSwapper(address _swapper) external onlyOwner
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L170](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L170)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```
```solidity
179:function setTricryptoLPGetter(address _lpGetter) external onlyOwner
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L179](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L179)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
56:function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner
```
#### **Context**:-
[NativeTokenFactory.sol#L56](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L56)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
109:function mint(uint256 tokenId, address to, uint256 amount) public onlyOwner
```
#### **Context**:-
[NativeTokenFactory.sol#L109](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L109)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
127:function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner
```
#### **Context**:-
[NativeTokenFactory.sol#L127](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L127)

## **L-10** | Missing Contract-existence Checks Before Low-level Calls

### **Description** :-

Low-level calls return success if there is no code present at the specified address..

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
120:        (success, result) = payable(_toft).call{value: msg.value}(_bytecode);
```
#### **Context**:-
[TapiocaWrapper.sol#L120](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L120)
```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol
```
```solidity
280:        (bool sent, ) = to.call{value: amount}("");
```
#### **Context**:-
[BaseTOFTLeverageModule.sol#L280](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```
```solidity
380:        (bool sent, ) = to.call{value: amount}("");
```
#### **Context**:-
[BaseTOFT.sol#L380](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L380)
```solidity
File:tapioca-periph-audit/contracts/Multicall/Multicall3.sol
```
```solidity
79:            (result.success, result.returnData) = calli.target.call{value: val}(
```
#### **Context**:-
[Multicall3.sol#L79](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L79)

## **L-11** | Minting tokens to the zero address should be avoided

### **Description** :-

The core function mint is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. Address(0) check is missing in both this function and the internal function _mint, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/USDO.sol
```
```solidity
91:_mint(address(receiver), amount);
```
#### **Context**:-
[USDO.sol#L91](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L91)
```solidity
File:tapioca-bar-audit/contracts/usd0/USDO.sol
```
```solidity
111:_mint(_to, _amount);
```
#### **Context**:-
[USDO.sol#L111](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L111)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```
```solidity
360:_mint(_toAddress, _amount);
```
#### **Context**:-
[BaseTOFT.sol#L360](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L360)
```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```
```solidity
365:_mint(_toAddress, msg.value);
```
#### **Context**:-
[BaseTOFT.sol#L365](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/BaseTOFT.sol#L365)
```solidity
File:tap-token-audit/contracts/tokens/LTap.sol
```
```solidity
43:_mint(msg.sender, amount);
```
#### **Context**:-
[LTap.sol#L43](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L43)
```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```
```solidity
226:_mint(_to, _amount);
```
#### **Context**:-
[TapOFT.sol#L226](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L226)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
110:_mint(to, tokenId, amount);
```
#### **Context**:-
[NativeTokenFactory.sol#L110](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L110)
```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```
```solidity
130:_mint(tos[i], tokenId, amounts[i]);
```
#### **Context**:-
[NativeTokenFactory.sol#L130](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L130)
```solidity
File:YieldBox/contracts/YieldBox.sol
```
```solidity
139:_mint(to, assetId, share);
```
#### **Context**:-
[YieldBox.sol#L139](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L139)
```solidity
204:_mint(to, assetId, share);
```
#### **Context**:-
[YieldBox.sol#L204](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L204)

## **L-12** | Functions return bool true but cannot return false

### **Description** :-



#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-bar-audit/contracts/usd0/USDO.sol
```
```solidity
103:return true;
```
#### **Context**:-
[USDO.sol#L103](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L103)
```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```
```solidity
151:return true;
```
#### **Context**:-
[MarketERC20.sol#L151](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L151)
```solidity
186:return true;
```
#### **Context**:-
[MarketERC20.sol#L186](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L186)
```solidity
198:return true;
```
#### **Context**:-
[MarketERC20.sol#L198](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L198)
```solidity
206:return true;
```
#### **Context**:-
[MarketERC20.sol#L206](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L206)
```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```
```solidity
408:return true;
```
#### **Context**:-
[Market.sol#L408](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L408)

## **L-13** | off-by-one timestamp error

### **Description** :-



#### <ins>**Proof Of Concept**</ins>

```solidity
File:tap-token-audit/contracts/tokens/LTap.sol
```
```solidity
47:block.timestamp > 
```
#### **Context**:-
[LTap.sol#L47](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L47)
```solidity
File:tap-token-audit/contracts/Vesting.sol
```
```solidity
170:block.timestamp < 
```
#### **Context**:-
[Vesting.sol#L170](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L170)

## **L-14** | TransferOwnership Should Be Two Step

### **Description** :-

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```
```solidity
68:_transferOwnership(_owner);
```
#### **Context**:-
[TapiocaWrapper.sol#L68](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L68)

## **L-15** | Hardcode the address causes no future updates

### **Description** :-

In case the addresses change due to reasons such as updating their versions in the future, addresses coded as constants cannot be updated, so it is recommended to add the update option with the onlyOwner modifier.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```
```solidity
44:        IUniswapV3Pool(0x80A9ae39310abf666A87C743d6ebBD0E8C42158E)
```
#### **Context**:-
[GlpStrategy.sol#L44](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/glp/GlpStrategy.sol#L44)

## **L-16** | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

### **Description** :-

Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). Unless there is a compelling reason, `abi.encode` should be preferred. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```
```solidity
418:keccak256(abi.encodePacked(msg.sender));
```
#### **Context**:-
[AirdropBroker.sol#L418](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L418)

## **L-17** | Unlocked Compiler Version

### **Description** :-

The contract has an unlocked compiler version. An unlocked compiler version in the source code of the contract permits the user to compile it at or above a particular version. This, in turn, leads to differences in the generated bytecode between compilations due to differing compiler version numbers. This can lead to ambiguity when debugging as compiler-specific bugs may occur in the codebase that would be hard to identify over a span of multiple compiler versions rather than a specific one.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```
```solidity
2:pragma solidity 0.8.18;
```
#### **Context**:-
[twTAP.sol#L2](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L2)

## **L-18** | Condition will not revert when block.timestamp is `==` to the compared variable

### **Description** :-

The condition does not revert when block.timestamp is equal to the compared > or < variable.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```
```solidity
158:        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");
```
#### **Context**:-
[AirdropBroker.sol#L158](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L158)
```solidity
233:        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");
```
#### **Context**:-
[AirdropBroker.sol#L233](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L233)
```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```
```solidity
159:        if (participant.expiry < block.timestamp) {
```
#### **Context**:-
[twTAP.sol#L159](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L159)
```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```
```solidity
170:            bool daysPassed = (currentCooldown + 12 days) < block.timestamp;
```
#### **Context**:-
[AaveStrategy.sol#L170](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L170)
## **L-19** | For loops in public or external functions should be avoided due to high gas costs and possible DOS
### **Description** :-


#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```

```solidity
437:        for (uint256 i = 0; i < len; ) {
438:            require(
439:                isSingularityMasterContractRegistered[
440:                    masterContractOf[mc[i]]
441:                ] || isBigBangMasterContractRegistered[masterContractOf[mc[i]]],
442:                "Penrose: MC not registered"
443:            );
444:            (success[i], result[i]) = mc[i].call(data[i]);
445:            if (forceSuccess) {
446:                require(success[i], _getRevertMsg(result[i]));
447:            }
448:            ++i;
449:        }
```
#### **Context**:-
[Penrose.sol#L437-L449](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L437-L449)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/Singularity.sol
```

```solidity
187:        for (uint256 i = 0; i < calls.length; i++) {
188:            (bool success, bytes memory result) = address(this).delegatecall(
189:                calls[i]
190:            );
191:            require(success || !revertOnFail, _getRevertMsg(result));
192:            successes[i] = success;
193:            results[i] = _getRevertMsg(result);
194:        }
```
#### **Context**:-
[Singularity.sol#L187-L194](https://github.com/Tapioca-DAO/contracts/markets/singularity/Singularity.sol#L187-L194)

```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```

```solidity
215:        for (uint256 i = 0; i < calls.length; i++) {
216:            (bool success, bytes memory result) = address(this).delegatecall(
217:                calls[i]
218:            );
219:            require(success || !revertOnFail, _getRevertMsg(result));
220:            successes[i] = success;
221:            results[i] = _getRevertMsg(result);
222:        }
```
#### **Context**:-
[BigBang.sol#L215-L222](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L215-L222)

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```

```solidity
98:        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {
99:            harvestableTapiocaOFTs[i].harvestFees();
100:        }
```
#### **Context**:-
[TapiocaWrapper.sol#L98-L100](https://github.com/Tapioca-DAO/contracts/TapiocaWrapper.sol#L98-L100)

```solidity
140:        for (uint256 i = 0; i < _call.length; i++) {
141:            (success, results[i]) = payable(_call[i].toft).call{
142:                value: msg.value
143:            }(_call[i].bytecode);
144:            if (_call[i].revertOnFailure && !success) {
145:                revert TapiocaWrapper__TOFTExecutionFailed(results[i]);
146:            }
147:        }
```
#### **Context**:-
[TapiocaWrapper.sol#L140-L147](https://github.com/Tapioca-DAO/contracts/TapiocaWrapper.sol#L140-L147)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
```

```solidity
202:        for (uint256 i = 0; i < length; i++) {
203:            Call calldata _action = calls[i];
204:            if (!_action.allowFailure) {
205:                require(
206:                    _action.call.length > 0,
207:                    string.concat(
208:                        "MagnetarV2: Missing call for action with index",
209:                        string(abi.encode(i))
210:                    )
211:                );
212:            }
213:
214:            unchecked {
215:                valAccumulator += _action.value;
216:            }
217:
218:            if (_action.id == PERMIT_ALL) {
219:                _permit(
220:                    _action.target,
221:                    _action.call,
222:                    true,
223:                    _action.allowFailure
224:                );
225:            } else if (_action.id == PERMIT) {
226:                _permit(
227:                    _action.target,
228:                    _action.call,
229:                    false,
230:                    _action.allowFailure
231:                );
232:            } else if (_action.id == TOFT_WRAP) {
233:                WrapData memory data = abi.decode(_action.call[4:], (WrapData));
234:                _checkSender(data.from);
235:                if (_action.value > 0) {
236:                    unchecked {
237:                        valAccumulator += _action.value;
238:                    }
239:                    ITapiocaOFT(_action.target).wrapNative{
240:                        value: _action.value
241:                    }(data.to);
242:                } else {
243:                    ITapiocaOFT(_action.target).wrap(
244:                        msg.sender,
245:                        data.to,
246:                        data.amount
247:                    );
248:                }
249:            } else if (_action.id == TOFT_SEND_FROM) {
250:                (
251:                    address from,
252:                    uint16 dstChainId,
253:                    bytes32 to,
254:                    uint256 amount,
255:                    ISendFrom.LzCallParams memory lzCallParams
256:                ) = abi.decode(
257:                        _action.call[4:],
258:                        (
259:                            address,
260:                            uint16,
261:                            bytes32,
262:                            uint256,
263:                            (ISendFrom.LzCallParams)
264:                        )
265:                    );
266:                _checkSender(from);
267:
268:                ISendFrom(_action.target).sendFrom{value: _action.value}(
269:                    msg.sender,
270:                    dstChainId,
271:                    to,
272:                    amount,
273:                    lzCallParams
274:                );
275:            } else if (_action.id == YB_DEPOSIT_ASSET) {
276:                YieldBoxDepositData memory data = abi.decode(
277:                    _action.call[4:],
278:                    https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999DepositData)
279:                );
280:                _checkSender(data.from);
281:
282:                (uint256 amountOut, uint256 shareOut) = IYieldBoxBase(
283:                    _action.target
284:                ).depositAsset(
285:                        data.assetId,
286:                        msg.sender,
287:                        data.to,
288:                        data.amount,
289:                        data.share
290:                    );
291:                returnData[i] = Result({
292:                    success: true,
293:                    returnData: abi.encode(amountOut, shareOut)
294:                });
295:            } else if (_action.id == MARKET_ADD_COLLATERAL) {
296:                SGLAddCollateralData memory data = abi.decode(
297:                    _action.call[4:],
298:                    (SGLAddCollateralData)
299:                );
300:                _checkSender(data.from);
301:
302:                IMarket(_action.target).addCollateral(
303:                    msg.sender,
304:                    data.to,
305:                    data.skim,
306:                    data.amount,
307:                    data.share
308:                );
309:            } else if (_action.id == MARKET_BORROW) {
310:                SGLBorrowData memory data = abi.decode(
311:                    _action.call[4:],
312:                    (SGLBorrowData)
313:                );
314:                _checkSender(data.from);
315:
316:                (uint256 part, uint256 share) = IMarket(_action.target).borrow(
317:                    msg.sender,
318:                    data.to,
319:                    data.amount
320:                );
321:                returnData[i] = Result({
322:                    success: true,
323:                    returnData: abi.encode(part, share)
324:                });
325:            } else if (_action.id == YB_WITHDRAW_TO) {
326:                (
327:                    address yieldBox,
328:                    address from,
329:                    uint256 assetId,
330:                    uint16 dstChainId,
331:                    bytes32 receiver,
332:                    uint256 amount,
333:                    uint256 share,
334:                    bytes memory adapterParams,
335:                    address payable refundAddress
336:                ) = abi.decode(
337:                        _action.call[4:],
338:                        (
339:                            address,
340:                            address,
341:                            uint256,
342:                            uint16,
343:                            bytes32,
344:                            uint256,
345:                            uint256,
346:                            bytes,
347:                            address
348:                        )
349:                    );
350:
351:                _executeModule(
352:                    Module.Market,
353:                    abi.encodeWithSelector(
354:                        MagnetarMarketModule.withdrawToChain.selector,
355:                        yieldBox,
356:                        from,
357:                        assetId,
358:                        dstChainId,
359:                        receiver,
360:                        amount,
361:                        share,
362:                        adapterParams,
363:                        refundAddress,
364:                        _action.value
365:                    )
366:                );
367:            } else if (_action.id == MARKET_LEND) {
368:                SGLLendData memory data = abi.decode(
369:                    _action.call[4:],
370:                    (SGLLendData)
371:                );
372:                _checkSender(data.from);
373:
374:                uint256 fraction = IMarket(_action.target).addAsset(
375:                    msg.sender,
376:                    data.to,
377:                    data.skim,
378:                    data.share
379:                );
380:                returnData[i] = Result({
381:                    success: true,
382:                    returnData: abi.encode(fraction)
383:                });
384:            } else if (_action.id == MARKET_REPAY) {
385:                SGLRepayData memory data = abi.decode(
386:                    _action.call[4:],
387:                    (SGLRepayData)
388:                );
389:                _checkSender(data.from);
390:
391:                uint256 amount = IMarket(_action.target).repay(
392:                    msg.sender,
393:                    data.to,
394:                    data.skim,
395:                    data.part
396:                );
397:                returnData[i] = Result({
398:                    success: true,
399:                    returnData: abi.encode(amount)
400:                });
401:            } else if (_action.id == TOFT_SEND_AND_BORROW) {
402:                (
403:                    address from,
404:                    address to,
405:                    uint16 lzDstChainId,
406:                    bytes memory airdropAdapterParams,
407:                    ITapiocaOFT.IBorrowParams memory borrowParams,
408:                    ICommonData.IWithdrawParams memory withdrawParams,
409:                    ICommonData.ISendOptions memory options,
410:                    ICommonData.IApproval[] memory approvals
411:                ) = abi.decode(
412:                        _action.call[4:],
413:                        (
414:                            address,
415:                            address,
416:                            uint16,
417:                            bytes,
418:                            ITapiocaOFT.IBorrowParams,
419:                            ICommonData.IWithdrawParams,
420:                            ICommonData.ISendOptions,
421:                            ICommonData.IApproval[]
422:                        )
423:                    );
424:                _checkSender(from);
425:
426:                ITapiocaOFT(_action.target).sendToYBAndBorrow{
427:                    value: _action.value
428:                }(
429:                    msg.sender,
430:                    to,
431:                    lzDstChainId,
432:                    airdropAdapterParams,
433:                    borrowParams,
434:                    withdrawParams,
435:                    options,
436:                    approvals
437:                );
438:            } else if (_action.id == TOFT_SEND_AND_LEND) {
439:                (
440:                    address from,
441:                    address to,
442:                    uint16 dstChainId,
443:                    address zroPaymentAddress,
444:                    IUSDOBase.ILendOrRepayParams memory lendParams,
445:                    ICommonData.IApproval[] memory approvals,
446:                    ICommonData.IWithdrawParams memory withdrawParams,
447:                    bytes memory adapterParams
448:                ) = abi.decode(
449:                        _action.call[4:],
450:                        (
451:                            address,
452:                            address,
453:                            uint16,
454:                            address,
455:                            (IUSDOBase.ILendOrRepayParams),
456:                            (ICommonData.IApproval[]),
457:                            (ICommonData.IWithdrawParams),
458:                            bytes
459:                        )
460:                    );
461:                _checkSender(from);
462:
463:                IUSDOBase(_action.target).sendAndLendOrRepay{
464:                    value: _action.value
465:                }(
466:                    msg.sender,
467:                    to,
468:                    dstChainId,
469:                    zroPaymentAddress,
470:                    lendParams,
471:                    approvals,
472:                    withdrawParams,
473:                    adapterParams
474:                );
475:            } else if (_action.id == TOFT_DEPOSIT_TO_STRATEGY) {
476:                TOFTSendToStrategyData memory data = abi.decode(
477:                    _action.call[4:],
478:                    (TOFTSendToStrategyData)
479:                );
480:                _checkSender(data.from);
481:
482:                ITapiocaOFT(_action.target).sendToStrategy{
483:                    value: _action.value
484:                }(
485:                    msg.sender,
486:                    data.to,
487:                    data.amount,
488:                    data.share,
489:                    data.assetId,
490:                    data.lzDstChainId,
491:                    data.options
492:                );
493:            } else if (_action.id == TOFT_RETRIEVE_FROM_STRATEGY) {
494:                (
495:                    address from,
496:                    uint256 amount,
497:                    uint256 share,
498:                    uint256 assetId,
499:                    uint16 lzDstChainId,
500:                    address zroPaymentAddress,
501:                    bytes memory airdropAdapterParam
502:                ) = abi.decode(
503:                        _action.call[4:],
504:                        (
505:                            address,
506:                            uint256,
507:                            uint256,
508:                            uint256,
509:                            uint16,
510:                            address,
511:                            bytes
512:                        )
513:                    );
514:
515:                _checkSender(from);
516:
517:                ITapiocaOFT(_action.target).retrieveFromStrategy{
518:                    value: _action.value
519:                }(
520:                    msg.sender,
521:                    amount,
522:                    share,
523:                    assetId,
524:                    lzDstChainId,
525:                    zroPaymentAddress,
526:                    airdropAdapterParam
527:                );
528:            } else if (_action.id == MARKET_YBDEPOSIT_AND_LEND) {
529:                HelperLendData memory data = abi.decode(
530:                    _action.call[4:],
531:                    (HelperLendData)
532:                );
533:
534:                _executeModule(
535:                    Module.Market,
536:                    abi.encodeWithSelector(
537:                        MagnetarMarketModule.mintFromBBAndLendOnSGL.selector,
538:                        data.user,
539:                        data.lendAmount,
540:                        data.mintData,
541:                        data.depositData,
542:                        data.lockData,
543:                        data.participateData,
544:                        data.externalContracts
545:                    )
546:                );
547:            } else if (_action.id == MARKET_YBDEPOSIT_COLLATERAL_AND_BORROW) {
548:                (
549:                    address market,
550:                    address user,
551:                    uint256 collateralAmount,
552:                    uint256 borrowAmount,
553:                    ,
554:                    bool deposit,
555:                    ICommonData.IWithdrawParams memory withdrawParams
556:                ) = abi.decode(
557:                        _action.call[4:],
558:                        (
559:                            address,
560:                            address,
561:                            uint256,
562:                            uint256,
563:                            bool,
564:                            bool,
565:                            ICommonData.IWithdrawParams
566:                        )
567:                    );
568:
569:                _executeModule(
570:                    Module.Market,
571:                    abi.encodeWithSelector(
572:                        MagnetarMarketModule
573:                            .depositAddCollateralAndBorrowFromMarket
574:                            .selector,
575:                        market,
576:                        user,
577:                        collateralAmount,
578:                        borrowAmount,
579:                        false,
580:                        deposit,
581:                        withdrawParams
582:                    )
583:                );
584:            } else if (_action.id == MARKET_REMOVE_ASSET) {
585:                HelperMarketRemoveAndRepayAsset memory data = abi.decode(
586:                    _action.call[4:],
587:                    (HelperMarketRemoveAndRepayAsset)
588:                );
589:
590:                _executeModule(
591:                    Module.Market,
592:                    abi.encodeWithSelector(
593:                        MagnetarMarketModule
594:                            .exitPositionAndRemoveCollateral
595:                            .selector,
596:                        data.user,
597:                        data.externalData,
598:                        data.removeAndRepayData
599:                    )
600:                );
601:            } else if (_action.id == MARKET_DEPOSIT_REPAY_REMOVE_COLLATERAL) {
602:                HelperDepositRepayRemoveCollateral memory data = abi.decode(
603:                    _action.call[4:],
604:                    (HelperDepositRepayRemoveCollateral)
605:                );
606:
607:                _executeModule(
608:                    Module.Market,
609:                    abi.encodeWithSelector(
610:                        MagnetarMarketModule
611:                            .depositRepayAndRemoveCollateralFromMarket
612:                            .selector,
613:                        data.market,
614:                        data.user,
615:                        data.depositAmount,
616:                        data.repayAmount,
617:                        data.collateralAmount,
618:                        data.extractFromSender,
619:                        data.withdrawCollateralParams
620:                    )
621:                );
622:            } else if (_action.id == MARKET_BUY_COLLATERAL) {
623:                HelperBuyCollateral memory data = abi.decode(
624:                    _action.call[4:],
625:                    (HelperBuyCollateral)
626:                );
627:
628:                IMarket(data.market).buyCollateral(
629:                    data.from,
630:                    data.borrowAmount,
631:                    data.supplyAmount,
632:                    data.minAmountOut,
633:                    address(data.swapper),
634:                    data.dexData
635:                );
636:            } else if (_action.id == MARKET_SELL_COLLATERAL) {
637:                HelperSellCollateral memory data = abi.decode(
638:                    _action.call[4:],
639:                    (HelperSellCollateral)
640:                );
641:
642:                IMarket(data.market).sellCollateral(
643:                    data.from,
644:                    data.share,
645:                    data.minAmountOut,
646:                    address(data.swapper),
647:                    data.dexData
648:                );
649:            } else if (_action.id == TAP_EXERCISE_OPTION) {
650:                HelperExerciseOption memory data = abi.decode(
651:                    _action.call[4:],
652:                    (HelperExerciseOption)
653:                );
654:
655:                ITapiocaOptionsBrokerCrossChain(_action.target).exerciseOption(
656:                    data.optionsData,
657:                    data.lzData,
658:                    data.tapSendData,
659:                    data.approvals
660:                );
661:            } else if (_action.id == MARKET_MULTIHOP_BUY) {
662:                HelperMultiHopBuy memory data = abi.decode(
663:                    _action.call[4:],
664:                    (HelperMultiHopBuy)
665:                );
666:
667:                IUSDOBase(_action.target).initMultiHopBuy(
668:                    data.from,
669:                    data.collateralAmount,
670:                    data.borrowAmount,
671:                    data.swapData,
672:                    data.lzData,
673:                    data.externalData,
674:                    data.airdropAdapterParams,
675:                    data.approvals
676:                );
677:            } else if (_action.id == MARKET_MULTIHOP_BUY) {
678:                HelperMultiHopBuy memory data = abi.decode(
679:                    _action.call[4:],
680:                    (HelperMultiHopBuy)
681:                );
682:
683:                IUSDOBase(_action.target).initMultiHopBuy(
684:                    data.from,
685:                    data.collateralAmount,
686:                    data.borrowAmount,
687:                    data.swapData,
688:                    data.lzData,
689:                    data.externalData,
690:                    data.airdropAdapterParams,
691:                    data.approvals
692:                );
693:            } else if (_action.id == TOFT_REMOVE_AND_REPAY) {
694:                HelperTOFTRemoveAndRepayAsset memory data = abi.decode(
695:                    _action.call[4:],
696:                    (HelperTOFTRemoveAndRepayAsset)
697:                );
698:
699:                IUSDOBase(_action.target).removeAsset(
700:                    data.from,
701:                    data.to,
702:                    data.lzDstChainId,
703:                    data.zroPaymentAddress,
704:                    data.adapterParams,
705:                    data.externalData,
706:                    data.removeAndRepayData,
707:                    data.approvals
708:                );
709:            } else {
710:                revert("MagnetarV2: action not valid");
711:            }
712:        }
```
#### **Context**:-
[MagnetarV2.sol#L202-L712](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L202-L712)

## **L-20** | Possible reentrancy with callback on transfer tokens
### **Description** :-
hook

#### <ins>Proof Of Concept</ins>


```solidity
File:tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol
```

```solidity
241:        yieldBox.transfer(
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L241](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L241)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
448:        rewardToken.safeTransferFrom(msg.sender, address(this), _amount);
```
#### **Context**:-
[twTAP.sol#L448](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L448)

```solidity
565:        tapOFT.transfer(_to, releasedAmount);
```
#### **Context**:-
[twTAP.sol#L565](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L565)

```solidity
File:YieldBox/contracts/YieldBox.sol
```

```solidity
209:        wrappedNative.safeTransfer(address(asset.strategy), amount);
```
#### **Context**:-
[YieldBox.sol#L209](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L209)

## **L-21** | Functions calling contracts with transfer hooks are missing reentrancy guards
### **Description** :-
Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to read-only reentrancies with no way to protect against it, except by block-listing the whole protocol.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol
```

```solidity
49:        yieldBox.transfer(address(this), to, collateralId, share);
```
#### **Context**:-
[SGLLendingCommon.sol#L49](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L49)

```solidity
79:        yieldBox.transfer(address(this), to, assetId, share);
```
#### **Context**:-
[SGLLendingCommon.sol#L79](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L79)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol
```

```solidity
243:        yieldBox.transfer(address(this), to, assetId, share);
```
#### **Context**:-
[SGLCommon.sol#L243](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLCommon.sol#L243)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol
```

```solidity
166:        yieldBox.transfer(
```
#### **Context**:-
[SGLLiquidation.sol#L166](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLiquidation.sol#L166)

```solidity
193:        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
```
#### **Context**:-
[SGLLiquidation.sol#L193](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLiquidation.sol#L193)

```solidity
281:        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
```
#### **Context**:-
[SGLLiquidation.sol#L281](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLiquidation.sol#L281)

```solidity
282:        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
```
#### **Context**:-
[SGLLiquidation.sol#L282](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLiquidation.sol#L282)

```solidity
330:        yieldBox.transfer(
```
#### **Context**:-
[SGLLiquidation.sol#L330](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLiquidation.sol#L330)

```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```

```solidity
596:        yieldBox.transfer(
```
#### **Context**:-
[BigBang.sol#L596](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L596)

```solidity
648:        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
```
#### **Context**:-
[BigBang.sol#L648](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L648)

```solidity
649:        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
```
#### **Context**:-
[BigBang.sol#L649](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L649)

```solidity
717:        yieldBox.transfer(address(this), to, collateralId, share);
```
#### **Context**:-
[BigBang.sol#L717](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L717)

```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```

```solidity
359:        IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);
```
#### **Context**:-
[BaseTOFT.sol#L359](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFT.sol#L359)

```solidity
File:tap-token-audit/contracts/tokens/LTap.sol
```

```solidity
42:        tapToken.transferFrom(msg.sender, address(this), amount);
```
#### **Context**:-
[LTap.sol#L42](https://github.com/Tapioca-DAO/contracts/tokens/LTap.sol#L42)

```solidity
50:        tapToken.transfer(msg.sender, amount);
```
#### **Context**:-
[LTap.sol#L50](https://github.com/Tapioca-DAO/contracts/tokens/LTap.sol#L50)

```solidity
File:tap-token-audit/contracts/Vesting.sol
```

```solidity
119:        token.safeTransfer(msg.sender, _claimable);
```
#### **Context**:-
[Vesting.sol#L119](https://github.com/Tapioca-DAO/contracts/Vesting.sol#L119)

```solidity
File:tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol
```

```solidity
186:        yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L186](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L186)

```solidity
241:        yieldBox.transfer(
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L241](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L241)

```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```

```solidity
509:        _paymentToken.transferFrom(
```
#### **Context**:-
[AirdropBroker.sol#L509](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L509)

```solidity
514:        tapOFT.transfer(msg.sender, tapAmount);
```
#### **Context**:-
[AirdropBroker.sol#L514](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L514)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
260:        tapOFT.transferFrom(msg.sender, address(this), _amount);
```
#### **Context**:-
[twTAP.sol#L260](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L260)

```solidity
448:        rewardToken.safeTransferFrom(msg.sender, address(this), _amount);
```
#### **Context**:-
[twTAP.sol#L448](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L448)

```solidity
565:        tapOFT.transfer(_to, releasedAmount);
```
#### **Context**:-
[twTAP.sol#L565](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L565)

```solidity
File:tap-token-audit/contracts/options/TapiocaOptionBroker.sol
```

```solidity
230:        tOLP.transferFrom(msg.sender, address(this), _tOLPTokenID);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L230](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L230)

```solidity
342:        tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L342](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L342)

```solidity
530:        _paymentToken.transferFrom(
```
#### **Context**:-
[TapiocaOptionBroker.sol#L530](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L530)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol
```

```solidity
797:        IERC20(_token).safeTransferFrom(_from, address(this), _amount);
```
#### **Context**:-
[MagnetarMarketModule.sol#L797](https://github.com/Tapioca-DAO/contracts/Magnetar/modules/MagnetarMarketModule.sol#L797)

```solidity
File:tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol
```

```solidity
162:        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
```
#### **Context**:-
[BaseSwapper.sol#L162](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L162)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
147:        wrappedNative.safeTransfer(to, amount - 1); //rounding error
```
#### **Context**:-
[YearnStrategy.sol#L147](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L147)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
159:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[CompoundStrategy.sol#L159](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L159)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
164:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L164](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L164)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```

```solidity
239:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L239](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L239)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```

```solidity
246:        lpToken.safeTransfer(to, amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L246](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L246)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```

```solidity
266:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[StargateStrategy.sol#L266](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L266)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```

```solidity
273:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[AaveStrategy.sol#L273](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L273)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
212:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[BalancerStrategy.sol#L212](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L212)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```

```solidity
93:        gmx.safeTransfer(address(gmxWethPool), amount);
```
#### **Context**:-
[GlpStrategy.sol#L93](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L93)

```solidity
148:        IERC20(contractAddress).safeTransfer(to, amount);
```
#### **Context**:-
[GlpStrategy.sol#L148](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L148)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```

```solidity
344:        wrappedNative.safeTransfer(to, amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L344](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L344)

```solidity
File:YieldBox/contracts/YieldBox.sol
```

```solidity
181:        IERC721(asset.contractAddress).safeTransferFrom(from, address(asset.strategy), asset.tokenId);
```
#### **Context**:-
[YieldBox.sol#L181](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L181)

```solidity
209:        wrappedNative.safeTransfer(address(asset.strategy), amount);
```
#### **Context**:-
[YieldBox.sol#L209](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L209)

## **L-22** | Prefer safeTransferFrom() for ERC721 transfers
### **Description** :-
transferred using simple transferFrom. This doesn't check whether receiver is able to receive NFTs which can lead to lost tokens.

#### <ins>Proof Of Concept</ins>


```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
260:        tapOFT.transferFrom(msg.sender, address(this), _amount);
```
#### **Context**:-
[twTAP.sol#L260](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L260)
## **NC-23** | Burn functions should be protected with a modifier
### **Description** :-
Burn function lake a protected modifier Consider adding a modifier

#### <ins>Proof Of Concept</ins>


```solidity
File:tap-token-audit/contracts/options/oTAP.sol
```

```solidity
115:    function burn(uint256 _tokenId) external {
116:        require(
117:            _isApprovedOrOwner(msg.sender, _tokenId),
118:            "OTAP: only approved or owner"
119:        );
120:        _burn(_tokenId);
121:
122:        emit Burn(msg.sender, _tokenId, options[_tokenId]);
123:    }
```
#### **Context**:-
[oTAP.sol#L115-L123](https://github.com/Tapioca-DAO/contracts/options/oTAP.sol#L115-L123)

```solidity
File:tap-token-audit/contracts/option-airdrop/aoTAP.sol
```

```solidity
128:    function burn(uint256 _tokenId) external {
129:        require(
130:            _isApprovedOrOwner(msg.sender, _tokenId),
131:            "AOTAP: only approved or owner"
132:        );
133:        _burn(_tokenId);
134:
135:        emit Burn(msg.sender, _tokenId, options[_tokenId]);
136:    }
```
#### **Context**:-
[aoTAP.sol#L128-L136](https://github.com/Tapioca-DAO/contracts/option-airdrop/aoTAP.sol#L128-L136)

## **NC-24** | Unused State variables
### **Description** :-
Consider removing unused State variables or moving it to a higher level contract.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```

```solidity
30:    bool public paused;
```
#### **Context**:-
[BaseUSDOStorage.sol#L30](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L30)

```solidity
37:    uint256 internal constant FLASH_MINT_FEE_PRECISION = 1e6;
```
#### **Context**:-
[BaseUSDOStorage.sol#L37](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L37)

```solidity
38:    bytes32 internal constant FLASH_MINT_CALLBACK_SUCCESS =
39:        keccak256("ERC3156FlashBorrower.onFlashLoan");
```
#### **Context**:-
[BaseUSDOStorage.sol#L38-L39](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L38-L39)

```solidity
41:    uint16 internal constant PT_MARKET_MULTIHOP_BUY = 772;
```
#### **Context**:-
[BaseUSDOStorage.sol#L41](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L41)

```solidity
42:    uint16 internal constant PT_MARKET_REMOVE_ASSET = 773;
```
#### **Context**:-
[BaseUSDOStorage.sol#L42](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L42)

```solidity
43:    uint16 internal constant PT_YB_SEND_SGL_LEND_OR_REPAY = 774;
```
#### **Context**:-
[BaseUSDOStorage.sol#L43](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L43)

```solidity
44:    uint16 internal constant PT_LEVERAGE_MARKET_UP = 775;
```
#### **Context**:-
[BaseUSDOStorage.sol#L44](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L44)

```solidity
45:    uint16 internal constant PT_TAP_EXERCISE = 777;
```
#### **Context**:-
[BaseUSDOStorage.sol#L45](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L45)

```solidity
46:    uint16 internal constant PT_SEND_FROM = 778;
```
#### **Context**:-
[BaseUSDOStorage.sol#L46](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L46)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```

```solidity
42:    ISingularity.AccrueInfo public accrueInfo;
```
#### **Context**:-
[SGLStorage.sol#L42](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L42)

```solidity
46:    ILiquidationQueue public liquidationQueue;
```
#### **Context**:-
[SGLStorage.sol#L46](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L46)

```solidity
50:    bytes32 internal ASSET_SIG =
51:        0x0bd4060688a1800ae986e4840aebc924bb40b5bf44de4583df2257220b54b77c; // keccak256("asset")
```
#### **Context**:-
[SGLStorage.sol#L50-L51](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L50-L51)

```solidity
52:    bytes32 internal COLLATERAL_SIG =
53:        0x7d1dc38e60930664f8cbf495da6556ca091d2f92d6550877750c049864b18230; // keccak256("collateral")
```
#### **Context**:-
[SGLStorage.sol#L52-L53](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L52-L53)

```solidity
55:    uint256 public lqCollateralizationRate = 25000; // 25%
```
#### **Context**:-
[SGLStorage.sol#L55](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L55)

```solidity
57:    uint256 public minimumTargetUtilization;
```
#### **Context**:-
[SGLStorage.sol#L57](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L57)

```solidity
58:    uint256 public maximumTargetUtilization;
```
#### **Context**:-
[SGLStorage.sol#L58](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L58)

```solidity
59:    uint256 public fullUtilizationMinusMax;
```
#### **Context**:-
[SGLStorage.sol#L59](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L59)

```solidity
61:    uint64 public minimumInterestPerSecond;
```
#### **Context**:-
[SGLStorage.sol#L61](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L61)

```solidity
62:    uint64 public maximumInterestPerSecond;
```
#### **Context**:-
[SGLStorage.sol#L62](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L62)

```solidity
63:    uint256 public interestElasticity;
```
#### **Context**:-
[SGLStorage.sol#L63](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L63)

```solidity
64:    uint64 public startingInterestPerSecond;
```
#### **Context**:-
[SGLStorage.sol#L64](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L64)

```solidity
143:    uint256 internal constant FULL_UTILIZATION = 1e18;
```
#### **Context**:-
[SGLStorage.sol#L143](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L143)

```solidity
144:    uint256 internal constant UTILIZATION_PRECISION = 1e18;
```
#### **Context**:-
[SGLStorage.sol#L144](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L144)

```solidity
146:    uint256 internal constant FACTOR_PRECISION = 1e18;
```
#### **Context**:-
[SGLStorage.sol#L146](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L146)

```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```

```solidity
45:    bytes32 private _PERMIT_TYPEHASH_DEPRECATED_SLOT;
```
#### **Context**:-
[MarketERC20.sol#L45](https://github.com/Tapioca-DAO/contracts/markets/MarketERC20.sol#L45)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
23:    IPenrose public penrose;
```
#### **Context**:-
[Market.sol#L23](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L23)

```solidity
32:    uint256 public assetId;
```
#### **Context**:-
[Market.sol#L32](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L32)

```solidity
53:    uint256 public totalCollateralShare;
```
#### **Context**:-
[Market.sol#L53](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L53)

```solidity
84:    uint256 internal constant FEE_PRECISION_DECIMALS = 5;
```
#### **Context**:-
[Market.sol#L84](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L84)

```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol
```

```solidity
34:    uint16 internal constant PT_YB_SEND_STRAT = 770;
```
#### **Context**:-
[BaseTOFTStorage.sol#L34](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L34)

```solidity
35:    uint16 internal constant PT_YB_RETRIEVE_STRAT = 771;
```
#### **Context**:-
[BaseTOFTStorage.sol#L35](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L35)

```solidity
36:    uint16 internal constant PT_MARKET_REMOVE_COLLATERAL = 772;
```
#### **Context**:-
[BaseTOFTStorage.sol#L36](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L36)

```solidity
37:    uint16 internal constant PT_MARKET_MULTIHOP_SELL = 773;
```
#### **Context**:-
[BaseTOFTStorage.sol#L37](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L37)

```solidity
38:    uint16 internal constant PT_YB_SEND_SGL_BORROW = 775;
```
#### **Context**:-
[BaseTOFTStorage.sol#L38](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L38)

```solidity
39:    uint16 internal constant PT_LEVERAGE_MARKET_DOWN = 776;
```
#### **Context**:-
[BaseTOFTStorage.sol#L39](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L39)

```solidity
40:    uint16 internal constant PT_TAP_EXERCISE = 777;
```
#### **Context**:-
[BaseTOFTStorage.sol#L40](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L40)

```solidity
41:    uint16 internal constant PT_SEND_FROM = 778;
```
#### **Context**:-
[BaseTOFTStorage.sol#L41](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L41)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
112:    string private baseURI;
```
#### **Context**:-
[twTAP.sol#L112](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L112)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```

```solidity
298:    uint16 internal constant PERMIT_ALL = 1;
```
#### **Context**:-
[MagnetarV2Storage.sol#L298](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L298)

```solidity
301:    uint16 internal constant YB_DEPOSIT_ASSET = 100;
```
#### **Context**:-
[MagnetarV2Storage.sol#L301](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L301)

```solidity
302:    uint16 internal constant YB_WITHDRAW_TO = 102;
```
#### **Context**:-
[MagnetarV2Storage.sol#L302](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L302)

```solidity
304:    uint16 internal constant MARKET_ADD_COLLATERAL = 200;
```
#### **Context**:-
[MagnetarV2Storage.sol#L304](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L304)

```solidity
305:    uint16 internal constant MARKET_BORROW = 201;
```
#### **Context**:-
[MagnetarV2Storage.sol#L305](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L305)

```solidity
306:    uint16 internal constant MARKET_LEND = 203;
```
#### **Context**:-
[MagnetarV2Storage.sol#L306](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L306)

```solidity
307:    uint16 internal constant MARKET_REPAY = 204;
```
#### **Context**:-
[MagnetarV2Storage.sol#L307](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L307)

```solidity
308:    uint16 internal constant MARKET_YBDEPOSIT_AND_LEND = 205;
```
#### **Context**:-
[MagnetarV2Storage.sol#L308](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L308)

```solidity
309:    uint16 internal constant MARKET_YBDEPOSIT_COLLATERAL_AND_BORROW = 206;
```
#### **Context**:-
[MagnetarV2Storage.sol#L309](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L309)

```solidity
310:    uint16 internal constant MARKET_REMOVE_ASSET = 207;
```
#### **Context**:-
[MagnetarV2Storage.sol#L310](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L310)

```solidity
311:    uint16 internal constant MARKET_DEPOSIT_REPAY_REMOVE_COLLATERAL = 208;
```
#### **Context**:-
[MagnetarV2Storage.sol#L311](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L311)

```solidity
312:    uint16 internal constant MARKET_BUY_COLLATERAL = 209;
```
#### **Context**:-
[MagnetarV2Storage.sol#L312](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L312)

```solidity
313:    uint16 internal constant MARKET_SELL_COLLATERAL = 210;
```
#### **Context**:-
[MagnetarV2Storage.sol#L313](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L313)

```solidity
314:    uint16 internal constant MARKET_MULTIHOP_BUY = 211;
```
#### **Context**:-
[MagnetarV2Storage.sol#L314](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L314)

```solidity
315:    uint16 internal constant MARKET_MULTIHOP_SELL = 212;
```
#### **Context**:-
[MagnetarV2Storage.sol#L315](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L315)

```solidity
317:    uint16 internal constant TOFT_WRAP = 300;
```
#### **Context**:-
[MagnetarV2Storage.sol#L317](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L317)

```solidity
318:    uint16 internal constant TOFT_SEND_FROM = 301;
```
#### **Context**:-
[MagnetarV2Storage.sol#L318](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L318)

```solidity
319:    uint16 internal constant TOFT_SEND_APPROVAL = 302;
```
#### **Context**:-
[MagnetarV2Storage.sol#L319](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L319)

```solidity
320:    uint16 internal constant TOFT_SEND_AND_BORROW = 303;
```
#### **Context**:-
[MagnetarV2Storage.sol#L320](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L320)

```solidity
321:    uint16 internal constant TOFT_SEND_AND_LEND = 304;
```
#### **Context**:-
[MagnetarV2Storage.sol#L321](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L321)

```solidity
322:    uint16 internal constant TOFT_DEPOSIT_TO_STRATEGY = 305;
```
#### **Context**:-
[MagnetarV2Storage.sol#L322](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L322)

```solidity
323:    uint16 internal constant TOFT_RETRIEVE_FROM_STRATEGY = 306;
```
#### **Context**:-
[MagnetarV2Storage.sol#L323](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L323)

```solidity
324:    uint16 internal constant TOFT_REMOVE_AND_REPAY = 307;
```
#### **Context**:-
[MagnetarV2Storage.sol#L324](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L324)

```solidity
326:    uint16 internal constant TAP_EXERCISE_OPTION = 400;
```
#### **Context**:-
[MagnetarV2Storage.sol#L326](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L326)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
41:    address[] public rewardTokens;
```
#### **Context**:-
[BalancerStrategy.sol#L41](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L41)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```

```solidity
26:    string public constant override name = "sGLP";
```
#### **Context**:-
[GlpStrategy.sol#L26](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L26)

```solidity
27:    string public constant override description =
28:        "Holds staked GLP tokens and compounds the rewards";
```
#### **Context**:-
[GlpStrategy.sol#L27-L28](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L27-L28)

## **NC-25** | Event is not properly indexed
### **Description** :-
The emitted events are not indexed, making off-chain scripts such as front-ends of dApps difficult to filter the events efficiently.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```

```solidity
52:    event Minted(address indexed _for, uint256 _amount);
```
#### **Context**:-
[BaseUSDOStorage.sol#L52](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L52)

```solidity
54:    event Burned(address indexed _from, uint256 _amount);
```
#### **Context**:-
[BaseUSDOStorage.sol#L54](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L54)

```solidity
56:    event SetMinterStatus(address indexed _for, bool _status);
```
#### **Context**:-
[BaseUSDOStorage.sol#L56](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L56)

```solidity
58:    event SetBurnerStatus(address indexed _for, bool _status);
```
#### **Context**:-
[BaseUSDOStorage.sol#L58](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L58)

```solidity
62:    event PausedUpdated(bool oldState, bool newState);
```
#### **Context**:-
[BaseUSDOStorage.sol#L62](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L62)

```solidity
64:    event FlashMintFeeUpdated(uint256 _old, uint256 _new);
```
#### **Context**:-
[BaseUSDOStorage.sol#L64](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L64)

```solidity
66:    event MaxFlashMintUpdated(uint256 _old, uint256 _new);
```
#### **Context**:-
[BaseUSDOStorage.sol#L66](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L66)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```

```solidity
70:    event LogAccrue(
71:        uint256 accruedAmount,
72:        uint256 feeFraction,
73:        uint64 rate,
74:        uint256 utilization
75:    );
```
#### **Context**:-
[SGLStorage.sol#L70-L75](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L70-L75)

```solidity
77:    event LogAddCollateral(
78:        address indexed from,
79:        address indexed to,
80:        uint256 share
81:    );
```
#### **Context**:-
[SGLStorage.sol#L77-L81](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L77-L81)

```solidity
83:    event LogAddAsset(
84:        address indexed from,
85:        address indexed to,
86:        uint256 share,
87:        uint256 fraction
88:    );
```
#### **Context**:-
[SGLStorage.sol#L83-L88](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L83-L88)

```solidity
90:    event LogRemoveCollateral(
91:        address indexed from,
92:        address indexed to,
93:        uint256 share
94:    );
```
#### **Context**:-
[SGLStorage.sol#L90-L94](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L90-L94)

```solidity
96:    event LogRemoveAsset(
97:        address indexed from,
98:        address indexed to,
99:        uint256 share,
100:        uint256 fraction
101:    );
```
#### **Context**:-
[SGLStorage.sol#L96-L101](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L96-L101)

```solidity
103:    event LogBorrow(
104:        address indexed from,
105:        address indexed to,
106:        uint256 amount,
107:        uint256 feeAmount,
108:        uint256 part
109:    );
```
#### **Context**:-
[SGLStorage.sol#L103-L109](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L103-L109)

```solidity
111:    event LogRepay(
112:        address indexed from,
113:        address indexed to,
114:        uint256 amount,
115:        uint256 part
116:    );
```
#### **Context**:-
[SGLStorage.sol#L111-L116](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L111-L116)

```solidity
118:    event LogWithdrawFees(address indexed feeTo, uint256 feesEarnedFraction);
```
#### **Context**:-
[SGLStorage.sol#L118](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L118)

```solidity
120:    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);
```
#### **Context**:-
[SGLStorage.sol#L120](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L120)

```solidity
122:    event MinimumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L122](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L122)

```solidity
124:    event MaximumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L124](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L124)

```solidity
126:    event MinimumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L126](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L126)

```solidity
128:    event MaximumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L128](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L128)

```solidity
130:    event InterestElasticityUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L130](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L130)

```solidity
132:    event LqCollateralizationRateUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L132](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L132)

```solidity
134:    event OrderBookLiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L134](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L134)

```solidity
136:    event BidExecutionSwapperUpdated(address newAddress);
```
#### **Context**:-
[SGLStorage.sol#L136](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L136)

```solidity
138:    event UsdoSwapperUpdated(address newAddress);
```
#### **Context**:-
[SGLStorage.sol#L138](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L138)

```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```

```solidity
66:    event ApprovalBorrow(
67:        address indexed owner,
68:        address indexed spender,
69:        uint256 value
70:    );
```
#### **Context**:-
[MarketERC20.sol#L66-L70](https://github.com/Tapioca-DAO/contracts/markets/MarketERC20.sol#L66-L70)

```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```

```solidity
138:    event ProtocolWithdrawal(IMarket[] markets, uint256 timestamp);
```
#### **Context**:-
[Penrose.sol#L138](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L138)

```solidity
140:    event RegisterSingularityMasterContract(
141:        address location,
142:        IPenrose.ContractType risk
143:    );
```
#### **Context**:-
[Penrose.sol#L140-L143](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L140-L143)

```solidity
145:    event RegisterBigBangMasterContract(
146:        address location,
147:        IPenrose.ContractType risk
148:    );
```
#### **Context**:-
[Penrose.sol#L145-L148](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L145-L148)

```solidity
150:    event RegisterSingularity(address location, address masterContract);
```
#### **Context**:-
[Penrose.sol#L150](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L150)

```solidity
152:    event RegisterBigBang(address location, address masterContract);
```
#### **Context**:-
[Penrose.sol#L152](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L152)

```solidity
154:    event FeeToUpdate(address newFeeTo);
```
#### **Context**:-
[Penrose.sol#L154](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L154)

```solidity
156:    event SwapperUpdate(address swapper, bool isRegistered);
```
#### **Context**:-
[Penrose.sol#L156](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L156)

```solidity
158:    event UsdoTokenUpdated(address indexed usdoToken, uint256 assetId);
```
#### **Context**:-
[Penrose.sol#L158](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L158)

```solidity
162:    event PausedUpdated(bool oldState, bool newState);
```
#### **Context**:-
[Penrose.sol#L162](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L162)

```solidity
166:    event BigBangEthMarketDebtRate(uint256 _rate);
```
#### **Context**:-
[Penrose.sol#L166](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L166)

```solidity
168:    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);
```
#### **Context**:-
[Penrose.sol#L168](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L168)

```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```

```solidity
63:    event LogAccrue(uint256 accruedAmount, uint64 rate);
```
#### **Context**:-
[BigBang.sol#L63](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L63)

```solidity
65:    event LogAddCollateral(
66:        address indexed from,
67:        address indexed to,
68:        uint256 share
69:    );
```
#### **Context**:-
[BigBang.sol#L65-L69](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L65-L69)

```solidity
71:    event LogRemoveCollateral(
72:        address indexed from,
73:        address indexed to,
74:        uint256 share
75:    );
```
#### **Context**:-
[BigBang.sol#L71-L75](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L71-L75)

```solidity
77:    event LogBorrow(
78:        address indexed from,
79:        address indexed to,
80:        uint256 amount,
81:        uint256 feeAmount,
82:        uint256 part
83:    );
```
#### **Context**:-
[BigBang.sol#L77-L83](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L77-L83)

```solidity
85:    event LogRepay(
86:        address indexed from,
87:        address indexed to,
88:        uint256 amount,
89:        uint256 part
90:    );
```
#### **Context**:-
[BigBang.sol#L85-L90](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L85-L90)

```solidity
92:    event MinDebtRateUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[BigBang.sol#L92](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L92)

```solidity
94:    event MaxDebtRateUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[BigBang.sol#L94](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L94)

```solidity
96:    event DebtRateAgainstEthUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[BigBang.sol#L96](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L96)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
92:    event PausedUpdated(bool oldState, bool newState);
```
#### **Context**:-
[Market.sol#L92](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L92)

```solidity
94:    event LogExchangeRate(uint256 rate);
```
#### **Context**:-
[Market.sol#L94](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L94)

```solidity
96:    event LogBorrowCapUpdated(uint256 _oldVal, uint256 _newVal);
```
#### **Context**:-
[Market.sol#L96](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L96)

```solidity
102:    event Liquidated(
103:        address liquidator,
104:        address[] users,
105:        uint256 liquidatorReward,
106:        uint256 protocolReward,
107:        uint256 repayedAmount,
108:        uint256 collateralShareRemoved
109:    );
```
#### **Context**:-
[Market.sol#L102-L109](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L102-L109)

```solidity
111:    event LogBorrowingFee(uint256 _oldVal, uint256 _newVal);
```
#### **Context**:-
[Market.sol#L111](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L111)

```solidity
113:    event LiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[Market.sol#L113](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L113)

```solidity
File:tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol
```

```solidity
27:    event ConnectedChainStatusUpdated(uint256 _chain, bool _old, bool _new);
```
#### **Context**:-
[mTapiocaOFT.sol#L27](https://github.com/Tapioca-DAO/contracts/tOFT/mTapiocaOFT.sol#L27)

```solidity
29:    event BalancerStatusUpdated(
30:        address indexed _balancer,
31:        bool _bool,
32:        bool _new
33:    );
```
#### **Context**:-
[mTapiocaOFT.sol#L29-L33](https://github.com/Tapioca-DAO/contracts/tOFT/mTapiocaOFT.sol#L29-L33)

```solidity
35:    event Rebalancing(
36:        address indexed _balancer,
37:        uint256 _amount,
38:        bool _isNative
39:    );
```
#### **Context**:-
[mTapiocaOFT.sol#L35-L39](https://github.com/Tapioca-DAO/contracts/tOFT/mTapiocaOFT.sol#L35-L39)

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```

```solidity
51:    event SetFees(uint256 _newFee);
```
#### **Context**:-
[TapiocaWrapper.sol#L51](https://github.com/Tapioca-DAO/contracts/TapiocaWrapper.sol#L51)

```solidity
File:tapiocaz-audit/contracts/Balancer.sol
```

```solidity
69:    event ConnectedChainUpdated(
70:        address indexed _srcOft,
71:        uint16 _dstChainId,
72:        address indexed _dstOft
73:    );
```
#### **Context**:-
[Balancer.sol#L69-L73](https://github.com/Tapioca-DAO/contracts/Balancer.sol#L69-L73)

```solidity
76:    event Rebalanced(
77:        address indexed _srcOft,
78:        uint16 _dstChainId,
79:        uint256 _slippage,
80:        uint256 _amount,
81:        bool _isNative
82:    );
```
#### **Context**:-
[Balancer.sol#L76-L82](https://github.com/Tapioca-DAO/contracts/Balancer.sol#L76-L82)

```solidity
84:    event RebalanceAmountUpdated(
85:        address _srcOft,
86:        uint16 _dstChainId,
87:        uint256 _amount,
88:        uint256 _totalAmount
89:    );
```
#### **Context**:-
[Balancer.sol#L84-L89](https://github.com/Tapioca-DAO/contracts/Balancer.sol#L84-L89)

```solidity
File:tap-token-audit/contracts/options/oTAP.sol
```

```solidity
47:    event Mint(address indexed to, uint256 indexed tokenId, TapOption option);
```
#### **Context**:-
[oTAP.sol#L47](https://github.com/Tapioca-DAO/contracts/options/oTAP.sol#L47)

```solidity
48:    event Burn(address indexed from, uint256 indexed tokenId, TapOption option);
```
#### **Context**:-
[oTAP.sol#L48](https://github.com/Tapioca-DAO/contracts/options/oTAP.sol#L48)

```solidity
File:tap-token-audit/contracts/option-airdrop/aoTAP.sol
```

```solidity
53:    event Mint(
54:        address indexed to,
55:        uint256 indexed tokenId,
56:        AirdropTapOption option
57:    );
```
#### **Context**:-
[aoTAP.sol#L53-L57](https://github.com/Tapioca-DAO/contracts/option-airdrop/aoTAP.sol#L53-L57)

```solidity
58:    event Burn(
59:        address indexed from,
60:        uint256 indexed tokenId,
61:        AirdropTapOption option
62:    );
```
#### **Context**:-
[aoTAP.sol#L58-L62](https://github.com/Tapioca-DAO/contracts/option-airdrop/aoTAP.sol#L58-L62)

```solidity
File:tap-token-audit/contracts/Vesting.sol
```

```solidity
60:    event UserRegistered(address indexed user, uint256 amount);
```
#### **Context**:-
[Vesting.sol#L60](https://github.com/Tapioca-DAO/contracts/Vesting.sol#L60)

```solidity
62:    event Claimed(address indexed user, uint256 amount);
```
#### **Context**:-
[Vesting.sol#L62](https://github.com/Tapioca-DAO/contracts/Vesting.sol#L62)

```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```

```solidity
78:    event Emitted(uint256 week, uint256 amount);
```
#### **Context**:-
[TapOFT.sol#L78](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L78)

```solidity
80:    event Minted(address indexed _by, address indexed _to, uint256 _amount);
```
#### **Context**:-
[TapOFT.sol#L80](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L80)

```solidity
82:    event Burned(address indexed _from, uint256 _amount);
```
#### **Context**:-
[TapOFT.sol#L82](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L82)

```solidity
84:    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
```
#### **Context**:-
[TapOFT.sol#L84](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L84)

```solidity
86:    event PausedUpdated(bool oldState, bool newState);
```
#### **Context**:-
[TapOFT.sol#L86](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L86)

```solidity
File:tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol
```

```solidity
81:    event Mint(
82:        address indexed to,
83:        uint128 indexed sglAssetID,
84:        LockPosition lockPosition
85:    );
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L81-L85](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L81-L85)

```solidity
86:    event Burn(
87:        address indexed to,
88:        uint128 indexed sglAssetID,
89:        LockPosition lockPosition
90:    );
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L86-L90](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L86-L90)

```solidity
91:    event UpdateTotalSingularityPoolWeights(
92:        uint256 totalSingularityPoolWeights
93:    );
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L91-L93](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L91-L93)

```solidity
94:    event SetSGLPoolWeight(address sgl, uint256 poolWeight);
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L94](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L94)

```solidity
95:    event RegisterSingularity(address sgl, uint256 assetID);
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L95](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L95)

```solidity
96:    event UnregisterSingularity(address sgl, uint256 assetID);
```
#### **Context**:-
[TapiocaOptionLiquidityProvision.sol#L96](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionLiquidityProvision.sol#L96)

```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```

```solidity
115:    event Participate(uint256 indexed epoch, uint256 aoTAPTokenID);
```
#### **Context**:-
[AirdropBroker.sol#L115](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L115)

```solidity
116:    event ExerciseOption(
117:        uint256 indexed epoch,
118:        address indexed to,
119:        ERC20 indexed paymentToken,
120:        uint256 aoTapTokenID,
121:        uint256 amount
122:    );
```
#### **Context**:-
[AirdropBroker.sol#L116-L122](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L116-L122)

```solidity
123:    event NewEpoch(uint256 indexed epoch, uint256 epochTAPValuation);
```
#### **Context**:-
[AirdropBroker.sol#L123](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L123)

```solidity
125:    event SetPaymentToken(ERC20 paymentToken, IOracle oracle, bytes oracleData);
```
#### **Context**:-
[AirdropBroker.sol#L125](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L125)

```solidity
126:    event SetTapOracle(IOracle oracle, bytes oracleData);
```
#### **Context**:-
[AirdropBroker.sol#L126](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L126)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
134:    event Participate(
135:        address indexed participant,
136:        uint256 tapAmount,
137:        uint256 multiplier
138:    );
```
#### **Context**:-
[twTAP.sol#L134-L138](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L134-L138)

```solidity
139:    event AMLDivergence(
140:        uint256 cumulative,
141:        uint256 averageMagnitude,
142:        uint256 totalParticipants
143:    );
```
#### **Context**:-
[twTAP.sol#L139-L143](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L139-L143)

```solidity
144:    event ExitPosition(uint256 tokenId, uint256 amount);
```
#### **Context**:-
[twTAP.sol#L144](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L144)

```solidity
File:tap-token-audit/contracts/options/TapiocaOptionBroker.sol
```

```solidity
100:    event Participate(
101:        uint256 indexed epoch,
102:        uint256 indexed sglAssetID,
103:        uint256 totalDeposited,
104:        LockPosition lock,
105:        uint256 discount
106:    );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L100-L106](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L100-L106)

```solidity
107:    event AMLDivergence(
108:        uint256 indexed epoch,
109:        uint256 cumulative,
110:        uint256 averageMagnitude,
111:        uint256 totalParticipants
112:    );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L107-L112](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L107-L112)

```solidity
113:    event ExerciseOption(
114:        uint256 indexed epoch,
115:        address indexed to,
116:        ERC20 indexed paymentToken,
117:        uint256 oTapTokenID,
118:        uint256 amount
119:    );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L113-L119](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L113-L119)

```solidity
120:    event NewEpoch(
121:        uint256 indexed epoch,
122:        uint256 extractedTAP,
123:        uint256 epochTAPValuation
124:    );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L120-L124](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L120-L124)

```solidity
125:    event ExitPosition(
126:        uint256 indexed epoch,
127:        uint256 indexed tokenId,
128:        uint256 amount
129:    );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L125-L129](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L125-L129)

```solidity
130:    event SetPaymentToken(ERC20 paymentToken, IOracle oracle, bytes oracleData);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L130](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L130)

```solidity
131:    event SetTapOracle(IOracle oracle, bytes oracleData);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L131](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L131)

```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```

```solidity
41:    event CallFailedStr(uint16 _srcChainId, bytes _payload, string _reason);
```
#### **Context**:-
[BaseTapOFT.sol#L41](https://github.com/Tapioca-DAO/contracts/tokens/BaseTapOFT.sol#L41)

```solidity
42:    event CallFailedBytes(uint16 _srcChainId, bytes _payload, bytes _reason);
```
#### **Context**:-
[BaseTapOFT.sol#L42](https://github.com/Tapioca-DAO/contracts/tokens/BaseTapOFT.sol#L42)

```solidity
File:tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol
```

```solidity
43:    event PoolFee(uint256 _old, uint256 _new);
```
#### **Context**:-
[UniswapV3Swapper.sol#L43](https://github.com/Tapioca-DAO/contracts/Swapper/UniswapV3Swapper.sol#L43)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```

```solidity
331:    event ApprovalForAll(address owner, address operator, bool approved);
```
#### **Context**:-
[MagnetarV2Storage.sol#L331](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L331)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
46:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[YearnStrategy.sol#L46](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L46)

```solidity
47:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[YearnStrategy.sol#L47](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L47)

```solidity
48:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[YearnStrategy.sol#L48](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L48)

```solidity
49:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[YearnStrategy.sol#L49](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L49)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
45:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[CompoundStrategy.sol#L45](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L45)

```solidity
46:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[CompoundStrategy.sol#L46](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L46)

```solidity
47:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[CompoundStrategy.sol#L47](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L47)

```solidity
48:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[CompoundStrategy.sol#L48](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L48)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
47:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[LidoEthStrategy.sol#L47](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L47)

```solidity
48:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L48](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L48)

```solidity
49:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L49](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L49)

```solidity
50:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L50](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L50)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```

```solidity
56:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L56](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L56)

```solidity
58:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L58](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L58)

```solidity
59:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L59](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L59)

```solidity
60:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L60](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L60)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```

```solidity
57:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L57](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L57)

```solidity
59:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L59](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L59)

```solidity
60:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L60](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L60)

```solidity
61:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L61](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L61)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```

```solidity
62:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[StargateStrategy.sol#L62](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L62)

```solidity
63:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[StargateStrategy.sol#L63](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L63)

```solidity
64:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[StargateStrategy.sol#L64](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L64)

```solidity
65:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[StargateStrategy.sol#L65](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L65)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```

```solidity
53:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[AaveStrategy.sol#L53](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L53)

```solidity
54:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[AaveStrategy.sol#L54](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L54)

```solidity
55:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[AaveStrategy.sol#L55](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L55)

```solidity
56:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[AaveStrategy.sol#L56](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L56)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
52:    event RewardTokens(uint256 _count);
```
#### **Context**:-
[BalancerStrategy.sol#L52](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L52)

```solidity
53:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[BalancerStrategy.sol#L53](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L53)

```solidity
54:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[BalancerStrategy.sol#L54](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L54)

```solidity
55:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[BalancerStrategy.sol#L55](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L55)

```solidity
56:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[BalancerStrategy.sol#L56](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L56)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```

```solidity
78:    event DepositThreshold(uint256 _old, uint256 _new);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L78](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L78)

```solidity
80:    event AmountQueued(uint256 amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L80](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L80)

```solidity
81:    event AmountDeposited(uint256 amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L81](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L81)

```solidity
82:    event AmountWithdrawn(address indexed to, uint256 amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L82](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L82)

```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```

```solidity
28:    event TokenCreated(address indexed creator, string name, string symbol, uint8 decimals, uint256 tokenId);
```
#### **Context**:-
[NativeTokenFactory.sol#L28](https://github.com/Tapioca-DAO/contracts/NativeTokenFactory.sol#L28)

```solidity
File:YieldBox/contracts/YieldBox.sol
```

```solidity
62:    event Deposited(
63:        address indexed sender,
64:        address indexed from,
65:        address indexed to,
66:        uint256 assetId,
67:        uint256 amountIn,
68:        uint256 shareIn,
69:        uint256 amountOut,
70:        uint256 shareOut,
71:        bool isNFT
72:    );
```
#### **Context**:-
[YieldBox.sol#L62-L72](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L62-L72)

```solidity
74:    event Withdraw(
75:        address indexed sender,
76:        address indexed from,
77:        address indexed to,
78:        uint256 assetId,
79:        uint256 amountIn,
80:        uint256 shareIn,
81:        uint256 amountOut,
82:        uint256 shareOut
83:    );
```
#### **Context**:-
[YieldBox.sol#L74-L83](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L74-L83)

## **NC-26** | Unused Error
### **Description** :-
Consider removing unused error or moving it to a higher level contract.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```

```solidity
61:    error TapiocaWrapper__MngmtFeeTooHigh();
```
#### **Context**:-
[TapiocaWrapper.sol#L61](https://github.com/Tapioca-DAO/contracts/TapiocaWrapper.sol#L61)

## **NC-27** | public functions not called by the contract should be declared external instead
### **Description** :-
The following functions could be set external to save gas and improve code quality.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/markets/MarketERC20.sol
```

```solidity
114:    function totalSupply() public view virtual override returns (uint256) {}
```
#### **Context**:-
[MarketERC20.sol#L114](https://github.com/Tapioca-DAO/contracts/markets/MarketERC20.sol#L114)

```solidity
159:    function transferFrom(
160:        address from,
161:        address to,
162:        uint256 amount
163:    ) public virtual returns (bool) {
164:        // If `amount` is 0, or `from` is `to` nothing happens
165:        if (amount != 0) {
166:            uint256 srcBalance = balanceOf[from];
167:            require(srcBalance >= amount, "ERC20: balance too low");
168:
169:            if (from != to) {
170:                uint256 spenderAllowance = allowance[from][msg.sender];
171:                // If allowance is infinite, don't decrease it to save on gas (breaks with EIP-20).
172:                if (spenderAllowance != type(uint256).max) {
173:                    require(
174:                        spenderAllowance >= amount,
175:                        "ERC20: allowance too low"
176:                    );
177:                    allowance[from][msg.sender] = spenderAllowance - amount; // Underflow is checked
178:                }
179:                require(to != address(0), "ERC20: no zero address"); // Moved down so other failed calls safe some gas
180:
181:                balanceOf[from] = srcBalance - amount; // Underflow is checked
182:                balanceOf[to] += amount;
183:            }
184:        }
185:        emit Transfer(from, to, amount);
186:        return true;
187:    }
```
#### **Context**:-
[MarketERC20.sol#L159-L187](https://github.com/Tapioca-DAO/contracts/markets/MarketERC20.sol#L159-L187)

```solidity
File:tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol
```

```solidity
103:    function sendFromDestination(bytes memory _payload) public {
104:        (
105:            ,
106:            address from,
107:            uint256 amount,
108:            ISendFrom.LzCallParams memory callParams,
109:            uint16 lzDstChainId,
110:            ICommonData.IApproval[] memory approvals
111:        ) = abi.decode(
112:                _payload,
113:                (
114:                    uint16,
115:                    address,
116:                    uint256,
117:                    ISendFrom.LzCallParams,
118:                    uint16,
119:                    ICommonData.IApproval[]
120:                )
121:            );
122:
123:        if (approvals.length > 0) {
124:            _callApproval(approvals);
125:        }
126:
127:        ISendFrom(address(this)).sendFrom{value: address(this).balance}(
128:            from,
129:            lzDstChainId,
130:            LzLib.addressToBytes32(from),
131:            amount,
132:            callParams
133:        );
134:
135:        emit ReceiveFromChain(lzDstChainId, from, 0);
136:    }
```
#### **Context**:-
[USDOOptionsModule.sol#L103-L136](https://github.com/Tapioca-DAO/contracts/usd0/modules/USDOOptionsModule.sol#L103-L136)

```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```

```solidity
199:    function singularityMarkets()
200:        public
201:        view
202:        returns (address[] memory markets)
203:    {
204:        markets = _getMasterContractLength(singularityMasterContracts);
205:    }
```
#### **Context**:-
[Penrose.sol#L199-L205](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L199-L205)

```solidity
209:    function bigBangMarkets() public view returns (address[] memory markets) {
210:        markets = _getMasterContractLength(bigbangMasterContracts);
211:    }
```
#### **Context**:-
[Penrose.sol#L209-L211](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L209-L211)

```solidity
214:    function singularityMasterContractLength() public view returns (uint256) {
215:        return singularityMasterContracts.length;
216:    }
```
#### **Context**:-
[Penrose.sol#L214-L216](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L214-L216)

```solidity
219:    function bigBangMasterContractLength() public view returns (uint256) {
220:        return bigbangMasterContracts.length;
221:    }
```
#### **Context**:-
[Penrose.sol#L219-L221](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L219-L221)

```solidity
232:    function withdrawAllMarketFees(
233:        IMarket[] calldata markets_,
234:        ISwapper[] calldata swappers_,
235:        IPenrose.SwapData[] calldata swapData_
236:    ) public notPaused {
237:        require(
238:            markets_.length == swappers_.length &&
239:                swappers_.length == swapData_.length,
240:            "Penrose: length mismatch"
241:        );
242:        require(address(swappers_[0]) != address(0), "Penrose: zero address");
243:        require(address(markets_[0]) != address(0), "Penrose: zero address");
244:
245:        _withdrawAllProtocolFees(swappers_, swapData_, markets_);
246:
247:        emit ProtocolWithdrawal(markets_, block.timestamp);
248:    }
```
#### **Context**:-
[Penrose.sol#L232-L248](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L232-L248)

```solidity
File:tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol
```

```solidity
431:    function transferFrom(
432:        address from,
433:        address to,
434:        uint256 amount
435:    ) public override returns (bool) {}
```
#### **Context**:-
[BigBang.sol#L431-L435](https://github.com/Tapioca-DAO/contracts/markets/bigBang/BigBang.sol#L431-L435)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
256:    function computeClosingFactor(
257:        uint256 borrowPart,
258:        uint256 collateralPartInAsset,
259:        uint256 borrowPartDecimals,
260:        uint256 collateralPartDecimals,
261:        uint256 ratesPrecision
262:    ) public view returns (uint256) {
263:        uint256 borrowPartScaled = borrowPart;
264:        if (borrowPartDecimals > 18) {
265:            borrowPartScaled = borrowPart / (10 ** (borrowPartDecimals - 18));
266:        }
267:        if (borrowPartDecimals < 18) {
268:            borrowPartScaled = borrowPart * (10 ** (18 - borrowPartDecimals));
269:        }
270:
271:        uint256 collateralPartInAssetScaled = collateralPartInAsset;
272:        if (collateralPartDecimals > 18) {
273:            collateralPartInAssetScaled =
274:                collateralPartInAsset /
275:                (10 ** (collateralPartDecimals - 18));
276:        }
277:        if (collateralPartDecimals < 18) {
278:            collateralPartInAssetScaled =
279:                collateralPartInAsset *
280:                (10 ** (18 - collateralPartDecimals));
281:        }
282:
283:        uint256 liquidationStartsAt = (collateralPartInAssetScaled *
284:            collateralizationRate) / (10 ** ratesPrecision);
285:        if (borrowPartScaled < liquidationStartsAt) return 0;
286:
287:        uint256 numerator = borrowPartScaled -
288:            ((collateralizationRate * collateralPartInAssetScaled) /
289:                (10 ** ratesPrecision));
290:        uint256 denominator = ((10 ** ratesPrecision) -
291:            (collateralizationRate *
292:                ((10 ** ratesPrecision) + liquidationMultiplier)) /
293:            (10 ** ratesPrecision)) * (10 ** (18 - ratesPrecision));
294:
295:        uint256 x = (numerator * 1e18) / denominator;
296:        return x;
297:    }
```
#### **Context**:-
[Market.sol#L256-L297](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L256-L297)

```solidity
305:    function computeTVLInfo(
306:        address user,
307:        uint256 _exchangeRate
308:    )
309:        public
310:        view
311:        returns (uint256 amountToSolvency, uint256 minTVL, uint256 maxTVL)
312:    {
313:        uint256 borrowPart = userBorrowPart[user];
314:        if (borrowPart == 0) return (0, 0, 0);
315:
316:        Rebase memory _totalBorrow = totalBorrow;
317:
318:        uint256 collateralAmountInAsset = _computeMaxBorrowableAmount(
319:            user,
320:            _exchangeRate
321:        );
322:
323:        borrowPart = (borrowPart * _totalBorrow.elastic) / _totalBorrow.base;
324:
325:        amountToSolvency = borrowPart >= collateralAmountInAsset
326:            ? borrowPart - collateralAmountInAsset
327:            : 0;
328:
329:        (minTVL, maxTVL) = _computeMaxAndMinLTVInAsset(
330:            userCollateralShare[user],
331:            _exchangeRate
332:        );
333:    }
```
#### **Context**:-
[Market.sol#L305-L333](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L305-L333)

```solidity
356:    function computeLiquidatorReward(
357:        address user,
358:        uint256 _exchangeRate
359:    ) public view returns (uint256) {
360:        (uint256 minTVL, uint256 maxTVL) = _computeMaxAndMinLTVInAsset(
361:            userCollateralShare[user],
362:            _exchangeRate
363:        );
364:        return _getCallerReward(userBorrowPart[user], minTVL, maxTVL);
365:    }
```
#### **Context**:-
[Market.sol#L356-L365](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L356-L365)

```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol
```

```solidity
122:    function strategyDeposit(
123:        address module,
124:        uint16 _srcChainId,
125:        bytes memory _srcAddress,
126:        uint64 _nonce,
127:        bytes memory _payload,
128:        IERC20 _erc20
129:    ) public {
130:        (
131:            ,
132:            ,
133:            bytes32 from,
134:            uint256 amount,
135:            uint256 share,
136:            uint256 assetId,
137:
138:        ) = abi.decode(
139:                _payload,
140:                (uint16, bytes32, bytes32, uint256, uint256, uint256, address)
141:            );
142:
143:        uint256 balanceBefore = balanceOf(address(this));
144:        bool credited = creditedPackets[_srcChainId][_srcAddress][_nonce];
145:        if (!credited) {
146:            _creditTo(_srcChainId, address(this), amount);
147:            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
148:        }
149:        uint256 balanceAfter = balanceOf(address(this));
150:
151:        address onBehalfOf = LzLib.bytes32ToAddress(from);
152:        (bool success, bytes memory reason) = module.delegatecall(
153:            abi.encodeWithSelector(
154:                this.depositToYieldbox.selector,
155:                assetId,
156:                amount,
157:                share,
158:                _erc20,
159:                address(this),
160:                onBehalfOf
161:            )
162:        );
163:        if (!success) {
164:            if (balanceAfter - balanceBefore >= amount) {
165:                IERC20(address(this)).safeTransfer(onBehalfOf, amount);
166:            }
167:            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
168:        }
169:
170:        emit ReceiveFromChain(_srcChainId, onBehalfOf, amount);
171:    }
```
#### **Context**:-
[BaseTOFTStrategyModule.sol#L122-L171](https://github.com/Tapioca-DAO/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L122-L171)

```solidity
188:    function strategyWithdraw(
189:        uint16 _srcChainId,
190:        bytes memory _payload
191:    ) public {
192:        (
193:            ,
194:            bytes32 from,
195:            ,
196:            uint256 _amount,
197:            uint256 _share,
198:            uint256 _assetId,
199:            address _zroPaymentAddress
200:        ) = abi.decode(
201:                _payload,
202:                (uint16, bytes32, bytes32, uint256, uint256, uint256, address)
203:            );
204:
205:        address _from = LzLib.bytes32ToAddress(from);
206:        _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));
207:
208:        _debitFrom(
209:            address(this),
210:            lzEndpoint.getChainId(),
211:            LzLib.addressToBytes32(address(this)),
212:            _amount
213:        );
214:
215:        bytes memory lzSendBackPayload = _encodeSendPayload(
216:            from,
217:            _ld2sd(_amount)
218:        );
219:        _lzSend(
220:            _srcChainId,
221:            lzSendBackPayload,
222:            payable(this),
223:            _zroPaymentAddress,
224:            "",
225:            address(this).balance
226:        );
227:        emit SendToChain(
228:            _srcChainId,
229:            _from,
230:            LzLib.addressToBytes32(address(this)),
231:            _amount
232:        );
233:
234:        emit ReceiveFromChain(_srcChainId, _from, _amount);
235:    }
```
#### **Context**:-
[BaseTOFTStrategyModule.sol#L188-L235](https://github.com/Tapioca-DAO/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L188-L235)

```solidity
File:tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol
```

```solidity
118:    function sendFromDestination(bytes memory _payload) public {
119:        (
120:            ,
121:            address from,
122:            uint256 amount,
123:            ISendFrom.LzCallParams memory callParams,
124:            uint16 lzDstChainId,
125:            ICommonData.IApproval[] memory approvals
126:        ) = abi.decode(
127:                _payload,
128:                (
129:                    uint16,
130:                    address,
131:                    uint256,
132:                    ISendFrom.LzCallParams,
133:                    uint16,
134:                    ICommonData.IApproval[]
135:                )
136:            );
137:
138:        if (approvals.length > 0) {
139:            _callApproval(approvals);
140:        }
141:
142:        ISendFrom(address(this)).sendFrom{value: address(this).balance}(
143:            from,
144:            lzDstChainId,
145:            LzLib.addressToBytes32(from),
146:            amount,
147:            callParams
148:        );
149:
150:        emit ReceiveFromChain(lzDstChainId, from, 0);
151:    }
```
#### **Context**:-
[BaseTOFTOptionsModule.sol#L118-L151](https://github.com/Tapioca-DAO/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L118-L151)

```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```

```solidity
84:    function decimals() public view override returns (uint8) {
85:        if (_decimalCache == 0) return 18; //temporary fix for LZ _sharedDecimals check
86:        return _decimalCache;
87:    }
```
#### **Context**:-
[BaseTOFT.sol#L84-L87](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFT.sol#L84-L87)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
155:    function getParticipation(
156:        uint _tokenId
157:    ) public view returns (Participation memory participant) {
158:        participant = participants[_tokenId];
159:        if (participant.expiry < block.timestamp) {
160:            participant.multiplier = 0;
161:        }
162:        return participant;
163:    }
```
#### **Context**:-
[twTAP.sol#L155-L163](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L155-L163)

```solidity
File:tapioca-periph-audit/contracts/oracle/implementations/GLPOracle.sol
```

```solidity
47:    function name(bytes calldata) public pure override returns (string memory) {
48:        return "GLP/USD";
49:    }
```
#### **Context**:-
[GLPOracle.sol#L47-L49](https://github.com/Tapioca-DAO/contracts/oracle/implementations/GLPOracle.sol#L47-L49)

```solidity
52:    function symbol(
53:        bytes calldata
54:    ) public pure override returns (string memory) {
55:        return "GLP/USD";
56:    }
```
#### **Context**:-
[GLPOracle.sol#L52-L56](https://github.com/Tapioca-DAO/contracts/oracle/implementations/GLPOracle.sol#L52-L56)

```solidity
File:tapioca-periph-audit/contracts/Multicall/Multicall3.sol
```

```solidity
63:    function multicallValue(
64:        CallValue[] calldata calls
65:    ) public payable returns (Result[] memory returnData) {
66:        uint256 valAccumulator;
67:        uint256 length = calls.length;
68:        returnData = new Result[](length);
69:        CallValue memory calli;
70:        for (uint256 i = 0; i < length; ) {
71:            Result memory result = returnData[i];
72:            calli = calls[i];
73:            uint256 val = calli.value;
74:            // Humanity will be a Type V Kardashev Civilization before this overflows - andreas
75:            // ~ 10^25 Wei in existence << ~ 10^76 size uint fits in a uint256
76:            unchecked {
77:                valAccumulator += val;
78:            }
79:            (result.success, result.returnData) = calli.target.call{value: val}(
80:                calli.callData
81:            );
82:            if (!result.success) {
83:                _getRevertMsg(result.returnData);
84:            }
85:            unchecked {
86:                ++i;
87:            }
88:        }
89:        // Finally, make sure the msg.value = SUM(call[0...i].value)
90:        require(msg.value == valAccumulator, "Multicall3: value mismatch");
91:    }
```
#### **Context**:-
[Multicall3.sol#L63-L91](https://github.com/Tapioca-DAO/contracts/Multicall/Multicall3.sol#L63-L91)

```solidity
File:tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol
```

```solidity
47:    function getDefaultDexOptions()
48:        public
49:        pure
50:        override
51:        returns (bytes memory)
52:    {
53:        revert Undefined();
54:    }
```
#### **Context**:-
[CurveSwapper.sol#L47-L54](https://github.com/Tapioca-DAO/contracts/Swapper/CurveSwapper.sol#L47-L54)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol
```

```solidity
76:    function getCollateralAmountForShare(
77:        IMarket market,
78:        uint256 share
79:    ) public view returns (uint256 amount) {
80:        IYieldBoxBase yieldBox = IYieldBoxBase(market.yieldBox());
81:        return yieldBox.toAmount(market.collateralId(), share, false);
82:    }
```
#### **Context**:-
[MagnetarV2.sol#L76-L82](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L76-L82)

```solidity
89:    function getCollateralSharesForBorrowPart(
90:        IMarket market,
91:        uint256 borrowPart,
92:        uint256 liquidationMultiplierPrecision,
93:        uint256 exchangeRatePrecision
94:    ) public view returns (uint256 collateralShares) {
95:        Rebase memory _totalBorrowed;
96:        (uint128 totalBorrowElastic, uint128 totalBorrowBase) = market
97:            .totalBorrow();
98:        _totalBorrowed = Rebase(totalBorrowElastic, totalBorrowBase);
99:
100:        IYieldBoxBase yieldBox = IYieldBoxBase(market.yieldBox());
101:        uint256 borrowAmount = _totalBorrowed.toElastic(borrowPart, false);
102:        return
103:            yieldBox.toShare(
104:                market.collateralId(),
105:                (borrowAmount *
106:                    market.liquidationMultiplier() *
107:                    market.exchangeRate()) /
108:                    (liquidationMultiplierPrecision * exchangeRatePrecision),
109:                false
110:            );
111:    }
```
#### **Context**:-
[MagnetarV2.sol#L89-L111](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L89-L111)

```solidity
117:    function getAmountForBorrowPart(
118:        IMarket market,
119:        uint256 borrowPart
120:    ) public view returns (uint256 amount) {
121:        Rebase memory _totalBorrowed;
122:        (uint128 totalBorrowElastic, uint128 totalBorrowBase) = market
123:            .totalBorrow();
124:        _totalBorrowed = Rebase(totalBorrowElastic, totalBorrowBase);
125:
126:        return _totalBorrowed.toElastic(borrowPart, false);
127:    }
```
#### **Context**:-
[MagnetarV2.sol#L117-L127](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L117-L127)

```solidity
133:    function getBorrowPartForAmount(
134:        IMarket market,
135:        uint256 amount
136:    ) public view returns (uint256 part) {
137:        Rebase memory _totalBorrowed;
138:        (uint128 totalBorrowElastic, uint128 totalBorrowBase) = market
139:            .totalBorrow();
140:        _totalBorrowed = Rebase(totalBorrowElastic, totalBorrowBase);
141:
142:        return _totalBorrowed.toBase(amount, false);
143:    }
```
#### **Context**:-
[MagnetarV2.sol#L133-L143](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L133-L143)

```solidity
150:    function getAmountForAssetFraction(
151:        ISingularity singularity,
152:        uint256 fraction
153:    ) public view returns (uint256 amount) {
154:        (uint128 totalAssetElastic, uint128 totalAssetBase) = singularity
155:            .totalAsset();
156:
157:        IYieldBoxBase yieldBox = IYieldBoxBase(singularity.yieldBox());
158:        return
159:            yieldBox.toAmount(
160:                singularity.assetId(),
161:                (fraction * totalAssetElastic) / totalAssetBase,
162:                false
163:            );
164:    }
```
#### **Context**:-
[MagnetarV2.sol#L150-L164](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L150-L164)

```solidity
171:    function getFractionForAmount(
172:        ISingularity singularity,
173:        uint256 amount
174:    ) public view returns (uint256 fraction) {
175:        (uint128 totalAssetShare, uint128 totalAssetBase) = singularity
176:            .totalAsset();
177:        (uint128 totalBorrowElastic, ) = singularity.totalBorrow();
178:        uint256 assetId = singularity.assetId();
179:
180:        IYieldBoxBase yieldBox = IYieldBoxBase(singularity.yieldBox());
181:
182:        uint256 share = yieldBox.toShare(assetId, amount, false);
183:        uint256 allShare = totalAssetShare +
184:            yieldBox.toShare(assetId, totalBorrowElastic, true);
185:
186:        fraction = allShare == 0 ? share : (share * totalAssetBase) / allShare;
187:    }
```
#### **Context**:-
[MagnetarV2.sol#L171-L187](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2.sol#L171-L187)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
81:    function compoundAmount() public pure returns (uint256 result) {
82:        return 0;
83:    }
```
#### **Context**:-
[YearnStrategy.sol#L81-L83](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L81-L83)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
80:    function compoundAmount() public pure returns (uint256 result) {
81:        return 0;
82:    }
```
#### **Context**:-
[CompoundStrategy.sol#L80-L82](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L80-L82)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
84:    function compoundAmount() public pure returns (uint256 result) {
85:        return 0;
86:    }
```
#### **Context**:-
[LidoEthStrategy.sol#L84-L86](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L84-L86)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```

```solidity
104:    function harvestGmx(uint256 priceNum, uint256 priceDenom) public onlyOwner {
105:        _claimRewards();
106:        _sellGmx(priceNum, priceDenom);
107:        _buyGlp();
108:        _vestByGlp();
109:        _stakeEsGmx();
110:        _vestByEsGmx();
111:    }
```
#### **Context**:-
[GlpStrategy.sol#L104-L111](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L104-L111)

```solidity
File:YieldBox/contracts/NativeTokenFactory.sol
```

```solidity
56:    function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {
57:        if (direct) {
58:            // Checks
59:            require(newOwner != address(0) || renounce, "NTF: zero address");
60:
61:            // Effects
62:            emit OwnershipTransferred(tokenId, owner[tokenId], newOwner);
63:            owner[tokenId] = newOwner;
64:            pendingOwner[tokenId] = address(0);
65:        } else {
66:            // Effects
67:            pendingOwner[tokenId] = newOwner;
68:        }
69:    }
```
#### **Context**:-
[NativeTokenFactory.sol#L56-L69](https://github.com/Tapioca-DAO/contracts/NativeTokenFactory.sol#L56-L69)

```solidity
90:    function createToken(string calldata name, string calldata symbol, uint8 decimals, string calldata uri) public returns (uint32 tokenId) {
91:        // To keep each Token unique in the AssetRegister, we use the assetId as the tokenId. So for native assets, the tokenId is always equal to the assetId.
92:        tokenId = assets.length.to32();
93:        _registerAsset(TokenType.Native, address(0), NO_STRATEGY, tokenId);
94:        // Initial supply is 0, use owner can mint. For a fixed supply the owner can mint and revoke ownership.
95:        // The msg.sender is the initial owner, can be changed after.
96:        nativeTokens[tokenId] = NativeToken(name, symbol, decimals, uri);
97:        owner[tokenId] = msg.sender;
98:
99:        emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);
100:        emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);
101:        emit OwnershipTransferred(tokenId, address(0), msg.sender);
102:    }
```
#### **Context**:-
[NativeTokenFactory.sol#L90-L102](https://github.com/Tapioca-DAO/contracts/NativeTokenFactory.sol#L90-L102)

```solidity
127:    function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner(tokenId) {
128:        uint256 len = tos.length;
129:        for (uint256 i = 0; i < len; i++) {
130:            _mint(tos[i], tokenId, amounts[i]);
131:        }
132:    }
```
#### **Context**:-
[NativeTokenFactory.sol#L127-L132](https://github.com/Tapioca-DAO/contracts/NativeTokenFactory.sol#L127-L132)

```solidity
138:    function batchBurn(uint256 tokenId, address[] calldata froms, uint256[] calldata amounts) public {
139:        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");
140:        uint256 len = froms.length;
141:        for (uint256 i = 0; i < len; i++) {
142:            _requireTransferAllowed(froms[i], isApprovedForAsset[froms[i]][msg.sender][tokenId]);
143:            _burn(froms[i], tokenId, amounts[i]);
144:        }
145:    }
```
#### **Context**:-
[NativeTokenFactory.sol#L138-L145](https://github.com/Tapioca-DAO/contracts/NativeTokenFactory.sol#L138-L145)

```solidity
File:YieldBox/contracts/YieldBox.sol
```

```solidity
168:    function depositNFTAsset(
169:        uint256 assetId,
170:        address from,
171:        address to
172:    ) public allowed(from, assetId) returns (uint256 amountOut, uint256 shareOut) {
173:        // Checks
174:        Asset storage asset = assets[assetId];
175:        require(asset.tokenType == TokenType.ERC721, "YieldBox: not ERC721");
176:
177:        // Effects
178:        _mint(to, assetId, 1);
179:
180:        // Interactions
181:        IERC721(asset.contractAddress).safeTransferFrom(from, address(asset.strategy), asset.tokenId);
182:
183:        asset.strategy.deposited(1);
184:
185:        emit Deposited(msg.sender, from, to, assetId, 1, 1, 1, 1, true);
186:
187:        return (1, 1);
188:    }
```
#### **Context**:-
[YieldBox.sol#L168-L188](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L168-L188)

```solidity
307:    function batchTransfer(address from, address to, uint256[] calldata assetIds_, uint256[] calldata shares_) public {
308:        uint256 len = assetIds_.length;
309:        for (uint256 i = 0; i < len; i++) {
310:            _requireTransferAllowed(from, isApprovedForAsset[from][msg.sender][assetIds_[i]]);
311:        }
312:
313:        _transferBatch(from, to, assetIds_, shares_);
314:    }
```
#### **Context**:-
[YieldBox.sol#L307-L314](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L307-L314)

```solidity
336:    function transferMultiple(address from, address[] calldata tos, uint256 assetId, uint256[] calldata shares) public allowed(from, assetId) {
337:        // Checks
338:        uint256 len = tos.length;
339:        for (uint256 i = 0; i < len; i++) {
340:            require(tos[i] != address(0), "YieldBox: to not set"); // To avoid a bad UI from burning funds
341:        }
342:
343:        // Effects
344:        uint256 _totalShares;
345:        for (uint256 i = 0; i < len; i++) {
346:            address to = tos[i];
347:            uint256 share_ = shares[i];
348:            balanceOf[to][assetId] += share_;
349:            _totalShares += share_;
350:            emit TransferSingle(msg.sender, from, to, assetId, share_);
351:        }
352:        balanceOf[from][assetId] -= _totalShares;
353:    }
```
#### **Context**:-
[YieldBox.sol#L336-L353](https://github.com/Tapioca-DAO/contracts/YieldBox.sol#L336-L353)

```solidity
File:YieldBox/contracts/YieldBoxPermit.sol
```

```solidity
72:    function permitAll(
73:        address owner,
74:        address spender,
75:        uint256 deadline,
76:        uint8 v,
77:        bytes32 r,
78:        bytes32 s
79:    ) public virtual {
80:        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");
81:
82:        bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
83:
84:        bytes32 hash = _hashTypedDataV4(structHash);
85:
86:        address signer = ECDSA.recover(hash, v, r, s);
87:        require(signer == owner, "YieldBoxPermit: invalid signature");
88:
89:        _setApprovalForAll(owner, spender, true);
90:    }
```
#### **Context**:-
[YieldBoxPermit.sol#L72-L90](https://github.com/Tapioca-DAO/contracts/YieldBoxPermit.sol#L72-L90)

## **NC-28** | Events may be emitted out of order due to reentranc
### **Description** :-
Ensure that events follow the best practice of check-effects-interaction, and are emitted before external calls

#### <ins>Proof Of Concept</ins>


```solidity
File:tap-token-audit/contracts/Vesting.sol
```

```solidity
120:        emit Claimed(msg.sender, _claimable);
```
#### **Context**:-
[Vesting.sol#L120](https://github.com/Tapioca-DAO/contracts/Vesting.sol#L120)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
128:        emit AmountQueued(amount);
```
#### **Context**:-
[YearnStrategy.sol#L128](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L128)

```solidity
149:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[YearnStrategy.sol#L149](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L149)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
132:        emit AmountQueued(amount);
```
#### **Context**:-
[CompoundStrategy.sol#L132](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L132)

```solidity
161:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[CompoundStrategy.sol#L161](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L161)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
137:        emit AmountQueued(amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L137](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L137)

```solidity
166:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L166](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L166)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```

```solidity
212:        emit AmountQueued(amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L212](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L212)

```solidity
244:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L244](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L244)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```

```solidity
225:        emit AmountQueued(amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L225](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L225)

```solidity
253:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L253](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L253)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```

```solidity
228:        emit AmountQueued(amount);
```
#### **Context**:-
[StargateStrategy.sol#L228](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L228)

```solidity
268:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[StargateStrategy.sol#L268](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L268)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```

```solidity
246:        emit AmountQueued(amount);
```
#### **Context**:-
[AaveStrategy.sol#L246](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L246)

```solidity
274:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[AaveStrategy.sol#L274](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L274)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
144:        emit AmountQueued(amount);
```
#### **Context**:-
[BalancerStrategy.sol#L144](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L144)

```solidity
215:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[BalancerStrategy.sol#L215](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L215)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```

```solidity
307:        emit AmountQueued(amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L307](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L307)

```solidity
351:        emit AmountWithdrawn(to, amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L351](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L351)

## **NC-29** | internal functions not called by the contract should be removed
### **Description** :-
If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```

```solidity
87:    function _getRevertMsg(
88:        bytes memory _returnData
89:    ) internal pure returns (string memory) {
90:        // If the _res length is less than 68, then the transaction failed silently (without a revert message)
91:        if (_returnData.length < 68) return "USDO: no return data";
92:        // solhint-disable-next-line no-inline-assembly
93:        assembly {
94:            // Slice the sighash.
95:            _returnData := add(_returnData, 0x04)
96:        }
97:        return abi.decode(_returnData, (string)); // All that remains is the revert string
98:    }
```
#### **Context**:-
[BaseUSDOStorage.sol#L87-L98](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L87-L98)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol
```

```solidity
16:    function _addCollateral(
17:        address from,
18:        address to,
19:        bool skim,
20:        uint256 amount,
21:        uint256 share
22:    ) internal {
23:        if (share == 0) {
24:            share = yieldBox.toShare(collateralId, amount, false);
25:        }
26:        userCollateralShare[to] += share;
27:        uint256 oldTotalCollateralShare = totalCollateralShare;
28:        totalCollateralShare = oldTotalCollateralShare + share;
29:        _addTokens(
30:            from,
31:            to,
32:            collateralId,
33:            share,
34:            oldTotalCollateralShare,
35:            skim
36:        );
37:        emit LogAddCollateral(skim ? addresshttps://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999) : from, to, share);
38:    }
```
#### **Context**:-
[SGLLendingCommon.sol#L16-L38](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L16-L38)

```solidity
41:    function _removeCollateral(
42:        address from,
43:        address to,
44:        uint256 share
45:    ) internal {
46:        userCollateralShare[from] -= share;
47:        totalCollateralShare -= share;
48:        emit LogRemoveCollateral(from, to, share);
49:        yieldBox.transfer(address(this), to, collateralId, share);
50:        if (share > _yieldBoxShares[from][COLLATERAL_SIG]) {
51:            _yieldBoxShares[from][COLLATERAL_SIG] = 0; //accrues in time
52:        } else {
53:            _yieldBoxShares[from][COLLATERAL_SIG] -= share;
54:        }
55:    }
```
#### **Context**:-
[SGLLendingCommon.sol#L41-L55](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L41-L55)

```solidity
58:    function _borrow(
59:        address from,
60:        address to,
61:        uint256 amount
62:    ) internal returns (uint256 part, uint256 share) {
63:        uint256 feeAmount = (amount * borrowOpeningFee) / FEE_PRECISION; // A flat % fee is charged for any borrow
64:
65:        (totalBorrow, part) = totalBorrow.add(amount + feeAmount, true);
66:        require(
67:            totalBorrowCap == 0 || totalBorrow.base <= totalBorrowCap,
68:            "SGL: borrow cap reached"
69:        );
70:        userBorrowPart[from] += part;
71:        emit LogBorrow(from, to, amount, feeAmount, part);
72:
73:        share = yieldBox.toShare(assetId, amount, false);
74:        Rebase memory _totalAsset = totalAsset;
75:        require(_totalAsset.base >= 1000, "SGL: min limit");
76:        _totalAsset.elastic -= uint128(share);
77:        totalAsset = _totalAsset;
78:
79:        yieldBox.transfer(address(this), to, assetId, share);
80:    }
```
#### **Context**:-
[SGLLendingCommon.sol#L58-L80](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L58-L80)

```solidity
83:    function _repay(
84:        address from,
85:        address to,
86:        bool skim,
87:        uint256 part
88:    ) internal returns (uint256 amount) {
89:        (totalBorrow, amount) = totalBorrow.sub(part, true);
90:
91:        userBorrowPart[to] -= part;
92:
93:        uint256 share = yieldBox.toShare(assetId, amount, true);
94:        uint128 totalShare = totalAsset.elastic;
95:        _addTokens(from, to, assetId, share, uint256(totalShare), skim);
96:        totalAsset.elastic = totalShare + uint128(share);
97:        emit LogRepay(skim ? addresshttps://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999) : from, to, amount, part);
98:    }
```
#### **Context**:-
[SGLLendingCommon.sol#L83-L98](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLLendingCommon.sol#L83-L98)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```

```solidity
195:    function _accrue() internal virtual override {}
```
#### **Context**:-
[SGLStorage.sol#L195](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L195)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol
```

```solidity
197:    function _addAsset(
198:        address from,
199:        address to,
200:        bool skim,
201:        uint256 share
202:    ) internal returns (uint256 fraction) {
203:        Rebase memory _totalAsset = totalAsset;
204:        uint256 totalAssetShare = _totalAsset.elastic;
205:        uint256 allShare = _totalAsset.elastic +
206:            yieldBox.toShare(assetId, totalBorrow.elastic, true);
207:        fraction = allShare == 0
208:            ? share
209:            : (share * _totalAsset.base) / allShare;
210:        if (_totalAsset.base + uint128(fraction) < 1000) {
211:            return 0;
212:        }
213:        totalAsset = _totalAsset.add(share, fraction);
214:        balanceOf[to] += fraction;
215:        emit Transfer(address(0), to, fraction);
216:
217:        _addTokens(from, to, assetId, share, totalAssetShare, skim);
218:        emit LogAddAsset(skim ? addresshttps://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999) : from, to, share, fraction);
219:    }
```
#### **Context**:-
[SGLCommon.sol#L197-L219](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLCommon.sol#L197-L219)

```solidity
223:    function _removeAsset(
224:        address from,
225:        address to,
226:        uint256 fraction,
227:        bool updateYieldBoxShares
228:    ) internal returns (uint256 share) {
229:        if (totalAsset.base == 0) {
230:            return 0;
231:        }
232:        Rebase memory _totalAsset = totalAsset;
233:        uint256 allShare = _totalAsset.elastic +
234:            yieldBox.toShare(assetId, totalBorrow.elastic, true);
235:        share = (fraction * allShare) / _totalAsset.base;
236:        balanceOf[from] -= fraction;
237:        emit Transfer(from, address(0), fraction);
238:        _totalAsset.elastic -= uint128(share);
239:        _totalAsset.base -= uint128(fraction);
240:        require(_totalAsset.base >= 1000, "SGL: min limit");
241:        totalAsset = _totalAsset;
242:        emit LogRemoveAsset(from, to, share, fraction);
243:        yieldBox.transfer(address(this), to, assetId, share);
244:        if (updateYieldBoxShares) {
245:            if (share > _yieldBoxShares[from][ASSET_SIG]) {
246:                _yieldBoxShares[from][ASSET_SIG] = 0; //some assets accrue in time
247:            } else {
248:                _yieldBoxShares[from][ASSET_SIG] -= share;
249:            }
250:        }
251:    }
```
#### **Context**:-
[SGLCommon.sol#L223-L251](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLCommon.sol#L223-L251)

```solidity
254:    function _getAmountForBorrowPart(
255:        uint256 borrowPart
256:    ) internal view returns (uint256) {
257:        return totalBorrow.toElastic(borrowPart, false);
258:    }
```
#### **Context**:-
[SGLCommon.sol#L254-L258](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLCommon.sol#L254-L258)

```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDO.sol
```

```solidity
399:    function _nonblockingLzReceive(
400:        uint16 _srcChainId,
401:        bytes memory _srcAddress,
402:        uint64 _nonce,
403:        bytes memory _payload
404:    ) internal virtual override {
405:        uint256 packetType = _payload.toUint256(0);
406:
407:        if (packetType == PT_YB_SEND_SGL_LEND_OR_REPAY) {
408:            _executeOnDestination(
409:                Module.Market,
410:                abi.encodeWithSelector(
411:                    USDOMarketModule.lend.selector,
412:                    marketModule,
413:                    _srcChainId,
414:                    _srcAddress,
415:                    _nonce,
416:                    _payload
417:                ),
418:                _srcChainId,
419:                _srcAddress,
420:                _nonce,
421:                _payload
422:            );
423:        } else if (packetType == PT_LEVERAGE_MARKET_UP) {
424:            _executeOnDestination(
425:                Module.Leverage,
426:                abi.encodeWithSelector(
427:                    USDOLeverageModule.leverageUp.selector,
428:                    leverageModule,
429:                    _srcChainId,
430:                    _srcAddress,
431:                    _nonce,
432:                    _payload
433:                ),
434:                _srcChainId,
435:                _srcAddress,
436:                _nonce,
437:                _payload
438:            );
439:        } else if (packetType == PT_MARKET_REMOVE_ASSET) {
440:            _executeOnDestination(
441:                Module.Market,
442:                abi.encodeWithSelector(
443:                    USDOMarketModule.remove.selector,
444:                    _payload
445:                ),
446:                _srcChainId,
447:                _srcAddress,
448:                _nonce,
449:                _payload
450:            );
451:        } else if (packetType == PT_MARKET_MULTIHOP_BUY) {
452:            _executeOnDestination(
453:                Module.Leverage,
454:                abi.encodeWithSelector(
455:                    USDOLeverageModule.multiHop.selector,
456:                    _payload
457:                ),
458:                _srcChainId,
459:                _srcAddress,
460:                _nonce,
461:                _payload
462:            );
463:        } else if (packetType == PT_TAP_EXERCISE) {
464:            _executeOnDestination(
465:                Module.Options,
466:                abi.encodeWithSelector(
467:                    USDOOptionsModule.exercise.selector,
468:                    optionsModule,
469:                    _srcChainId,
470:                    _srcAddress,
471:                    _nonce,
472:                    _payload
473:                ),
474:                _srcChainId,
475:                _srcAddress,
476:                _nonce,
477:                _payload
478:            );
479:        } else if (packetType == PT_SEND_FROM) {
480:            _executeOnDestination(
481:                Module.Options,
482:                abi.encodeWithSelector(
483:                    USDOOptionsModule.sendFromDestination.selector,
484:                    _payload
485:                ),
486:                _srcChainId,
487:                _srcAddress,
488:                _nonce,
489:                _payload
490:            );
491:        } else {
492:            packetType = _payload.toUint8(0);
493:            if (packetType == PT_SEND) {
494:                _sendAck(_srcChainId, _srcAddress, _nonce, _payload);
495:            } else if (packetType == PT_SEND_AND_CALL) {
496:                _sendAndCallAck(_srcChainId, _srcAddress, _nonce, _payload);
497:            } else {
498:                revert("OFTCoreV2: unknown packet type");
499:            }
500:        }
501:    }
```
#### **Context**:-
[BaseUSDO.sol#L399-L501](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDO.sol#L399-L501)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
372:    function _getRevertMsg(
373:        bytes memory _returnData
374:    ) internal pure returns (string memory) {
375:        // If the _res length is less than 68, then the transaction failed silently (without a revert message)
376:        if (_returnData.length < 68) return "Market: no return data";
377:        // solhint-disable-next-line no-inline-assembly
378:        assembly {
379:            // Slice the sighash.
380:            _returnData := add(_returnData, 0x04)
381:        }
382:        return abi.decode(_returnData, (string)); // All that remains is the revert string
383:    }
```
#### **Context**:-
[Market.sol#L372-L383](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L372-L383)

```solidity
464:    function _computeAllowanceAmountInAsset(
465:        address user,
466:        uint256 _exchangeRate,
467:        uint256 borrowAmount,
468:        uint256 assetDecimals
469:    ) internal view returns (uint256) {
470:        uint256 maxBorrowabe = _computeMaxBorrowableAmount(user, _exchangeRate);
471:
472:        uint256 shareRatio = _getRatio(
473:            borrowAmount,
474:            maxBorrowabe,
475:            assetDecimals
476:        );
477:        return (shareRatio * userCollateralShare[user]) / (10 ** assetDecimals);
478:    }
```
#### **Context**:-
[Market.sol#L464-L478](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L464-L478)

```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol
```

```solidity
67:    function _getRevertMsg(
68:        bytes memory _returnData
69:    ) internal pure returns (string memory) {
70:        // If the _res length is less than 68, then the transaction failed silently (without a revert message)
71:        if (_returnData.length < 68) return "TOFT_data";
72:        // solhint-disable-next-line no-inline-assembly
73:        assembly {
74:            // Slice the sighash.
75:            _returnData := add(_returnData, 0x04)
76:        }
77:        return abi.decode(_returnData, (string)); // All that remains is the revert string
78:    }
```
#### **Context**:-
[BaseTOFTStorage.sol#L67-L78](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFTStorage.sol#L67-L78)

```solidity
File:tapiocaz-audit/contracts/tOFT/BaseTOFT.sol
```

```solidity
363:    function _wrapNative(address _toAddress) internal virtual {
364:        require(msg.value > 0, "TOFT_0");
365:        _mint(_toAddress, msg.value);
366:    }
```
#### **Context**:-
[BaseTOFT.sol#L363-L366](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFT.sol#L363-L366)

```solidity
368:    function _unwrap(address _toAddress, uint256 _amount) internal virtual {
369:        _burn(msg.sender, _amount);
370:
371:        if (erc20 == address(0)) {
372:            _safeTransferETH(_toAddress, _amount);
373:        } else {
374:            IERC20(erc20).safeTransfer(_toAddress, _amount);
375:        }
376:    }
```
#### **Context**:-
[BaseTOFT.sol#L368-L376](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFT.sol#L368-L376)

```solidity
442:    function _nonblockingLzReceive(
443:        uint16 _srcChainId,
444:        bytes memory _srcAddress,
445:        uint64 _nonce,
446:        bytes memory _payload
447:    ) internal virtual override {
448:        uint256 packetType = _payload.toUint256(0);
449:
450:        if (packetType == PT_YB_SEND_STRAT) {
451:            _executeOnDestination(
452:                Module.Strategy,
453:                abi.encodeWithSelector(
454:                    BaseTOFTStrategyModule.strategyDeposit.selector,
455:                    strategyModule,
456:                    _srcChainId,
457:                    _srcAddress,
458:                    _nonce,
459:                    _payload,
460:                    IERC20(address(this))
461:                ),
462:                _srcChainId,
463:                _srcAddress,
464:                _nonce,
465:                _payload
466:            );
467:        } else if (packetType == PT_YB_RETRIEVE_STRAT) {
468:            _executeOnDestination(
469:                Module.Strategy,
470:                abi.encodeWithSelector(
471:                    BaseTOFTStrategyModule.strategyWithdraw.selector,
472:                    _srcChainId,
473:                    _payload
474:                ),
475:                _srcChainId,
476:                _srcAddress,
477:                _nonce,
478:                _payload
479:            );
480:        } else if (packetType == PT_LEVERAGE_MARKET_DOWN) {
481:            _executeOnDestination(
482:                Module.Leverage,
483:                abi.encodeWithSelector(
484:                    BaseTOFTLeverageModule.leverageDown.selector,
485:                    leverageModule,
486:                    _srcChainId,
487:                    _srcAddress,
488:                    _nonce,
489:                    _payload
490:                ),
491:                _srcChainId,
492:                _srcAddress,
493:                _nonce,
494:                _payload
495:            );
496:        } else if (packetType == PT_YB_SEND_SGL_BORROW) {
497:            _executeOnDestination(
498:                Module.Market,
499:                abi.encodeWithSelector(
500:                    BaseTOFTMarketModule.borrow.selector,
501:                    marketModule,
502:                    _srcChainId,
503:                    _srcAddress,
504:                    _nonce,
505:                    _payload
506:                ),
507:                _srcChainId,
508:                _srcAddress,
509:                _nonce,
510:                _payload
511:            );
512:        } else if (packetType == PT_MARKET_REMOVE_COLLATERAL) {
513:            _executeOnDestination(
514:                Module.Market,
515:                abi.encodeWithSelector(
516:                    BaseTOFTMarketModule.remove.selector,
517:                    _payload
518:                ),
519:                _srcChainId,
520:                _srcAddress,
521:                _nonce,
522:                _payload
523:            );
524:        } else if (packetType == PT_MARKET_MULTIHOP_SELL) {
525:            _executeOnDestination(
526:                Module.Leverage,
527:                abi.encodeWithSelector(
528:                    BaseTOFTLeverageModule.multiHop.selector,
529:                    _payload
530:                ),
531:                _srcChainId,
532:                _srcAddress,
533:                _nonce,
534:                _payload
535:            );
536:        } else if (packetType == PT_TAP_EXERCISE) {
537:            _executeOnDestination(
538:                Module.Options,
539:                abi.encodeWithSelector(
540:                    BaseTOFTOptionsModule.exercise.selector,
541:                    _srcChainId,
542:                    _srcAddress,
543:                    _nonce,
544:                    _payload
545:                ),
546:                _srcChainId,
547:                _srcAddress,
548:                _nonce,
549:                _payload
550:            );
551:        } else if (packetType == PT_SEND_FROM) {
552:            _executeOnDestination(
553:                Module.Options,
554:                abi.encodeWithSelector(
555:                    BaseTOFTOptionsModule.sendFromDestination.selector,
556:                    _payload
557:                ),
558:                _srcChainId,
559:                _srcAddress,
560:                _nonce,
561:                _payload
562:            );
563:        } else {
564:            packetType = _payload.toUint8(0);
565:            if (packetType == PT_SEND) {
566:                _sendAck(_srcChainId, _srcAddress, _nonce, _payload);
567:            } else if (packetType == PT_SEND_AND_CALL) {
568:                _sendAndCallAck(_srcChainId, _srcAddress, _nonce, _payload);
569:            } else {
570:                revert("TOFT_packet");
571:            }
572:        }
573:    }
```
#### **Context**:-
[BaseTOFT.sol#L442-L573](https://github.com/Tapioca-DAO/contracts/tOFT/BaseTOFT.sol#L442-L573)

```solidity
File:tap-token-audit/contracts/governance/twTAP.sol
```

```solidity
571:    function _getChainId() internal view virtual returns (uint256) {
572:        uint256 chainId;
573:        assembly {
574:            chainId := chainid()
575:        }
576:        return chainId;
577:    }
```
#### **Context**:-
[twTAP.sol#L571-L577](https://github.com/Tapioca-DAO/contracts/governance/twTAP.sol#L571-L577)

```solidity
File:tap-token-audit/contracts/twAML.sol
```

```solidity
115:    function computeMinWeight(
116:        uint256 _totalWeight,
117:        uint256 _minWeightFactor
118:    ) internal pure returns (uint256) {
119:        uint256 mul = (_totalWeight * _minWeightFactor);
120:        return mul >= 1e4 ? mul / 1e4 : _totalWeight;
121:    }
```
#### **Context**:-
[twAML.sol#L115-L121](https://github.com/Tapioca-DAO/contracts/twAML.sol#L115-L121)

```solidity
123:    function computeMagnitude(
124:        uint256 _timeWeight,
125:        uint256 _cumulative
126:    ) internal pure returns (uint256) {
127:        return
128:            sqrt(_timeWeight * _timeWeight + _cumulative * _cumulative) -
129:            _cumulative;
130:    }
```
#### **Context**:-
[twAML.sol#L123-L130](https://github.com/Tapioca-DAO/contracts/twAML.sol#L123-L130)

```solidity
132:    function computeTarget(
133:        uint256 _dMin,
134:        uint256 _dMax,
135:        uint256 _magnitude,
136:        uint256 _cumulative
137:    ) internal pure returns (uint256) {
138:        if (_cumulative == 0) {
139:            return _dMax;
140:        }
141:        uint256 target = (_magnitude * _dMax) / _cumulative;
142:        target = target > _dMax ? _dMax : target < _dMin ? _dMin : target;
143:        return target;
144:    }
```
#### **Context**:-
[twAML.sol#L132-L144](https://github.com/Tapioca-DAO/contracts/twAML.sol#L132-L144)

```solidity
File:tap-token-audit/contracts/tokens/BaseTapOFT.sol
```

```solidity
52:    function _nonblockingLzReceive(
53:        uint16 _srcChainId,
54:        bytes memory _srcAddress,
55:        uint64 _nonce,
56:        bytes memory _payload
57:    ) internal virtual override {
58:        uint256 packetType = _payload.toUint256(0);
59:
60:        if (packetType == PT_LOCK_TWTAP) {
61:            _lockTwTapPosition(_srcChainId, _payload);
62:        } else if (packetType == PT_UNLOCK_TWTAP) {
63:            _unlockTwTapPosition(_srcChainId, _payload);
64:        } else if (packetType == PT_CLAIM_REWARDS) {
65:            _claimRewards(_srcChainId, _payload);
66:        } else {
67:            packetType = _payload.toUint8(0);
68:            if (packetType == PT_SEND) {
69:                _sendAck(_srcChainId, _srcAddress, _nonce, _payload);
70:            } else if (packetType == PT_SEND_AND_CALL) {
71:                _sendAndCallAck(_srcChainId, _srcAddress, _nonce, _payload);
72:            } else {
73:                revert("TOFT_packet");
74:            }
75:        }
76:    }
```
#### **Context**:-
[BaseTapOFT.sol#L52-L76](https://github.com/Tapioca-DAO/contracts/tokens/BaseTapOFT.sol#L52-L76)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```

```solidity
336:    function _checkSender(address _from) internal view {
337:        require(_from == msg.sender, "MagnetarV2: operator not approved");
338:    }
```
#### **Context**:-
[MagnetarV2Storage.sol#L336-L338](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L336-L338)

```solidity
File:tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol
```

```solidity
98:    function _safeApprove(address token, address to, uint256 value) internal {
99:        (bool success, bytes memory data) = token.call(
100:            abi.encodeWithSelector(0x095ea7b3, to, value)
101:        );
102:        require(
103:            success && (data.length == 0 || abi.decode(data, (bool))),
104:            "BaseSwapper::safeApprove: approve failed"
105:        );
106:    }
```
#### **Context**:-
[BaseSwapper.sol#L98-L106](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L98-L106)

```solidity
108:    function _getTokens(
109:        ISwapper.SwapTokensData calldata tokens,
110:        IYieldBox _yieldBox
111:    ) internal view returns (address tokenIn, address tokenOut) {
112:        if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {
113:            tokenIn = tokens.tokenIn;
114:            tokenOut = tokens.tokenOut;
115:        } else {
116:            (, tokenIn, , ) = _yieldBox.assets(tokens.tokenInId);
117:            (, tokenOut, , ) = _yieldBox.assets(tokens.tokenOutId);
118:        }
119:    }
```
#### **Context**:-
[BaseSwapper.sol#L108-L119](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L108-L119)

```solidity
121:    function _getAmounts(
122:        ISwapper.SwapAmountData calldata amounts,
123:        uint256 tokenInId,
124:        uint256 tokenOutId,
125:        IYieldBox _yieldBox
126:    ) internal view returns (uint256 amountIn, uint256 amountOut) {
127:        if (amounts.amountIn > 0 || amounts.amountOut > 0) {
128:            amountIn = amounts.amountIn;
129:            amountOut = amounts.amountOut;
130:        } else {
131:            if (tokenInId > 0) {
132:                amountIn = amounts.amountIn == 0
133:                    ? _yieldBox.toAmount(tokenInId, amounts.shareIn, false)
134:                    : amounts.amountIn;
135:            }
136:            if (tokenOutId > 0) {
137:                amountOut = amounts.amountOut == 0
138:                    ? _yieldBox.toAmount(tokenOutId, amounts.shareOut, false)
139:                    : amounts.amountOut;
140:            }
141:        }
142:    }
```
#### **Context**:-
[BaseSwapper.sol#L121-L142](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L121-L142)

```solidity
144:    function _extractTokens(
145:        ISwapper.YieldBoxData calldata ybData,
146:        IYieldBox _yieldBox,
147:        address token,
148:        uint256 tokenId,
149:        uint256 amount,
150:        uint256 share
151:    ) internal returns (uint256) {
152:        if (ybData.withdrawFromYb) {
153:            (amount, share) = _yieldBox.withdraw(
154:                tokenId,
155:                address(this),
156:                address(this),
157:                amount,
158:                share
159:            );
160:            return amount;
161:        }
162:        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
163:        return amount;
164:    }
```
#### **Context**:-
[BaseSwapper.sol#L144-L164](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L144-L164)

```solidity
166:    function _createPath(
167:        address tokenIn,
168:        address tokenOut
169:    ) internal pure returns (address[] memory path) {
170:        path = new address[](2);
171:        path[0] = tokenIn;
172:        path[1] = tokenOut;
173:    }
```
#### **Context**:-
[BaseSwapper.sol#L166-L173](https://github.com/Tapioca-DAO/contracts/Swapper/BaseSwapper.sol#L166-L173)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
121:    function _deposited(uint256 amount) internal override nonReentrant {
122:        uint256 queued = wrappedNative.balanceOf(address(this));
123:        if (queued > depositThreshold) {
124:            vault.deposit(queued, address(this));
125:            emit AmountDeposited(queued);
126:            return;
127:        }
128:        emit AmountQueued(amount);
129:    }
```
#### **Context**:-
[YearnStrategy.sol#L121-L129](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L121-L129)

```solidity
132:    function _withdraw(
133:        address to,
134:        uint256 amount
135:    ) internal override nonReentrant {
136:        uint256 available = _currentBalance();
137:        require(available >= amount, "YearnStrategy: amount not valid");
138:
139:        uint256 queued = wrappedNative.balanceOf(address(this));
140:        if (amount > queued) {
141:            uint256 pricePerShare = vault.pricePerShare();
142:            uint256 toWithdraw = (((amount - queued) *
143:                (10 ** vault.decimals())) / pricePerShare);
144:
145:            vault.withdraw(toWithdraw, address(this), 0);
146:        }
147:        wrappedNative.safeTransfer(to, amount - 1); //rounding error
148:
149:        emit AmountWithdrawn(to, amount);
150:    }
```
#### **Context**:-
[YearnStrategy.sol#L132-L150](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L132-L150)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
123:    function _deposited(uint256 amount) internal override nonReentrant {
124:        uint256 queued = wrappedNative.balanceOf(address(this));
125:        if (queued > depositThreshold) {
126:            INative(address(wrappedNative)).withdraw(queued);
127:
128:            cToken.mint{value: queued}();
129:            emit AmountDeposited(queued);
130:            return;
131:        }
132:        emit AmountQueued(amount);
133:    }
```
#### **Context**:-
[CompoundStrategy.sol#L123-L133](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L123-L133)

```solidity
136:    function _withdraw(
137:        address to,
138:        uint256 amount
139:    ) internal override nonReentrant {
140:        uint256 available = _currentBalance();
141:        require(available >= amount, "CompoundStrategy: amount not valid");
142:
143:        uint256 queued = wrappedNative.balanceOf(address(this));
144:        if (amount > queued) {
145:            uint256 pricePerShare = cToken.exchangeRateStored();
146:            uint256 toWithdraw = (((amount - queued) * (10 ** 18)) /
147:                pricePerShare);
148:
149:            cToken.redeem(toWithdraw);
150:            INative(address(wrappedNative)).deposit{
151:                value: address(this).balance
152:            }();
153:        }
154:
155:        require(
156:            wrappedNative.balanceOf(address(this)) >= amount,
157:            "CompoundStrategy: not enough"
158:        );
159:        wrappedNative.safeTransfer(to, amount);
160:
161:        emit AmountWithdrawn(to, amount);
162:    }
```
#### **Context**:-
[CompoundStrategy.sol#L136-L162](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L136-L162)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
128:    function _deposited(uint256 amount) internal override nonReentrant {
129:        uint256 queued = wrappedNative.balanceOf(address(this));
130:        if (queued > depositThreshold) {
131:            require(!stEth.isStakingPaused(), "LidoStrategy: staking paused");
132:            INative(address(wrappedNative)).withdraw(queued);
133:            stEth.submit{value: queued}(address(0)); //1:1 between eth<>stEth
134:            emit AmountDeposited(queued);
135:            return;
136:        }
137:        emit AmountQueued(amount);
138:    }
```
#### **Context**:-
[LidoEthStrategy.sol#L128-L138](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L128-L138)

```solidity
141:    function _withdraw(
142:        address to,
143:        uint256 amount
144:    ) internal override nonReentrant {
145:        uint256 available = _currentBalance();
146:        require(available >= amount, "LidoStrategy: amount not valid");
147:
148:        uint256 queued = wrappedNative.balanceOf(address(this));
149:        if (amount > queued) {
150:            uint256 toWithdraw = amount - queued; //1:1 between eth<>stEth
151:            uint256 minAmount = toWithdraw - (toWithdraw * 250) / 10_000; //2.5%
152:            uint256 obtainedEth = curveStEthPool.exchange(
153:                1,
154:                0,
155:                toWithdraw,
156:                minAmount
157:            );
158:
159:            INative(address(wrappedNative)).deposit{value: obtainedEth}();
160:        }
161:        queued = wrappedNative.balanceOf(address(this));
162:        require(queued >= amount, "LidoStrategy: not enough");
163:
164:        wrappedNative.safeTransfer(to, amount);
165:
166:        emit AmountWithdrawn(to, amount);
167:    }
```
#### **Context**:-
[LidoEthStrategy.sol#L141-L167](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L141-L167)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```

```solidity
205:    function _deposited(uint256 amount) internal override nonReentrant {
206:        uint256 queued = wrappedNative.balanceOf(address(this));
207:        if (queued > depositThreshold) {
208:            _addLiquidityAndStake(queued);
209:            emit AmountDeposited(queued);
210:            return;
211:        }
212:        emit AmountQueued(amount);
213:    }
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L205-L213](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L205-L213)

```solidity
216:    function _withdraw(
217:        address to,
218:        uint256 amount
219:    ) internal override nonReentrant {
220:        uint256 available = _currentBalance();
221:        require(
222:            available >= amount,
223:            "TricryptoNativeStrategy: amount not valid"
224:        );
225:
226:        uint256 queued = wrappedNative.balanceOf(address(this));
227:        if (amount > queued) {
228:            compound("");
229:            uint256 lpBalance = lpGauge.balanceOf(address(this));
230:            lpGauge.withdraw(lpBalance, true);
231:            uint256 calcWithdraw = lpGetter.calcLpToWeth(lpBalance);
232:            uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%
233:            lpGetter.removeLiquidityWeth(lpBalance, minAmount);
234:        }
235:        require(
236:            wrappedNative.balanceOf(address(this)) >= amount,
237:            "TricryptoNativeStrategy: not enough"
238:        );
239:        wrappedNative.safeTransfer(to, amount);
240:        queued = wrappedNative.balanceOf(address(this));
241:        if (queued > 0) {
242:            _addLiquidityAndStake(queued);
243:        }
244:        emit AmountWithdrawn(to, amount);
245:    }
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L216-L245](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L216-L245)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```

```solidity
218:    function _deposited(uint256 amount) internal override nonReentrant {
219:        uint256 queued = lpToken.balanceOf(address(this));
220:        if (queued > depositThreshold) {
221:            lpGauge.deposit(queued, address(this), false);
222:            emit AmountDeposited(queued);
223:            return;
224:        }
225:        emit AmountQueued(amount);
226:    }
```
#### **Context**:-
[TricryptoLPStrategy.sol#L218-L226](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L218-L226)

```solidity
229:    function _withdraw(
230:        address to,
231:        uint256 amount
232:    ) internal override nonReentrant {
233:        uint256 available = _currentBalance();
234:        require(available >= amount, "TricryptoLPStrategy: amount not valid");
235:
236:        uint256 queued = lpToken.balanceOf(address(this));
237:        if (amount > queued) {
238:            compound("");
239:            uint256 lpBalance = lpGauge.balanceOf(address(this));
240:            lpGauge.withdraw(lpBalance, true);
241:        }
242:        require(
243:            lpToken.balanceOf(address(this)) >= amount,
244:            "TricryptoLPStrategy: not enough"
245:        );
246:        lpToken.safeTransfer(to, amount);
247:
248:        queued = lpToken.balanceOf(address(this));
249:        if (queued > 0) {
250:            lpGauge.deposit(queued, address(this), false);
251:        }
252:
253:        emit AmountWithdrawn(to, amount);
254:    }
```
#### **Context**:-
[TricryptoLPStrategy.sol#L229-L254](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L229-L254)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```

```solidity
223:    function _deposited(uint256 amount) internal override nonReentrant {
224:        uint256 queued = wrappedNative.balanceOf(address(this));
225:        if (queued > depositThreshold) {
226:            _stake(queued);
227:        }
228:        emit AmountQueued(amount);
229:    }
```
#### **Context**:-
[StargateStrategy.sol#L223-L229](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L223-L229)

```solidity
241:    function _withdraw(
242:        address to,
243:        uint256 amount
244:    ) internal override nonReentrant {
245:        uint256 available = _currentBalance();
246:        require(available >= amount, "StargateStrategy: amount not valid");
247:
248:        uint256 queued = wrappedNative.balanceOf(address(this));
249:        if (amount > queued) {
250:            compound("");
251:            uint256 toWithdraw = amount - queued;
252:            lpStaking.withdraw(lpStakingPid, toWithdraw);
253:            router.instantRedeemLocal(
254:                uint16(lpRouterPid),
255:                toWithdraw,
256:                address(this)
257:            );
258:
259:            INative(address(wrappedNative)).deposit{value: toWithdraw}();
260:        }
261:
262:        require(
263:            amount <= wrappedNative.balanceOf(address(this)),
264:            "Stargate: not enough"
265:        );
266:        wrappedNative.safeTransfer(to, amount);
267:
268:        emit AmountWithdrawn(to, amount);
269:    }
```
#### **Context**:-
[StargateStrategy.sol#L241-L269](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L241-L269)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```

```solidity
234:    function _deposited(uint256 amount) internal override nonReentrant {
235:        uint256 queued = wrappedNative.balanceOf(address(this));
236:        if (queued > depositThreshold) {
237:            lendingPool.deposit(
238:                address(wrappedNative),
239:                queued,
240:                address(this),
241:                0
242:            );
243:            emit AmountDeposited(queued);
244:            return;
245:        }
246:        emit AmountQueued(amount);
247:    }
```
#### **Context**:-
[AaveStrategy.sol#L234-L247](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L234-L247)

```solidity
250:    function _withdraw(
251:        address to,
252:        uint256 amount
253:    ) internal override nonReentrant {
254:        uint256 available = _currentBalance();
255:        require(available >= amount, "AaveStrategy: amount not valid");
256:
257:        uint256 queued = wrappedNative.balanceOf(address(this));
258:        if (amount > queued) {
259:            compound("");
260:
261:            uint256 toWithdraw = amount - queued;
262:
263:            uint256 obtainedWrapped = lendingPool.withdraw(
264:                address(wrappedNative),
265:                toWithdraw,
266:                address(this)
267:            );
268:            if (obtainedWrapped > toWithdraw) {
269:                amount += (obtainedWrapped - toWithdraw);
270:            }
271:        }
272:
273:        wrappedNative.safeTransfer(to, amount);
274:        emit AmountWithdrawn(to, amount);
275:    }
```
#### **Context**:-
[AaveStrategy.sol#L250-L275](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L250-L275)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
138:    function _deposited(uint256 amount) internal override nonReentrant {
139:        uint256 queued = wrappedNative.balanceOf(address(this));
140:        if (queued > depositThreshold) {
141:            _vaultDeposit(queued);
142:            emit AmountDeposited(queued);
143:        }
144:        emit AmountQueued(amount);
145:
146:        updateCache();
147:    }
```
#### **Context**:-
[BalancerStrategy.sol#L138-L147](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L138-L147)

```solidity
191:    function _withdraw(
192:        address to,
193:        uint256 amount
194:    ) internal override nonReentrant {
195:        uint256 available = _currentBalance();
196:        require(available >= amount, "BalancerStrategy: amount not valid");
197:
198:        uint256 queued = wrappedNative.balanceOf(address(this));
199:        if (amount > queued) {
200:            uint256 pricePerShare = pool.getRate();
201:            uint256 decimals = IStrictERC20(address(pool)).decimals();
202:            uint256 toWithdraw = (((amount - queued) * (10 ** decimals)) /
203:                pricePerShare);
204:
205:            _vaultWithdraw(toWithdraw);
206:        }
207:
208:        require(
209:            amount <= wrappedNative.balanceOf(address(this)),
210:            "BalancerStrategy: not enough"
211:        );
212:        wrappedNative.safeTransfer(to, amount);
213:        updateCache();
214:
215:        emit AmountWithdrawn(to, amount);
216:    }
```
#### **Context**:-
[BalancerStrategy.sol#L191-L216](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L191-L216)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol
```

```solidity
129:    function _currentBalance() internal view override returns (uint256 amount) {
130:        // This _should_ included both free and "reserved" GLP:
131:        amount = IERC20(contractAddress).balanceOf(address(this));
132:    }
```
#### **Context**:-
[GlpStrategy.sol#L129-L132](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L129-L132)

```solidity
134:    function _deposited(uint256 /* amount */) internal override {
135:        harvest();
136:    }
```
#### **Context**:-
[GlpStrategy.sol#L134-L136](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L134-L136)

```solidity
138:    function _withdraw(address to, uint256 amount) internal override {
139:        _claimRewards();
140:        _buyGlp();
141:        uint256 freeGlp = stakedGlpTracker.balanceOf(address(this));
142:        if (freeGlp < amount) {
143:            // Reverts if none are vesting, but in that case the whole TX will
144:            // revert anyway for withdrawing too much:
145:            glpVester.withdraw();
146:        }
147:        // Call this first; `_vestByGlp()` will lock the GLP again
148:        IERC20(contractAddress).safeTransfer(to, amount);
149:        _vestByGlp();
150:        _stakeEsGmx();
151:        _vestByEsGmx();
152:    }
```
#### **Context**:-
[GlpStrategy.sol#L138-L152](https://github.com/Tapioca-DAO/contracts/glp/GlpStrategy.sol#L138-L152)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```

```solidity
300:    function _deposited(uint256 amount) internal override nonReentrant {
301:        uint256 queued = wrappedNative.balanceOf(address(this));
302:        if (queued > depositThreshold) {
303:            _addLiquidityAndStake(queued);
304:            emit AmountDeposited(queued);
305:            return;
306:        }
307:        emit AmountQueued(amount);
308:    }
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L300-L308](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L300-L308)

```solidity
320:    function _withdraw(
321:        address to,
322:        uint256 amount
323:    ) internal override nonReentrant {
324:        uint256 available = _currentBalance();
325:        require(
326:            available >= amount,
327:            "ConvexTricryptoStrategy: amount not valid"
328:        );
329:
330:        uint256 queued = wrappedNative.balanceOf(address(this));
331:        if (amount > queued) {
332:            compound(bytes(""));
333:            uint256 lpBalance = rewardPool.balanceOf(address(this));
334:            rewardPool.withdrawAndUnwrap(lpBalance, false);
335:            uint256 calcWithdraw = lpGetter.calcLpToWeth(lpBalance);
336:            uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%
337:            lpGetter.removeLiquidityWeth(lpBalance, minAmount);
338:        }
339:
340:        require(
341:            wrappedNative.balanceOf(address(this)) >= amount,
342:            "ConvexTricryptoStrategy: not enough"
343:        );
344:        wrappedNative.safeTransfer(to, amount);
345:
346:        queued = wrappedNative.balanceOf(address(this));
347:        if (queued > depositThreshold) {
348:            _addLiquidityAndStake(queued);
349:            emit AmountDeposited(queued);
350:        }
351:        emit AmountWithdrawn(to, amount);
352:    }
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L320-L352](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L320-L352)

```solidity
File:YieldBox/contracts/BoringMath.sol
```

```solidity
5:    function to128(uint256 a) internal pure returns (uint128 c) {
6:        require(a <= type(uint128).max, "BoringMath: uint128 Overflow");
7:        c = uint128(a);
8:    }
```
#### **Context**:-
[BoringMath.sol#L5-L8](https://github.com/Tapioca-DAO/contracts/BoringMath.sol#L5-L8)

```solidity
10:    function to64(uint256 a) internal pure returns (uint64 c) {
11:        require(a <= type(uint64).max, "BoringMath: uint64 Overflow");
12:        c = uint64(a);
13:    }
```
#### **Context**:-
[BoringMath.sol#L10-L13](https://github.com/Tapioca-DAO/contracts/BoringMath.sol#L10-L13)

```solidity
15:    function to32(uint256 a) internal pure returns (uint32 c) {
16:        require(a <= type(uint32).max, "BoringMath: uint32 Overflow");
17:        c = uint32(a);
18:    }
```
#### **Context**:-
[BoringMath.sol#L15-L18](https://github.com/Tapioca-DAO/contracts/BoringMath.sol#L15-L18)

```solidity
20:    function muldiv(
21:        uint256 value,
22:        uint256 mul,
23:        uint256 div,
24:        bool roundUp
25:    ) internal pure returns (uint256 result) {
26:        result = (value * mul) / div;
27:        if (roundUp && (result * div) / mul < value) {
28:            result++;
29:        }
30:    }
```
#### **Context**:-
[BoringMath.sol#L20-L30](https://github.com/Tapioca-DAO/contracts/BoringMath.sol#L20-L30)

```solidity
File:YieldBox/contracts/YieldBoxRebase.sol
```

```solidity
18:    function _toShares(
19:        uint256 amount,
20:        uint256 totalShares_,
21:        uint256 totalAmount,
22:        bool roundUp
23:    ) internal pure returns (uint256 share) {
24:        // To prevent reseting the ratio due to withdrawal of all shares, we start with
25:        // 1 amount/1e8 shares already burned. This also starts with a 1 : 1e8 ratio which
26:        // functions like 8 decimal fixed point math. This prevents ratio attacks or inaccuracy
27:        // due to 'gifting' or rebasing tokens. (Up to a certain degree)
28:        totalAmount++;
29:        totalShares_ += 1e8;
30:
31:        // Calculte the shares using te current amount to share ratio
32:        share = (amount * totalShares_) / totalAmount;
33:
34:        // Default is to round down (Solidity), round up if required
35:        if (roundUp && (share * totalAmount) / totalShares_ < amount) {
36:            share++;
37:        }
38:    }
```
#### **Context**:-
[YieldBoxRebase.sol#L18-L38](https://github.com/Tapioca-DAO/contracts/YieldBoxRebase.sol#L18-L38)

```solidity
41:    function _toAmount(
42:        uint256 share,
43:        uint256 totalShares_,
44:        uint256 totalAmount,
45:        bool roundUp
46:    ) internal pure returns (uint256 amount) {
47:        // To prevent reseting the ratio due to withdrawal of all shares, we start with
48:        // 1 amount/1e8 shares already burned. This also starts with a 1 : 1e8 ratio which
49:        // functions like 8 decimal fixed point math. This prevents ratio attacks or inaccuracy
50:        // due to 'gifting' or rebasing tokens. (Up to a certain degree)
51:        totalAmount++;
52:        totalShares_ += 1e8;
53:
54:        // Calculte the amount using te current amount to share ratio
55:        amount = (share * totalAmount) / totalShares_;
56:
57:        // Default is to round down (Solidity), round up if required
58:        if (roundUp && (amount * totalShares_) / totalAmount < share) {
59:            amount++;
60:        }
61:    }
```
#### **Context**:-
[YieldBoxRebase.sol#L41-L61](https://github.com/Tapioca-DAO/contracts/YieldBoxRebase.sol#L41-L61)

## **NC-30** | Unused Event
### **Description** :-
Unused events increase contract size and gas usage at deployment.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol
```

```solidity
52:    event Minted(address indexed _for, uint256 _amount);
```
#### **Context**:-
[BaseUSDOStorage.sol#L52](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L52)

```solidity
54:    event Burned(address indexed _from, uint256 _amount);
```
#### **Context**:-
[BaseUSDOStorage.sol#L54](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L54)

```solidity
56:    event SetMinterStatus(address indexed _for, bool _status);
```
#### **Context**:-
[BaseUSDOStorage.sol#L56](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L56)

```solidity
58:    event SetBurnerStatus(address indexed _for, bool _status);
```
#### **Context**:-
[BaseUSDOStorage.sol#L58](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L58)

```solidity
60:    event ConservatorUpdated(address indexed old, address indexed _new);
```
#### **Context**:-
[BaseUSDOStorage.sol#L60](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L60)

```solidity
62:    event PausedUpdated(bool oldState, bool newState);
```
#### **Context**:-
[BaseUSDOStorage.sol#L62](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L62)

```solidity
64:    event FlashMintFeeUpdated(uint256 _old, uint256 _new);
```
#### **Context**:-
[BaseUSDOStorage.sol#L64](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L64)

```solidity
66:    event MaxFlashMintUpdated(uint256 _old, uint256 _new);
```
#### **Context**:-
[BaseUSDOStorage.sol#L66](https://github.com/Tapioca-DAO/contracts/usd0/BaseUSDOStorage.sol#L66)

```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```

```solidity
70:    event LogAccrue(
71:        uint256 accruedAmount,
72:        uint256 feeFraction,
73:        uint64 rate,
74:        uint256 utilization
75:    );
```
#### **Context**:-
[SGLStorage.sol#L70-L75](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L70-L75)

```solidity
77:    event LogAddCollateral(
78:        address indexed from,
79:        address indexed to,
80:        uint256 share
81:    );
```
#### **Context**:-
[SGLStorage.sol#L77-L81](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L77-L81)

```solidity
83:    event LogAddAsset(
84:        address indexed from,
85:        address indexed to,
86:        uint256 share,
87:        uint256 fraction
88:    );
```
#### **Context**:-
[SGLStorage.sol#L83-L88](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L83-L88)

```solidity
90:    event LogRemoveCollateral(
91:        address indexed from,
92:        address indexed to,
93:        uint256 share
94:    );
```
#### **Context**:-
[SGLStorage.sol#L90-L94](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L90-L94)

```solidity
96:    event LogRemoveAsset(
97:        address indexed from,
98:        address indexed to,
99:        uint256 share,
100:        uint256 fraction
101:    );
```
#### **Context**:-
[SGLStorage.sol#L96-L101](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L96-L101)

```solidity
103:    event LogBorrow(
104:        address indexed from,
105:        address indexed to,
106:        uint256 amount,
107:        uint256 feeAmount,
108:        uint256 part
109:    );
```
#### **Context**:-
[SGLStorage.sol#L103-L109](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L103-L109)

```solidity
111:    event LogRepay(
112:        address indexed from,
113:        address indexed to,
114:        uint256 amount,
115:        uint256 part
116:    );
```
#### **Context**:-
[SGLStorage.sol#L111-L116](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L111-L116)

```solidity
118:    event LogWithdrawFees(address indexed feeTo, uint256 feesEarnedFraction);
```
#### **Context**:-
[SGLStorage.sol#L118](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L118)

```solidity
120:    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);
```
#### **Context**:-
[SGLStorage.sol#L120](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L120)

```solidity
122:    event MinimumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L122](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L122)

```solidity
124:    event MaximumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L124](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L124)

```solidity
126:    event MinimumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L126](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L126)

```solidity
128:    event MaximumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L128](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L128)

```solidity
130:    event InterestElasticityUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L130](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L130)

```solidity
132:    event LqCollateralizationRateUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L132](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L132)

```solidity
134:    event OrderBookLiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[SGLStorage.sol#L134](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L134)

```solidity
136:    event BidExecutionSwapperUpdated(address newAddress);
```
#### **Context**:-
[SGLStorage.sol#L136](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L136)

```solidity
138:    event UsdoSwapperUpdated(address newAddress);
```
#### **Context**:-
[SGLStorage.sol#L138](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L138)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
102:    event Liquidated(
103:        address liquidator,
104:        address[] users,
105:        uint256 liquidatorReward,
106:        uint256 protocolReward,
107:        uint256 repayedAmount,
108:        uint256 collateralShareRemoved
109:    );
```
#### **Context**:-
[Market.sol#L102-L109](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L102-L109)

```solidity
113:    event LiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```
#### **Context**:-
[Market.sol#L113](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L113)

```solidity
File:tapiocaz-audit/contracts/TapiocaWrapper.sol
```

```solidity
51:    event SetFees(uint256 _newFee);
```
#### **Context**:-
[TapiocaWrapper.sol#L51](https://github.com/Tapioca-DAO/contracts/TapiocaWrapper.sol#L51)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```

```solidity
331:    event ApprovalForAll(address owner, address operator, bool approved);
```
#### **Context**:-
[MagnetarV2Storage.sol#L331](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L331)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
52:    event RewardTokens(uint256 _count);
```
#### **Context**:-
[BalancerStrategy.sol#L52](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L52)

## **NC-31** | Unused Mapping
### **Description** :-
Consider removing unused mapping or moving it to a higher level contract.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol
```

```solidity
49:    mapping(address => mapping(bytes32 => uint256)) internal _yieldBoxShares;
```
#### **Context**:-
[SGLStorage.sol#L49](https://github.com/Tapioca-DAO/contracts/markets/singularity/SGLStorage.sol#L49)

```solidity
File:tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol
```

```solidity
29:    mapping(address => mapping(address => bool)) public isApprovedForAll;
```
#### **Context**:-
[MagnetarV2Storage.sol#L29](https://github.com/Tapioca-DAO/contracts/Magnetar/MagnetarV2Storage.sol#L29)

## **NC-32** | Emitting storage values instead of the memory one.
### **Description** :-
Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. We can avoid unecessary SLOADs by caching storage values that were previously accessed and emitting those cached values.

#### <ins>Proof Of Concept</ins>


```solidity
File:tapioca-bar-audit/contracts/Penrose.sol
```

```solidity
274:        emit PausedUpdated(paused, val);
```
#### **Context**:-
[Penrose.sol#L274](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L274)

```solidity
283:        emit ConservatorUpdated(conservator, _conservator);
```
#### **Context**:-
[Penrose.sol#L283](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L283)

```solidity
310:        emit UsdoTokenUpdated(_usdoToken, usdoAssetId);
```
#### **Context**:-
[Penrose.sol#L310](https://github.com/Tapioca-DAO/contracts/Penrose.sol#L310)

```solidity
File:tapioca-bar-audit/contracts/markets/Market.sol
```

```solidity
144:        emit LogBorrowingFee(borrowOpeningFee, _val);
```
#### **Context**:-
[Market.sol#L144](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L144)

```solidity
152:        emit LogBorrowCapUpdated(totalBorrowCap, _cap);
```
#### **Context**:-
[Market.sol#L152](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L152)

```solidity
248:        emit PausedUpdated(paused, val);
```
#### **Context**:-
[Market.sol#L248](https://github.com/Tapioca-DAO/contracts/markets/Market.sol#L248)

```solidity
File:tap-token-audit/contracts/tokens/TapOFT.sol
```

```solidity
143:        emit GovernanceChainIdentifierUpdated(
144:            governanceChainIdentifier,
145:            _identifier
146:        );
```
#### **Context**:-
[TapOFT.sol#L143-L146](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L143-L146)

```solidity
154:        emit PausedUpdated(paused, val);
```
#### **Context**:-
[TapOFT.sol#L154](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L154)

```solidity
162:        emit MinterUpdated(minter, _minter);
```
#### **Context**:-
[TapOFT.sol#L162](https://github.com/Tapioca-DAO/contracts/tokens/TapOFT.sol#L162)

```solidity
File:tap-token-audit/contracts/option-airdrop/AirdropBroker.sol
```

```solidity
293:        emit NewEpoch(epoch, epochTAPValuation);
```
#### **Context**:-
[AirdropBroker.sol#L293](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L293)

```solidity
293:        emit NewEpoch(epoch, epochTAPValuation);
```
#### **Context**:-
[AirdropBroker.sol#L293](https://github.com/Tapioca-DAO/contracts/option-airdrop/AirdropBroker.sol#L293)

```solidity
File:tap-token-audit/contracts/options/TapiocaOptionBroker.sol
```

```solidity
285:        emit Participate(
286:            epoch,
287:            lock.sglAssetID,
288:            pool.totalDeposited,
289:            lock,
290:            target
291:        );
```
#### **Context**:-
[TapiocaOptionBroker.sol#L285-L291](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L285-L291)

```solidity
344:        emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L344](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L344)

```solidity
431:        emit NewEpoch(epoch, epochTAP, epochTAPValuation);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L431](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L431)

```solidity
431:        emit NewEpoch(epoch, epochTAP, epochTAPValuation);
```
#### **Context**:-
[TapiocaOptionBroker.sol#L431](https://github.com/Tapioca-DAO/contracts/options/TapiocaOptionBroker.sol#L431)

```solidity
File:tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol
```

```solidity
62:        emit PoolFee(poolFee, _newFee);
```
#### **Context**:-
[UniswapV3Swapper.sol#L62](https://github.com/Tapioca-DAO/contracts/Swapper/UniswapV3Swapper.sol#L62)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol
```

```solidity
91:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[YearnStrategy.sol#L91](https://github.com/Tapioca-DAO/contracts/yearn/YearnStrategy.sol#L91)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol
```

```solidity
90:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[CompoundStrategy.sol#L90](https://github.com/Tapioca-DAO/contracts/compound/CompoundStrategy.sol#L90)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol
```

```solidity
94:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[LidoEthStrategy.sol#L94](https://github.com/Tapioca-DAO/contracts/lido/LidoEthStrategy.sol#L94)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol
```

```solidity
126:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[TricryptoNativeStrategy.sol#L126](https://github.com/Tapioca-DAO/contracts/curve/TricryptoNativeStrategy.sol#L126)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol
```

```solidity
135:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[TricryptoLPStrategy.sol#L135](https://github.com/Tapioca-DAO/contracts/curve/TricryptoLPStrategy.sol#L135)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol
```

```solidity
143:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[StargateStrategy.sol#L143](https://github.com/Tapioca-DAO/contracts/stargate/StargateStrategy.sol#L143)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol
```

```solidity
123:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[AaveStrategy.sol#L123](https://github.com/Tapioca-DAO/contracts/aave/AaveStrategy.sol#L123)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol
```

```solidity
110:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[BalancerStrategy.sol#L110](https://github.com/Tapioca-DAO/contracts/balancer/BalancerStrategy.sol#L110)

```solidity
File:tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol
```

```solidity
164:        emit DepositThreshold(depositThreshold, amount);
```
#### **Context**:-
[ConvexTricryptoStrategy.sol#L164](https://github.com/Tapioca-DAO/contracts/convex/ConvexTricryptoStrategy.sol#L164)