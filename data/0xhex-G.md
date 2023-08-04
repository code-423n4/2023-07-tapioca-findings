# Gas Optimization

# Summary

| Number | Optimization Details                                                                  | Context |
| :----: | :------------------------------------------------------------------------------------ | :-----: |
| [G-01] | abi.encode() is less efficient than abi.encodePacked()                                |   35    |
| [G-02] | Public Functions To External To save gas                                              |   80    |
| [G-03] | Use calldata instead of memory for function arguments that do not get mutated         |    5    |
| [G-04] | Emitting storage values instead of the memory one                                     |    7    |
| [G-05] | Sort Solidity operations using short-circuit mode                                     |   10    |
| [G-06] | Use `assembly` to emit events                                                         |   12    |
| [G-07] | Bytes constants are more efficient than String constants                              |    1    |
| [G-08] | Use `selfbalance()` instead of address(this).balance                                  |    7    |
| [G-09] | Use nested if statements instead of &&                                                |    9    |
| [G-10] | Using `ERC721A` instead of `ERC721` for more gas-efficient                            |    6    |
| [G-11] | Use hardcode address instead `address(this)`                                          |   16    |
| [G-12] | Can make the variable outside the loop to save gas                                    |    4    |
| [G-13] | Use assembly to check for address(0)                                                  |   14    |
| [G-14] | Do not calculate constants                                                            |   11    |
| [G-15] | Using > 0 costs more gas than != 0 when used on a uint in a require() statement       |   22    |
| [G-16] | Require() or Revert() statement should be used sorted from cheapest to most expensive |    2    |
| [G-17] | Use bitmap to save gas                                                                |    5    |

## [G-01] abi.encode() is less efficient than abi.encodePacked()

```solidity
file:  contracts/tokens/BaseTapOFT.sol

96     bytes memory lzPayload = abi.encode(

172      bytes memory lzPayload = abi.encode(

266   bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L96

```solidity
file:    contracts/markets/MarketERC20.sol
264      abi.encode(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L264

```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol

39     bytes memory lzPayload = abi.encode(

72     bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L39

```solidity
file:   contracts/usd0/modules/USDOMarketModule.sol
40       bytes memory lzPayload = abi.encode(

78         bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L40

```solidity
file: contracts/usd0/modules/USDOOptionsModule.sol
32     bytes memory lzPayload = abi.encode(

75   bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L32

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

209    string(abi.encode(i))

293    returnData: abi.encode(amountOut, shareOut)

323    returnData: abi.encode(part, share)

382   returnData: abi.encode(fraction)

399    returnData: abi.encode(amount)

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L209

```solidity
file:   contracts/Magnetar/modules/MagnetarMarketModule.sol

191    bytes memory withdrawAssetBytes = abi.encode(

592    bytes memory withdrawAssetBytes = abi.encode(

653    bytes memory withdrawCollateralBytes = abi.encode(

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L191

```solidity
file:  contracts/Swapper/UniswapV2Swapper.sol

53   return abi.encode(block.timestamp + 1 hours);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L53

```solidity
file:  contracts/Swapper/UniswapV3Swapper.sol

75    return abi.encode(block.timestamp + 1 hours);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol#L75

```solidity
file:  master/contracts/balancer/BalancerStrategy.sol

169   joinPoolRequest.userData = abi.encode(1, maxAmountsIn);

179   joinPoolRequest.userData = abi.encode(2, bptOut, uint256(index));

238      exitRequest.userData = abi.encode(

255    exitRequest.userData = abi.encode(0, bptIn, index);

288    exitRequest.userData = abi.encode(0, lpBalance, index);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L169

```solidity
file:  contracts/glp/GlpStrategy.sol

329    abi.encode(gmxAmount)

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L329

```solidity
file:  contracts/Balancer.sol

143     ercData = abi.encode(

316       dstNativeAddr: abi.encode(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L143

```solidity
file: contracts/tOFT/modules/BaseTOFTLeverageModule.sol

56    bytes memory lzPayload = abi.encode(

89    bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L56

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

56   bytes memory lzPayload = abi.encode(

105   bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L56

```solidity
file:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol

47     bytes memory lzPayload = abi.encode(

90   bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L47

```solidity
file: contracts/tOFT/modules/BaseTOFTStrategyModule.sol

60   bytes memory lzPayload = abi.encode(

102  bytes memory lzPayload = abi.encode(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L60

```solidity
file:  master/contracts/YieldBoxPermit.sol

53    bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));

82   bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L53

## [G-02] Public Functions To External To save gas

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

```solidity
file:  contracts/usd0/BaseUSDO.sol

143   function decimals() public pure override returns (uint8) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L143

```solidity
file:  contracts/NativeTokenFactory.sol

56   function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {

73  function claimOwnership(uint256 tokenId) public {

109    function mint(uint256 tokenId, address to, uint256 amount) public onlyOwner(tokenId) {

116    function burn(uint256 tokenId, address from, uint256 amount) public allowed(from, tokenId) {

127    function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner(tokenId) {

138   function batchBurn(uint256 tokenId, address[] calldata froms, uint256[] calldata amounts) public {

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L56

```solidity
file:  contracts/compound/CompoundStrategy.sol

80   function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L80

```solidity
file:  contracts/curve/TricryptoNativeStrategy.sol

103   function compoundAmount() public returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L103

```solidity
file:   contracts/markets/MarketERC20.sol

114    function totalSupply() public view virtual override returns (uint256) {}

135        function transfer(
        address to,
        uint256 amount
    ) public virtual returns (bool)

159       function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public virtual returns (bool)

201       function approveBorrow(
        address spender,
        uint256 amount
    ) public returns (bool)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L114

```solidity
file:  contracts/governance/twTAP.sol

155       function getParticipation(
        uint _tokenId
    ) public view returns (Participation memory participant)

397   function advanceWeek(uint256 _limit) public

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L155-L157

```solidity
file:   contracts/option-airdrop/aoTAP.sol

68        function tokenURI(
        uint256 _tokenId
    ) public view override returns (string memory)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L68-L70

```solidity
file:   contracts/options/oTAP.sol
54        function tokenURI(
        uint256 _tokenId
    ) public view override returns (string memory)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L54-L56

```solidity
file:   contracts/tokens/TapOFT.sol
168    function decimals() public pure override returns (uint8)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L168

```solidity
file:  contracts/Penrose.sol

209    function bigBangMarkets() public view returns (address[] memory markets)

214    function singularityMasterContractLength() public view returns (uint256)

219     function bigBangMasterContractLength() public view returns (uint256)

232       function withdrawAllMarketFees(
        IMarket[] calldata markets_,
        ISwapper[] calldata swappers_,
        IPenrose.SwapData[] calldata swapData_
    ) public notPaused

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L209

```solidity
file:   contracts/markets/Market.sol

256        function computeClosingFactor(
        uint256 borrowPart,
        uint256 collateralPartInAsset,
        uint256 borrowPartDecimals,
        uint256 collateralPartDecimals,
        uint256 ratesPrecision
    ) public view returns (uint256)

356       function computeLiquidatorReward(
        address user,
        uint256 _exchangeRate
    ) public view returns (uint256)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L256-L262

```solidity
file:   contracts/markets/bigBang/BigBang.sol

242         function borrow(
        address from,
        address to,
        uint256 amount
    ) public notPaused solvent(from) returns (uint256 part, uint256 share)

263       function repay(
        address from,
        address to,
        bool,
        uint256 part
    ) public notPaused allowedBorrow(from, part) returns (uint256 amount)

282       function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public allowedBorrow(from, share) notPaused

296       function removeCollateral(
        address from,
        address to,
        uint256 share
    ) public notPaused solvent(from) allowedBorrow(from, share)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L242-L246

```solidity
file:   contracts/markets/singularity/SGLBorrow.sol

21       function borrow(
        address from,
        address to,
        uint256 amount
    ) public notPaused solvent(from) returns (uint256 part, uint256 share)

45       function repay(
        address from,
        address to,
        bool skim,
        uint256 part
    ) public notPaused allowedBorrow(from, part) returns (uint256 amount)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLBorrow.sol#L21-L25

```solidity
file:   contracts/markets/singularity/SGLCollateral.sol

21       function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public notPaused allowedBorrow(from, share)

35       function removeCollateral(
        address from,
        address to,
        uint256 share
    ) public notPaused solvent(from) allowedBorrow(from, share)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCollateral.sol#L21-L27

```solidity
file:  contracts/markets/singularity/SGLCommon.sol

17     function accrue() public {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L17

```solidity
file:  contracts/markets/singularity/SGLStorage.sol

154    function symbol() public view returns (string memory) {

191    function totalSupply() public view override returns (uint256) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L154

```solidity
file:   contracts/markets/singularity/Singularity.sol

204       function addAsset(
        address from,
        address to,
        bool skim,
        uint256 share
    ) public notPaused allowedLend(from, share) returns (uint256 fraction)

219       function removeAsset(
        address from,
        address to,
        uint256 fraction
    ) public notPaused returns (uint256 share)

235       function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public

277       function borrow(
        address from,
        address to,
        uint256 amount
    ) public returns (uint256 part, uint256 share)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L204-L209

```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol

93    function multiHop(bytes memory _payload) public {

133       function leverageUp(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L93

```solidity
file:  contracts/usd0/modules/USDOMarketModule.sol

104    function remove(bytes memory _payload) public {

134       function lend(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L104

```solidity
file:  contracts/usd0/modules/USDOOptionsModule.sol

103   function sendFromDestination(bytes memory _payload) public {

138       function exercise(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {

206       function exerciseInternal(
        address from,
        uint256 oTAPTokenID,
        address paymentToken,
        uint256 tapAmount,
        address target,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData
            memory tapSendData,
        ICommonData.IApproval[] memory approvals
    ) public {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L103

```solidity
file:   contracts/Magnetar/MagnetarV2.sol

76       function getCollateralAmountForShare(
        IMarket market,
        uint256 share
    ) public view returns (uint256 amount)

89       function getCollateralSharesForBorrowPart(
        IMarket market,
        uint256 borrowPart,
        uint256 liquidationMultiplierPrecision,
        uint256 exchangeRatePrecision
    ) public view returns (uint256 collateralShares) {

117       function getAmountForBorrowPart(
        IMarket market,
        uint256 borrowPart
    ) public view returns (uint256 amount) {

133       function getBorrowPartForAmount(
        IMarket market,
        uint256 amount
    ) public view returns (uint256 part) {

150       function getAmountForAssetFraction(
        ISingularity singularity,
        uint256 fraction
    ) public view returns (uint256 amount) {

171       function getFractionForAmount(
        ISingularity singularity,
        uint256 amount
    ) public view returns (uint256 fraction) {

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L76-L79

```solidity
file:  contracts/Multicall/Multicall3.sol

41       function multicall(
        Call[] calldata calls
    ) public payable returns (Result[] memory returnData) {

63       function multicallValue(
        CallValue[] calldata calls
    ) public payable returns (Result[] memory returnData) {
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L41-L43

```solidity
file:  contracts/glp/GlpStrategy.sol

104    function harvestGmx(uint256 priceNum, uint256 priceDenom) public onlyOwner {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L104

```solidity
file:  contracts/lido/LidoEthStrategy.sol

84    function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L84

```solidity
file:  contracts/yearn/YearnStrategy.sol

81    function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L81

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.sol

111   function multiHop(bytes memory _payload) public {

148       function leverageDown(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {

```

## [G-03] Use calldata instead of memory for function arguments that do not get mutated

When you specify a data location as memory, that value will be copied into memory. When you specify the location as calldata, the value will stay static within calldata. If the value is a large, complex type, using memory may result in extra memory expansion costs.

```solidity
File: tap-token-audit/tree/master/contracts/governance/twTAP.sol
363        IERC20[] memory _rewardTokens
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L363

```solidity
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
166        address[] memory rewardTokens,
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/Penrose.sol
426        bytes[] memory data,
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol
223        ICommonData.IApproval[] memory approvals
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

```solidity
File: tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
163        ICommonData.IApproval[] memory approvals
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol

## [G-04] Emitting storage values instead of the memory one

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

103          totalSingularityPoolWeights = _computeSGLPoolWeights();
        emit UpdateTotalSingularityPoolWeights(totalSingularityPoolWeights);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L103-L104

```solidity
file:  contracts/option-airdrop/AirdropBroker.sol

293    emit NewEpoch(epoch, epochTAPValuation);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L293

## [G-05] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
 f(x) || g(y)
 f(x) && g(y)
```

```solidity
file: /contracts/TapiocaWrapper.sol

121        if (_revertOnFailure && !success) {

144        if (_call[i].revertOnFailure && !success) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L121

```solidity
file: /contracts/governance/twTAP.sol

473        require(
            msg.sender == tokenOwner ||
                _to == tokenOwner ||
                isApprovedForAll(tokenOwner, msg.sender) ||
                getApproved(_tokenId) == msg.sender,
            "twTAP: cannot claim"
479        );

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L473-L479

```solidity
file: /contracts/YieldBox.sol

488        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

459        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L448

```solidity
file: /contracts/Magnetar/modules/MagnetarMarketModule.sol

579            address removeAssetTo = removeAndRepayData
                .assetWithdrawData
                .withdraw || removeAndRepayData.repayAssetOnBB
                ? address(this)
583                : user;

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L579-L583

```solidity
file: /contracts/convex/ConvexTricryptoStrategy.sol

377            success && (data.length == 0 || abi.decode(data, (bool))),

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L377

```solidity
file: /contracts/Penrose.sol

438            require(
                isSingularityMasterContractRegistered[
                    masterContractOf[mc[i]]
                ] || isBigBangMasterContractRegistered[masterContractOf[mc[i]]],
                "Penrose: MC not registered"
443            );

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L438-L443

## [G-06] Use `assembly` to emit events

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs. Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

```solidity
file: /contracts/tOFT/mTapiocaOFT.sol

120        emit ConnectedChainStatusUpdated(
            _chain,
            connectedChains[_chain],
            _status
124        );


135        emit BalancerStatusUpdated(_balancer, balancers[_balancer], _status);

151        emit Rebalancing(msg.sender, _amount, _isNative);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L120-L124

```solidity
file: /contracts/TapiocaWrapper.sol

177        emit CreateOFT(iOFT, _erc20);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L177

```solidity
file: /contracts/Balancer.sol

211        emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L211

```solidity
file: /contracts/Balancer.sol

256        emit RebalanceAmountUpdated(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L256

```solidity
file: /contracts/options/oTAP.sol

122        emit Burn(msg.sender, _tokenId, options[_tokenId]);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L122

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

328        emit UnregisterSingularity(address(singularity), sglAssetID);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L328

```solidity
file: /contracts/Swapper/UniswapV3Swapper.sol

62        emit PoolFee(poolFee, _newFee);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L62

```solidity
file: /contracts/compound/CompoundStrategy.sol

161        emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol#L161

```solidity
file: /contracts/balancer/BalancerStrategy.sol

110        emit DepositThreshold(depositThreshold, amount);

```

110https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol#L110

```solidity
file: /contracts/NativeTokenFactory.sol

99        emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L99

## [G-07] Bytes constants are more efficient than String constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

```solidity
file: /contracts/glp/GlpStrategy.sol
26    string public constant override name = "sGLP";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L26

## [G-08] Use `selfbalance()` instead of address(this).balance

Using selfbalance() instead of address(this).balance can save gas in Solidity because selfbalance() is a built-in function that returns the balance of the current contract directly as a uint256 value, without the need to create a new address object.
On the other hand, using address(this).balance to get the balance of the current contract creates a new address object, which consumes more gas and can make the code less efficient.
By using selfbalance(), you can reduce the amount of gas consumed by your Solidity code and make your contracts more efficient.

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

225            address(this).balance

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L225

```solidity
file: /contracts/Balancer.sol

286        if (address(this).balance < _amount) revert ExceedsBalance();

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L286

```solidity
file: /contracts/tOFT/modules/BaseTOFTOptionsModule.sol

142        ISendFrom(address(this)).sendFrom{value: address(this).balance}(

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142

```solidity
file: /contracts/tOFT/modules/BaseTOFTLeverageModule.sol

230            value: address(this).balance

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L230

```solidity
file: /contracts/TapiocaDeployer/TapiocaDeployer.sol

29            address(this).balance >= amount,

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol#L29

```solidity
file: /contracts/Magnetar/modules/MagnetarMarketModule.sol

751        uint256 gas = msg.value > 0 ? msg.value : address(this).balance;

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L751

```solidity
file: /contracts/stargate/StargateStrategy.sol

206        result = address(this).balance;

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/stargate/StargateStrategy.sol#L206

## [G-09] Use nested if statements instead of &&

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol


370          if (!success && !_forwardRevert) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L370

```solidity

1046         if (!success && !allowFailure) {
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1046

```solidity
File:  tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol
402        if (lendAmount == 0 && depositData.deposit) {
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
171    if (daysPassed && balanceOfStkAave > 0) {
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File:  tapiocaz-audit/tree/master/contracts/Balancer.sol


226        if (!isNative && _ercData.length == 0) revert PoolInfoRequired();
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L226

```solidity
File: tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol


121    if (_revertOnFailure && !success) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L121

```solidity
File: tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol
412       if (!success && !_forwardRevert) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L412

```solidity
File: Yieldbox/tree/master/contracts/BoringMath.sol
27        if (roundUp && (result * div) / mul < value) {
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L27

```solidity
File:  Yieldbox/tree/master/contracts/YieldBoxRebase.sol
35    if (roundUp && (share * totalAmount) / totalShares_ < amount) {

58    if (roundUp && (amount * totalShares_) / totalAmount < share) {
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol#L35

## [G-10] Using `ERC721A` instead of `ERC721` for more gas-efficient

```solidity
file: /contracts/options/oTAP.sol

30        contract OTAP is ERC721, ERC721Permit, BaseBoringBatchable {

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L30

```solidity
file: /contracts/option-airdrop/aoTAP.sol

32       contract AOTAP is ERC721, ERC721Permit, BaseBoringBatchable, BoringOwnable {

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/aoTAP.sol#L32

```solidit
file: /contracts/YieldBox.sol

175        require(asset.tokenType == TokenType.ERC721, "YieldBox: not ERC721");

235        if (asset.tokenType == TokenType.ERC721) {

448        if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L175

```solidity
file: /contracts/governance/twTAP.sol

584    ) public view virtual override(ONFT721, ERC721) returns (bool) {

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L584

## [G-11] Use hardcode address instead `address(this)`

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.
References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

```solidity
file: /contracts/tokens/LTap.sol

42        tapToken.transferFrom(msg.sender, address(this), amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/LTap.sol#L42

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

186        yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L186

```solidity
file: /contracts/governance/twTAP.sol

260        tapOFT.transferFrom(msg.sender, address(this), _amount);

448        rewardToken.safeTransferFrom(msg.sender, address(this), _amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L260

```solidity
file: /contracts/Balancer.sol

305        if (erc20.balanceOf(address(this)) < _amount) revert ExceedsBalance();

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L305

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

143        uint256 balanceBefore = balanceOf(address(this));

146         _creditTo(_srcChainId, address(this), amount);

149        uint256 balanceAfter = balanceOf(address(this));

165         IERC20(address(this)).safeTransfer(onBehalfOf, amount);

206        _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L143

```solidity
file: /contracts/tOFT/modules/BaseTOFTMarketModule.sol

152        uint256 balanceBefore = balanceOf(address(this));

158        uint256 balanceAfter = balanceOf(address(this));

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L152

```solidity
file: /contracts/curve/TricryptoNativeStrategy.sol

152        uint256 claimable = lpGauge.claimable_tokens(address(this));

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoNativeStrategy.sol#L152

## [G-12] Can make the variable outside the loop to save gas

Consider making the stack variables before the loop which gonna save gas and the variable will re-initialized per iteration, this will consume extra gas.

```solidity
file: /contracts/governance/twTAP.sol

235            uint256 net = cur.totalDistPerVote[i] - prev.totalDistPerVote[i];

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L235

```solidity
file: /contracts/governance/twTAP.sol

508                uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L508

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

566                uint256 currentPoolWeight = sglPools[i].poolWeight;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L566

```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

108            address user = users[i];

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L108

## [G-13] Use assembly to check for address(0)

```solidity
file: /contracts/usd0/BaseUSDO.sol

354        if (module == address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L354

```solidity
file: /contracts/glp/GlpStrategy.sol

62        require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router");

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L62

```solidity
file: /contracts/NativeTokenFactory.sol

64            pendingOwner[tokenId] = address(0);

93        _registerAsset(TokenType.Native, address(0), NO_STRATEGY, tokenId);

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L64

```solidity
file: /contracts/YieldBoxURIBuilder.sol

107         : abi.encodePacked(',"totalSupply":', totalSupply.toString(), ',"fixedSupply":', owner == address(0) ? "true" : "false")

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxURIBuilder.sol#L107

```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

40        if (address(liquidationQueue) != address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L40

```solidity
file: /contracts/usd0/BaseUSDO.sol

106         require(_conservator != address(0), "USDO: address not valid");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L106

```solidity
file: /contracts/markets/singularity/Singularity.sol

581        if (address(_liquidationQueue) != address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L581

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

### these are missed from Automated the Automated get only 5 instences but there are 11 of them

```solidity
file:  contracts/governance/twTAP.sol

82       uint256 constant dMAX = 100 * 1e4; // 10% - 100% voting power multiplier

83       uint256 constant dMIN = 10 * 1e4;

95    uint256 constant DIST_PRECISION = 2 ** 128;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L82

```solidity
file:  contracts/option-airdrop/AirdropBroker.sol

69    uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4; // 50%

85   uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;

93   uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L69

```solidity
file: contracts/options/TapiocaOptionBroker.sol

76    uint256 constant dMAX = 50 * 1e4; // 5% - 50% discount

77    uint256 constant dMIN = 5 * 1e4;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L76

```solidity
file:  contracts/tokens/TapOFT.sol

41   uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO

42   uint256 public dso_supply = 53_313_405 * 1e18;

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L41

```solidity
file: contracts/oracle/implementations/ARBTriCryptoOracle.sol

41   uint256 public constant A0 = 2 * 3 ** 3 * 10_000;

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L41

## [G‑15] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance.

```solidity
file:

12   require(denominator > 0);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol#L12

```solidity
file:

68   require(_duration > 0, "Vesting: no vesting");

```

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

203    require(cachedEpoch > 0, "adb: Airdrop not started");

392     require(_eligibleAmount > 0, "adb: Not eligible");

467    require(_eligibleAmount > 0, "adb: Not eligible");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L203

```solidity
file:  master/contracts/options/TapiocaOptionBroker.sol

419   require(singularities.length > 0, "tOB: No active singularities");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L419

```solidity
file:   master/contracts/options/TapiocaOptionLiquidityProvision.sol

177     require(_lockDuration > 0, "tOLP: lock duration must be > 0");

178     require(_amount > 0, "tOLP: amount must be > 0");

181     require(sglAssetID > 0, "tOLP: singularity not active");

281      require(assetID > 0, "tOLP: invalid asset ID");

301    require(sglAssetID > 0, "tOLP: not registered");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L177

```solidity
file:  master/contracts/tokens/BaseTapOFT.sol

104    require(duration > 0, "TapOFT: Small duration");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L104

```solidity
file:  contracts/markets/Market.sol

344   require(rate > 0, "Market: invalid rate");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L344

```solidity
file:   contracts/markets/singularity/SGLLiquidation.sol

397    require(liquidatedCount > 0, "SGL: no users found");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L397

```solidity
file:  master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol

56   require(amount > 0, "TOFT_0");

98    require(amount > 0, "TOFT_0");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L56

## [G-16] Require() or Revert() statement should be used sorted from cheapest to most expensive

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first,
the function is able to revert before wasting a Gcoldsload (2100 gas\*) in a function that may ultimately revert in the unhappy case

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

187        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");

190        require(tapAmount >= 1e18, "tOB: Too low");

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L187

## [G-17] Use bitmap to save gas

```soldiity
file: /contracts/usd0/BaseUSDOStorage.sol

75       allowedMinter[chain][msg.sender] = true;

76        allowedBurner[chain][msg.sender] = true;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol#L75-L76

```solidity
file: /contracts/tOFT/mTapiocaOFT.sol

77            connectedChains[_hostChainID] = true;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L77

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

147            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L147

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

456        userParticipation[tokenIDToAddress][3] = true;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L456