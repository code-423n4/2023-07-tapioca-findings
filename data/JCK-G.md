

#  Gas Optimizations

| Number | 	Issue | nstances |
|--------|--------|----------|
|[G-01]| Use assembly in place of abi.decode to extract calldata values more efficiently  | 70 |
|[G-02]| bytes constants are more eficient than string constans| 2 |
|[G-03]| Save loop calls  | 9 |
|[G-04]| Use functions instead of modifiers  | 6 |
|[G-05]| Caching a variable that is used once just wastes Gas  | 3 |
|[G-06]| in for loop  Writing data to the storage of the Ethereum blockchain is more expensive than performing read-only operations in memory.  | 2 |
|[G-07]| Use hardcode address instead address(this)  | 229 |
|[G-08]| Emitting storage values instead of the memory one | 2 |
|[G-09]| Optimizing check order for cost efficient function execution  | 2 |
|[G-10]| Use calldata instead of memory for function parameters  | 14 |
|[G-11]| Nested if is cheaper than single statement  | 12 |
|[G-12]| Cache storage values in memory to minimize SLOADs  | 17 |
|[G-13]| Use the existing Local variable/global variable when equal to a state variable to avoid reading from state | 4 |
|[G-14]| Use assembly to emit events  | 205 |
|[G-15]| Use assembly to validate msg.sender  | 17 |
|[G-16]| Cache calldata/memory pointers for complex types to avoid offset calculations  | 8 |
|[G-17]| Forgo internal function to save 1 STATICCALL  | 5 |
|[G-18]| A modifier used only once and not being inherited should be inlined to save gas  | 5 |
|[G-19]| Call environment variables directly instead of caching them  | 1 |
|[G-20]| a modifier not inherited and not used by they contract should be remove to save gas   | 2 |
|[G-21]| Use assembly for loops  | 9 |
|[G-22]| Use assembly to write address storage values  | 9 |
|[G-23]| Using > 0 costs more gas than != 0 when used on a uint in a require() statement  | 16 |
|[G-24]| Use assembly to check for address(0)  | 57 |
|[G-25]| Don’t initialize variables with default value  | 61 |
|[G-26]| Do not calculate constants  | 11 |
|[G-27]| Amounts should be checked for 0 before calling a transfer  | 18 |
|[G-28]| abi.encode() is less efficient than abi.encodePacked()  | 38 |
|[G-29]| Using XOR (^) and OR  bitwise equivalents  | 2 |
|[G-30]| Using a positive conditional flow to save a NOT opcode  | 55 |
|[G-31]| Make 3 event parameters indexed when possible  | 26 |
|[G-32]| Assigning keccak operations to constant variables results in extra gas costs  | 5 |
|[G-33]| 10 ** 18 can be changed to 1e18 and save some gas  | 2 |
|[G-34]| With assembly, .call (bool success) transfer can be done gas-optimized  | 6 |
|[G-35]| Use constants instead of type(uintx).max  | 34 |
|[G-36]| Use fixed size bytes array rather than string or bytes[]  | 11 |


## [G-01] Use assembly in place of abi.decode to extract calldata values more efficiently

Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.[G-04] Use assembly in place of abi.decode to extract calldata values more efficiently
Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

413           (uint256 _role, bytes32[] memory _merkleProof) = abi.decode(
            _data,
            (uint256, bytes32[])
        );

445    uint256 _tokenID = abi.decode(_data, (uint256));

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L413-L416

```solidity
file:  contracts/tokens/BaseTapOFT.sol

129           (, , address to, uint256 amount, uint duration) = abi.decode(
            _payload,
            (uint16, address, address, uint256, uint256)
        );

209           ) = abi.decode(
                _payload,
                (
                    uint16,
                    address,
                    address,
                    uint256,
                    IERC20[],
                    IRewardClaimSendFromParams[]
                )
            );

301          ) = abi.decode(
                _payload,
                (uint16, address, address, uint256, LzCallParams)
            );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L129-L132

```solidity
file:   contracts/Penrose.sol

482     return abi.decode(_returnData, (string)); 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L482

```solidity
file:   contracts/markets/Market.sol

382    return abi.decode(_returnData, (string)); 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L382

```solidity
file:    contracts/markets/bigBang/BigBang.sol

112           ) = abi.decode(
                data,
                (
                    IPenrose,
                    IERC20,
                    uint256,
                    IOracle,
                    uint256,
                    uint256,
                    uint256,
                    uint256,
                    uint256
                )
            );

605    minAssetMount = abi.decode(_dexData, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L112-L125

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

339     minAssetAmount = abi.decode(dexData, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L339


```solidity
file:   contracts/markets/singularity/Singularity.sol

75           ) = abi.decode(
                data,
                (
                    address,
                    address,
                    address,
                    address,
                    IPenrose,
                    IERC20,
                    uint256,
                    IERC20,
                    uint256,
                    IOracle,
                    uint256
                )
            );

286    (part, share) = abi.decode(result, (uint256, uint256));

312   amount = abi.decode(result, (uint256));

340   amountOut = abi.decode(result, (uint256));

371   amountOut = abi.decode(result, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L75-L90


```solidity
file:  contracts/usd0/BaseUSDOStorage.sol

97    return abi.decode(_returnData, (string)); /

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDOStorage.sol#L97


```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol

104   ) = abi.decode(

148    ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L104


```solidity
file:  contracts/usd0/modules/USDOMarketModule.sol

111   ) = abi.decode(

148    ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L111


```solidity
file:   contracts/usd0/modules/USDOOptionsModule.sol

111    ) = abi.decode(

152    ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L111

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

233    WrapData memory data = abi.decode(_action.call[4:], (WrapData));

256    ) = abi.decode(

276    YieldBoxDepositData memory data = abi.decode(

296    SGLAddCollateralData memory data = abi.decode(

310    SGLBorrowData memory data = abi.decode(

336    ) = abi.decode(

368    SGLLendData memory data = abi.decode(

385   SGLRepayData memory data = abi.decode(

411   ) = abi.decode(

448   ) = abi.decode(

476    TOFTSendToStrategyData memory data = abi.decode(

502     ) = abi.decode(

529     HelperLendData memory data = abi.decode(

556    ) = abi.decode(

585    HelperMarketRemoveAndRepayAsset memory data = abi.decode(

602     HelperDepositRepayRemoveCollateral memory data = abi.decode(

623   HelperBuyCollateral memory data = abi.decode(

637   HelperSellCollateral memory data = abi.decode(

650   HelperExerciseOption memory data = abi.decode(

662    HelperMultiHopBuy memory data = abi.decode(

678    HelperMultiHopBuy memory data = abi.decode(

694    HelperTOFTRemoveAndRepayAsset memory data = abi.decode(

1032   PermitAllData memory permitData = abi.decode(

1038   PermitData memory permitData = abi.decode(

1086   revert(abi.decode(_returnData, (string)));


```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L233

```solidity
file:   contracts/Magnetar/modules/MagnetarMarketModule.sol

749     ) = abi.decode(withdrawData, (bool, uint16, bytes32, bytes));

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L749


```solidity
file:  contracts/Multicall/Multicall3.sol

102    revert(abi.decode(_returnData, (string))); /

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L102 

```solidity
file:  contracts/Swapper/BaseSwapper.sol

103    success && (data.length == 0 || abi.decode(data, (bool))),

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L103

```solidity
file:  contracts/Swapper/CurveSwapper.sol

63     uint256[] memory tokenIndexes = abi.decode(dexOptions, (uint256[]));

101    uint256[] memory tokenIndexes = abi.decode(data, (uint256[]));

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol#L63


```solidity
file:  contracts/Swapper/UniswapV2Swapper.sol   

150    uint256 deadline = abi.decode(data, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L150


```solidity
file:  contracts/Swapper/UniswapV3Swapper.sol

178    uint256 deadline = abi.decode(data, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol#L178

```solidity
file:  contracts/convex/ConvexTricryptoStrategy.sol

229    ) = abi.decode(

240   ) = abi.decode(

377    success && (data.length == 0 || abi.decode(data, (bool))),

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L229


```solidity
file:   contracts/curve/TricryptoLPStrategy.sol

111    claimable = abi.decode(response, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L111

```solidity
file:   contracts/glp/GlpStrategy.sol

92      uint256 amount = abi.decode(data, (uint256));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L92

```solidity
file:   contracts/Balancer.sol

230     (uint256 _srcPoolId, uint256 _dstPoolId) = abi.decode(

307     (uint256 _srcPoolId, uint256 _dstPoolId) = abi.decode(


```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L230


```solidity
file:  contracts/tOFT/BaseTOFTStorage.sol

77    return abi.decode(_returnData, (string)); 

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFTStorage.sol#L77

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.sol

121   ) = abi.decode(

163   ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L121

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

140   ) = abi.decode(

213   ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L140

```solidity
file:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol

126    ) = abi.decode(

167    ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L126

```solidity
file:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

138    ) = abi.decode(

200    ) = abi.decode(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L138


## [G-02] bytes constants are more eficient than string constans

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol
26    string public constant override name = "sGLP";

27    string public constant override description =
        "Holds staked GLP tokens and compounds the rewards";
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L26



The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

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
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L111

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

126       function borrow(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public payable {

204     function remove(bytes memory _payload) public {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L126-L132



```solidity
file:   contracts/tOFT/modules/BaseTOFTStrategyModule.sol

122       function strategyDeposit(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload,
        IERC20 _erc20
    ) public {

188      function strategyWithdraw(
        uint16 _srcChainId,
        bytes memory _payload
    ) public {


```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L122-L129


```solidity
file:  contracts/YieldBox.sol

168       function depositNFTAsset(
        uint256 assetId,
        address from,
        address to
    ) public allowed(from, assetId) returns (uint256 amountOut, uint256 shareOut) {

303   function transfer(address from, address to, uint256 assetId, uint256 share) public allowed(from, assetId) {

336    function transferMultiple(address from, address[] calldata tos, uint256 assetId, uint256[] calldata shares) public allowed(from, assetId) {

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L168-L172

```solidity
file:   contracts/YieldBoxPermit.sol

42       function permit(
        address owner,
        address spender,
        uint256 assetId,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public virtual {

72       function permitAll(
        address owner,
        address spender,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public virtual {

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L42-L50

## [G-03] Save loop calls

### Instead of calling totalDistPerVote[i] 2 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:

413             for (uint256 i = 0; i < len; ) {
                next.totalDistPerVote[i] += prev.totalDistPerVote[i];
                unchecked {
                    ++i;
                }
            }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L413-L418

### Recommended Mitigation Steps

```solidity

         for (uint256 i = 0; i < len; ) {
     +          uint256 _totalDistPerVote = totalDistPerVote[i]
     -          next.totalDistPerVote[i] += prev.totalDistPerVote[i];
     
     +          next._totalDistPerVote += prev._totalDistPerVote;

                unchecked {
                    ++i;
                }
            }
```

### Instead of calling result[i] 5 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:    contracts/Magnetar/MagnetarV2.sol

973           for (uint256 i = 0; i < len; i++) {
            ISingularity sgl = markets[i];

            result[i].market = _commonInfo(who, IMarket(address(sgl)));

            (uint128 totalAssetElastic, uint128 totalAssetBase) = sgl //
                .totalAsset(); //
            _totalAsset = Rebase(totalAssetElastic, totalAssetBase); //
            result[i].totalAsset = _totalAsset; //
            result[i].userAssetFraction = sgl.balanceOf(who); //

            (
                ISingularity.AccrueInfo memory _accrueInfo,
                uint256 _utilization
            ) = sgl.getInterestDetails();

            result[i].accrueInfo = _accrueInfo;
            result[i].utilization = _utilization;
        }

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L973-L991


### Instead of calling result[i] 8 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

1004           for (uint256 i = 0; i < len; i++) {
            IBigBang bigBang = markets[i];
            result[i].market = _commonInfo(who, IMarket(address(bigBang)));

            (uint64 debtRate, uint64 lastAccrued) = bigBang.accrueInfo();
            _accrueInfo = IBigBang.AccrueInfo(debtRate, lastAccrued);
            result[i].accrueInfo = _accrueInfo;
            result[i].minDebtRate = bigBang.minDebtRate();
            result[i].maxDebtRate = bigBang.maxDebtRate();
            result[i].debtRateAgainstEthMarket = bigBang
                .debtRateAgainstEthMarket();
            result[i].currentDebtRate = bigBang.getDebtRate();

            IPenrose penrose = IPenrose(bigBang.penrose());
            result[i].mainBBMarket = penrose.bigBangEthMarket();
            result[i].mainBBDebtRate = penrose.bigBangEthDebtRate();
        }



```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1004-L1020




### Instead of calling sglPools[i] 2 times in each loop for fetching data, it can be saved as a variable.


```solidity
file:   contracts/options/TapiocaOptionBroker.sol

565               for (uint256 i = 0; i < len; ++i) {
                uint256 currentPoolWeight = sglPools[i].poolWeight;
                uint256 quotaPerSingularity = muldiv(
                    currentPoolWeight,
                    _epochTAP,
                    totalWeights
                );
                singularityGauges[epoch][
                    sglPools[i].sglAssetID
                ] = quotaPerSingularity;
            }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L565-L575

### Instead of calling approvals[i] 9 times in each loop for fetching data, it can be saved as a variable.


```solidity
file:    contracts/tokens/BaseTapOFT.sol

333           for (uint256 i = 0; i < approvals.length; ) {
            try
                IERC20Permit(approvals[i].target).permit(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                )
            {} catch Error(string memory reason) {
                if (!approvals[i].allowFailure) {
                    revert(reason);
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L333-L353

### Instead of calling approvals[i] 25 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:    contracts/usd0/modules/USDOLeverageModule.sol

255      for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L255-L308


### Instead of calling approvals[i] 25 times in each loop for fetching data, it can be saved as a variable.


```solidity
file:    contracts/usd0/modules/USDOMarketModule.sol

244          for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L244-L297

### Instead of calling approvals[i] 25 times in each loop for fetching data, it can be saved as a variable.


```solidity
file:    contracts/usd0/modules/USDOOptionsModule.sol

245           for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }


```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L245-L298


### Instead of calling approvals[i] 27 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:    contracts/tOFT/modules/BaseTOFTLeverageModule.sol
 
285           for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L285-L338


## [G-04] Use functions instead of modifiers

Modifiers:
Modifiers are used to add pre or post-conditions to functions. They are typically applied using the modifier keyword and can be used to enforce access control, check conditions, or perform other checks before or after a function's execution. Modifiers are often used to reduce code duplication and enforce certain rules consistently across multiple functions.

However, using modifiers can lead to increased gas costs, especially when multiple function are applied to a single modifier. Each modifier adds overhead in terms of gas consumption because its code is effectively inserted into the function at the point of the modifier keyword. This results in additional gas usage for each modifier, which can accumulate if multiple modifiers are involved.

Functions:
Functions, on the other hand, are more efficient in terms of gas consumption. When you define a function, its code is written only once and can be called from multiple places within the contract. This reduces gas costs compared to using modifiers since the function's code is not duplicated every time it's called.

By using functions instead of modifiers, you can avoid the gas overhead associated with multiple modifiers and create more gas-efficient smart contracts. If you find that certain logic needs to be applied to multiple functions, you can write a separate function to encapsulate that logic and call it from the functions that require it. This way, you achieve code reusability without incurring unnecessary gas costs.

Let's illustrate the difference between using modifiers and functions with a simple example.

Using Modifiers:

```solidity
pragma solidity ^0.8.0;

contract BankAccountUsingModifiers {
    address public owner;
    uint public balance;

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can perform this action.");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function withdraw(uint amount) public onlyOwner {
        require(amount <= balance, "Insufficient balance.");
        balance -= amount;
        // Perform withdrawal logic
    }

    function transfer(address recipient, uint amount) public onlyOwner {
        require(amount <= balance, "Insufficient balance.");
        balance -= amount;
        // Perform transfer logic
    }
}

```

Using Functions:

```solidity
  
pragma solidity ^0.8.0;

contract BankAccountUsingFunctions {
    address public owner;
    uint public balance;

    constructor() {
        owner = msg.sender;
    }

    function onlyOwner() private view returns (bool) {
        return msg.sender == owner;
    }

    function withdraw(uint amount) public {
        require(onlyOwner(), "Only the owner can perform this action.");
        require(amount <= balance, "Insufficient balance.");
        balance -= amount;
        // Perform withdrawal logic
    }

    function transfer(address recipient, uint amount) public {
        require(onlyOwner(), "Only the owner can perform this action.");
        require(amount <= balance, "Insufficient balance.");
        balance -= amount;
        // Perform transfer logic
    }
}

```
In the first contract (BankAccountUsingModifiers), we used a modifier called onlyOwner to check if the caller is the owner of the bank account. The onlyOwner modifier is applied to both the withdraw and transfer functions to ensure that only the owner can perform these actions. However, using the modifier will result in additional gas costs since the modifier's code is duplicated in both functions.

In the second contract (BankAccountUsingFunctions), we replaced the modifier with a private function called onlyOwner, which checks if the caller is the owner. We then call this function at the beginning of both the withdraw and transfer functions. By doing this, we avoid the gas overhead associated with using modifiers, making the contract more gas-efficient.

### use function instead of  onlyBroker modifier this modifier use by  more then one function 

```solidity
file:  contracts/options/oTAP.sol

39       modifier onlyBroker() {
        require(msg.sender == broker, "OTAP: only onlyBroker");
        _;
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L39-L42


### use function instead of updateTotalSGLPoolWeights modifier this modifier use by   more then one function 


```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

101       modifier updateTotalSGLPoolWeights() {
        _;
        totalSingularityPoolWeights = _computeSGLPoolWeights();
        emit UpdateTotalSingularityPoolWeights(totalSingularityPoolWeights);
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L101-L105

### use function instead of onlyOwner modifier this modifier use by   more then one function 

```solidity
file:  contracts/NativeTokenFactory.sol

45        modifier onlyOwner(uint256 tokenId) {
        require(msg.sender == owner[tokenId], "NTF: caller is not the owner");
        _;
    }

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L45-L48

### use function instead of notPaused modifier this modifier use by   more then one function 


```solidity
file:  contracts/tokens/TapOFT.sol

88       modifier notPaused() {
        require(!paused, "TAP: paused");
        _;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L88-L91

### use function instead of notPaused modifier this modifier use by   more then one function 


```solidity
file:  contracts/usd0/USDO.sol

25   modifier notPaused() {
        require(!paused, "USDO: paused");
        _;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol#L25-L28

### use function instead of onlyValidDestination modifier this modifier use by   more then one function 


```solidity
file:   contracts/Balancer.sol

111       modifier onlyValidDestination(address _srcOft, uint16 _dstChainId) {
        if (connectedOFTs[_srcOft][_dstChainId].dstOft == address(0))
            revert DestinationNotValid();
        _;
    }

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L111-L115



## [G-05] Caching a variable that is used once just wastes Gas


### No need to cache userCollateralShare[user]; as it’s being used once to save gas 

#### saved gas  1293

```solidity
file:   contracts/markets/singularity/SGLLiquidation.sol

70        function _computeAssetAmountToSolvency(
        address user,
        uint256 _exchangeRate
    ) private view returns (uint256) {
        // accrue must have already been called!
        uint256 borrowPart = userBorrowPart[user];
        if (borrowPart == 0) return 0;
        uint256 collateralShare = userCollateralShare[user];

        Rebase memory _totalBorrow = totalBorrow;

        uint256 collateralAmountInAsset = yieldBox.toAmount(
            collateralId,
            (collateralShare *
                (EXCHANGE_RATE_PRECISION / FEE_PRECISION) *
                lqCollateralizationRate),
            false
        ) / _exchangeRate;
        // Obviously it's not `borrowPart` anymore but `borrowAmount`
        borrowPart = (borrowPart * _totalBorrow.elastic) / _totalBorrow.base;

        return
            borrowPart >= collateralAmountInAsset
                ? borrowPart - collateralAmountInAsset
                : 0;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L70-L95


### No need to cache currentWeek() function  in they cure variable but instead of you can directly cache they currentWeek() function to goal variable to save gas 

```solidity
file:   contracts/governance/twTAP.sol

397       function advanceWeek(uint256 _limit) public {
        // TODO: Make whole function unchecked
        uint256 cur = currentWeek();
        uint256 week = lastProcessedWeek;
        uint256 goal = cur;
        unchecked {
            if (goal - week > _limit) {
                goal = week + _limit;
            }
        }
        uint256 len = rewardTokens.length;
        while (week < goal) {
            WeekTotals storage prev = weekTotals[week];
            WeekTotals storage next = weekTotals[++week];
            // TODO: Prove that math is safe
            next.netActiveVotes += prev.netActiveVotes;
            for (uint256 i = 0; i < len; ) {
                next.totalDistPerVote[i] += prev.totalDistPerVote[i];
                unchecked {
                    ++i;
                }
            }
        }
        lastProcessedWeek = goal;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L397-L421

### No need to cache _extractModule(_module); as it’s being used once to save gas 

```solidity
file:  contracts/markets/singularity/Singularity.sol

631       function _executeViewModule(
        Module _module,
        bytes memory _data
    ) private view returns (bytes memory returnData) {
        bool success = true;
        address module = _extractModule(_module);

        (success, returnData) = module.staticcall(_data);
        if (!success) {
            revert(_getRevertMsg(returnData));
        }
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L631-L642


## [G-06] in for loop  Writing data to the storage of the Ethereum blockchain is more expensive than performing read-only operations in memory.

Read-Only Operations in Memory:
Read-only operations involve accessing and retrieving data from the contract's memory without modifying the contract's state. These operations are relatively inexpensive in terms of gas consumption because they do not alter the blockchain's state. They are used when the contract needs to fetch information from its internal state or make decisions based on existing data.

Writing Data to Storage:
On the other hand, writing data to the storage of the Ethereum blockchain is considered an expensive operation. When a smart contract modifies its state variables, it needs to update the data on the blockchain, which requires more computational effort and thus consumes more gas. Writing data to storage involves persisting the new state on the blockchain, which is a fundamental aspect of blockchain's security and immutability.

To save gas when writing smart contracts, it is essential to minimize unnecessary state modifications and consider alternative approaches that reduce the frequency of writing data to the blockchain's storage. Instead, try to keep frequently accessed and temporary data in memory, where read-only operations can be performed more efficiently and with lower gas costs.



### saved gas  56989

###  cache paymentTokenBeneficiary state variable  out side the  loops and require to save gas 

```solidity
file:   contracts/options/TapiocaOptionBroker.sol

479        function collectPaymentTokens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
        require(
            paymentTokenBeneficiary != address(0),
            "tOB: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
        }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L479-L497

### Recommanded code

```slidity
       function collectPaymentTokens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
   +     address  _paymentTokenBeneficiary= paymentTokenBeneficiary;
        require(
   -        paymentTokenBeneficiary != address(0),

   +       _paymentTokenBeneficiary != address(0),

            "tOB: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
   -                 paymentTokenBeneficiary,

   +                _paymentTokenBeneficiary,

                    paymentToken.balanceOf(address(this))
                );
            }
        }


```

### cache singularities state variable  out side the  loops  to save gas 

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

308            for (uint256 i = 0; i < sglLength; i++) {
                // If last element, just pop
                if (i == sglLastIndex) {
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];
                    singularities.pop();
                } else if (
                    _singularities[i] == sglAssetID && i < sglLastIndex
                ) {
                    // If in the middle, copy last element on deleted element, then pop
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];

                    singularities[i] = _singularities[sglLastIndex];
                    singularities.pop();
                    break;
                }
            }
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L308-L325


## [G-07] Use hardcode address instead address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

References:

https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824


##### instences 227

```solidity
file:  contracts/Vesting.sol

157   uint256 availableToken = _token.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L157

```solidity
file:   tap-token-audit/tree/master/contracts/governance/twTAP.sol

260     tapOFT.transferFrom(msg.sender, address(this), _amount);

448     rewardToken.safeTransferFrom(msg.sender, address(this), _amount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L260

```solidity
file:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
379    paymentToken.balanceOf(address(this))

511     address(this),

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L379

```solidity
file:   tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol

230    tOLP.transferFrom(msg.sender, address(this), _tOLPTokenID);

342    tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);

493     paymentToken.balanceOf(address(this))

530        _paymentToken.transferFrom(
            msg.sender,
            address(this),
            discountedPaymentAmount
        );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L230

```solidity
file:  contracts/options/TapiocaOptionLiquidityProvision.sol

186   yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);

241           yieldBox.transfer(
            address(this),
            _to,
            lockPosition.sglAssetID,
            sharesOut
        );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L186

```solidity
file:  contracts/tokens/BaseTapOFT.sol

134   _creditTo(_srcChainId, address(this), amount);

144    _transferFrom(address(this), to, amount);

147    _transferFrom(address(this), to, amount);

232     address(this),

135     IERC20(rewardTokens[i]).balanceOf(address(this)),

312    this.sendFrom{value: address(this).balance}(
                address(this),

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L134 

```solidity
file:  contracts/tokens/LTap.sol

42    tapToken.transferFrom(msg.sender, address(this), amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol#L42

```solidity
file:    contracts/Penrose.sol

514                yieldBox.transfer(
                address(this),
                address(swapper),
                assetId,
                feeShares
            );
536    yieldBox.transfer(address(this), feeTo, assetId, feeShares);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L514-L519

```solidity
file:   contracts/markets/bigBang/BigBang.sol

216      (bool success, bytes memory result) = address(this).delegatecall(

445     uint256 balance = asset.balanceOf(address(this));

452                yieldBox.depositAsset(
                assetId,
                address(this),
                msg.sender,
                totalFees,
                0
            );
596            yieldBox.transfer(
            address(this),
            address(swapper),
            collateralId,
            collateralShare
        );

608     uint256 balanceBefore = yieldBox.balanceOf(address(this), assetId);

618     swapper.swap(swapData, minAssetMount, address(this), "");

619     uint256 balanceAfter = yieldBox.balanceOf(address(this), assetId);

648     yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);

649     yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

700     share <= yieldBox.balanceOf(address(this), _tokenId) - total,

704     yieldBox.transfer(from, address(this), _tokenId, share);

717      yieldBox.transfer(address(this), to, collateralId, share);

732    yieldBox.withdraw(assetId, from, address(this), amount, 0);

735     IUSDOBase(address(asset)).burn(address(this), toBurn);

758      IUSDOBase(address(asset)).mint(address(this), amount);

762     yieldBox.depositAsset(assetId, address(this), to, amount, 0);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L216

```solidity
file:  contracts/markets/singularity/SGLCommon.sol

188    share <= yieldBox.balanceOf(address(this), _assetId) - total,

192     yieldBox.transfer(from, address(this), _assetId, share);

243    yieldBox.transfer(address(this), to, assetId, share);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L188

```solidity
file:  contracts/markets/singularity/SGLLendingCommon.sol

49     yieldBox.transfer(address(this), to, collateralId, share);

79      yieldBox.transfer(address(this), to, assetId, share);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLendingCommon.sol#L49

```solidity
file:  contracts/markets/singularity/SGLLeverage.sol

47     yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);

71     _removeCollateral(from, address(this), share);

74      address(this),

75      address(this),

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol#L47

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

179     uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -

166           yieldBox.transfer(
            address(this),
            address(liquidationQueue),
            collateralId,
            allCollateralShare
        );

193    ieldBox.transfer(address(this), msg.sender, assetId, callerShare);

275     uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -

281     yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);

282     yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

288    address(this),

331    address(this),

350   swapper.swap(swapData, minAssetAmount, address(this), "");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L179

```solidity
file:  contracts/usd0/USDO.sol

68    require(token == address(this), "USDO: token not valid");

87    require(token == address(this), "USDO: token not valid");

99   uint256 _allowance = allowance(address(receiver), address(this));

101   _approve(address(receiver), address(this), _allowance - (amount + fee));

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol#L68 

```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol

161     uint256 balanceBefore = balanceOf(address(this));

164     _creditTo(_srcChainId, address(this), amount);

167     uint256 balanceAfter = balanceOf(address(this));

182    IERC20(address(this)).safeTransfer(leverageFor, amount);

198    _approve(address(this), externalData.swapper, amount);

227      value: address(this).balance

229   address(this),

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L161 

```solidity
file:   contracts/usd0/modules/USDOMarketModule.sol

160     uint256 balanceBefore = balanceOf(address(this));

163     _creditTo(_srcChainId, address(this), lendParams.depositAmount);

166      uint256 balanceAfter = balanceOf(address(this));

180    IERC20(address(this)).safeTransfer(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L160

```solidity
file:  contracts/usd0/modules/USDOOptionsModule.sol

127     ISendFrom(address(this)).sendFrom{value: address(this).balance}(

162    uint256 balanceBefore = balanceOf(address(this));

167    address(this),

172     uint256 balanceAfter = balanceOf(address(this));

191    IERC20(address(this)).safeTransfer(

227       address(this),

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L127

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

438    address lockTo = participateData.participate ? address(this) : user;

478     IERC721(oTapAddress).approve(address(this), oTAPTokenId);

528    ownerOfTapTokenId == user || ownerOfTapTokenId == address(this),

712    yieldBox.withdraw(assetId, from, address(this), amount, 0);

797    IERC20(_token).safeTransferFrom(_from, address(this), _amount);

174    deposit ? address(this) : user,

248    depositAmount > 0 ? address(this) : user,

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L438

```solidity
file:  contracts/Swapper/BaseSwapper.sol

162    IERC20(token).safeTransferFrom(msg.sender, address(this), amount)

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L162

```solidity
file:  contracts/Swapper/CurveSwapper.sol

158     uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));

163      uint256 balanceAfter = IERC20(tokenOut).balanceOf(address(this));

```
tapioca-periph-audit-023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/CurveSwapper.sol#L158

```solidity
file:  contracts/Swapper/UniswapV2Swapper.sol

155    swapData.yieldBoxData.depositToYb ? address(this) : to,

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L155

```solidity
file:  contracts/TapiocaDeployer/TapiocaDeployer.sol

29     address(this).balance >= amount,

60    return computeAddress(salt, bytecodeHash, address(this));

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol#L29

```solidity
file:   contracts/aave/AaveStrategy.sol

139    uint256 aaveBalanceBefore = rewardToken.balanceOf(address(this));

159     stakedRewardToken.claimRewards(address(this), claimable);

167    uint256 balanceOfStkAave = stakedRewardToken.balanceOf(address(this));

179    uint256 aaveBalanceAfter = rewardToken.balanceOf(address(this));

194     swapper.swap(swapData, minAmount, address(this), "");

197     uint256 queued = wrappedNative.balanceOf(address(this));

226     (amount, , , , , ) = lendingPool.getUserAccountData(address(this));

227     uint256 queued = wrappedNative.balanceOf(address(this));

235      uint256 queued = wrappedNative.balanceOf(address(this));

257     uint256 queued = wrappedNative.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L139

```solidity
file:  contracts/balancer/BalancerStrategy.sol

131   uint256 queued = wrappedNative.balanceOf(address(this));

139  uint256 queued = wrappedNative.balanceOf(address(this));

150   uint256 lpBalanceBefore = pool.balanceOf(address(this));

181    vault.joinPool(poolId, address(this), address(this), joinPoolRequest);

182    uint256 lpBalanceAfter = pool.balanceOf(address(this));

198    uint256 queued = wrappedNative.balanceOf(address(this));

209    amount <= wrappedNative.balanceOf(address(this)),

241    pool.balanceOf(address(this))

251     uint256 maxBpt = pool.balanceOf(address(this));

257     vault.exitPool(poolId, address(this), payable(this), exitRequest);

272    uint256 lpBalance = pool.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L131

```solidity
file:  contracts/compound/CompoundStrategy.sol

103   uint256 toWithdraw = cToken.balanceOf(address(this));

105     INative(address(wrappedNative)).deposit{value: address(this).balance}();

107     result = address(this).balance;

114   uint256 shares = cToken.balanceOf(address(this));

117   uint256 queued = wrappedNative.balanceOf(address(this));

124   uint256 queued = wrappedNative.balanceOf(address(this));

143   uint256 queued = wrappedNative.balanceOf(address(this));

151     value: address(this).balance

156    wrappedNative.balanceOf(address(this)) >= amount,

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L103

```solidity
file:  contracts/convex/ConvexTricryptoStrategy.sol

131    uint256 claimable = rewardPool.rewards(address(this));

151   uint256 lpBalance = rewardPool.balanceOf(address(this));

208   swapper.swap(swapData, minAmount, address(this), "");

212   uint256 queued = wrappedNative.balanceOf(address(this));

292    uint256 lpBalance = rewardPool.balanceOf(address(this));

294    uint256 queued = wrappedNative.balanceOf(address(this));

301    uint256 queued = wrappedNative.balanceOf(address(this));

330    uint256 queued = wrappedNative.balanceOf(address(this));

333    uint256 lpBalance = rewardPool.balanceOf(address(this));

341    wrappedNative.balanceOf(address(this)) >= amount,

346   queued = wrappedNative.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L131 

```solidity
file:   contracts/curve/TricryptoLPStrategy.sol

106     abi.encodeWithSignature("claimable_tokens(address)", address(this))

161      uint256 claimable = lpGauge.claimable_tokens(address(this));

163     uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));

165     uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));

180      swapper.swap(swapData, minAmount, address(this), "");

191      lpGauge.deposit(lpAmount, address(this), false);

202    result = lpGauge.balanceOf(address(this));

211    uint256 lpBalance = lpGauge.balanceOf(address(this));

212    uint256 queued = lpToken.balanceOf(address(this));

219     uint256 queued = lpToken.balanceOf(address(this));

221     lpGauge.deposit(queued, address(this), false);

236        uint256 queued = lpToken.balanceOf(address(this));

239     uint256 lpBalance = lpGauge.balanceOf(address(this));

243    lpToken.balanceOf(address(this)) >= amount,

248      queued = lpToken.balanceOf(address(this));

250    lpGauge.deposit(queued, address(this), false);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L106

```solidity
file:   contracts/curve/TricryptoNativeStrategy.sol

104      uint256 claimable = lpGauge.claimable_tokens(address(this));

152       uint256 claimable = lpGauge.claimable_tokens(address(this));

154     uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));

156       uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));

171      swapper.swap(swapData, minAmount, address(this), "");

173    uint256 queued = wrappedNative.balanceOf(address(this));

185    uint256 lpBalance = lpGauge.balanceOf(address(this));

197    uint256 lpBalance = lpGauge.balanceOf(address(this));

199     uint256 queued = wrappedNative.balanceOf(address(this));

206    uint256 queued = wrappedNative.balanceOf(address(this));

226     uint256 queued = wrappedNative.balanceOf(address(this));

229    uint256 lpBalance = lpGauge.balanceOf(address(this));

236      wrappedNative.balanceOf(address(this)) >= amount,

240     queued = wrappedNative.balanceOf(address(this));

251     lpGauge.deposit(lpAmount, address(this), false);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L104

```solidity
file:   contracts/glp/GlpStrategy.sol

120    uint256 wethAmount = weth.balanceOf(address(this));

131      amount = IERC20(contractAddress).balanceOf(address(this));

141    uint256 freeGlp = stakedGlpTracker.balanceOf(address(this));

167     uint256 wethAmount = weth.balanceOf(address(this));

190      uint256 totalVestable = vester.getMaxVestableAmount(address(this));

191        uint256 vestingNow = vester.balances(address(this));

192        uint256 alreadyVested = vester.cumulativeClaimAmounts(address(this));

209    uint256 tokenReserved = vester.pairAmounts(address(this));

254     uint256 freeEsGmx = esGmx.balanceOf(address(this));

263    stakedGlpTracker.balanceOf(address(this))

281      uint256 freeEsGmx = esGmx.balanceOf(address(this));

288     uint256 unusedStakedTokens = feeGmxTracker.balanceOf(address(this));

302   uint256 freeEsGmx = esGmx.balanceOf(address(this));

317       uint256 gmxAmount = gmx.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L120

```solidity
file:  contracts/lido/LidoEthStrategy.sol

107    uint256 toWithdraw = stEth.balanceOf(address(this));

119    uint256 stEthBalance = stEth.balanceOf(address(this));

123   uint256 queued = wrappedNative.balanceOf(address(this));

129   uint256 queued = wrappedNative.balanceOf(address(this));

148   uint256 queued = wrappedNative.balanceOf(address(this));

161    queued = wrappedNative.balanceOf(address(this));

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L107

```solidity
file:  contracts/stargate/StargateStrategy.sol

166    uint256 stgBalanceBefore = stgTokenReward.balanceOf(address(this));

168    uint256 stgBalanceAfter = stgTokenReward.balanceOf(address(this));

184   swapper.swap(swapData, minAmount, address(this), "");
 
186   uint256 queued = wrappedNative.balanceOf(address(this));

206    result = address(this).balance;

215    uint256 queued = wrappedNative.balanceOf(address(this));

216   (amount, ) = lpStaking.userInfo(lpStakingPid, address(this));

224    uint256 queued = wrappedNative.balanceOf(address(this));

235   uint256 toStake = stgNative.balanceOf(address(this));

248   uint256 queued = wrappedNative.balanceOf(address(this));

263   amount <= wrappedNative.balanceOf(address(this)),

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L166

```solidity
file:  contracts/yearn/YearnStrategy.sol

104      uint256 toWithdraw = vault.balanceOf(address(this));

105      result = vault.withdraw(toWithdraw, address(this), 0);

112   uint256 shares = vault.balanceOf(address(this));

115   uint256 queued = wrappedNative.balanceOf(address(this));

122   uint256 queued = wrappedNative.balanceOf(address(this));

124   vault.deposit(queued, address(this));

139   uint256 queued = wrappedNative.balanceOf(address(this));

145   vault.withdraw(toWithdraw, address(this), 0);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#l104

```solidity
file:  contracts/Balancer.sol

286    if (address(this).balance < _amount) revert ExceedsBalance();

305    if (erc20.balanceOf(address(this)) < _amount) revert ExceedsBalance();

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#l286

```solidity
file:  contracts/tOFT/BaseTOFT.sol

359    IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#l359

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.so

176    uint256 balanceBefore = balanceOf(address(this));

179    _creditTo(_srcChainId, address(this), amount);

182    uint256 balanceAfter = balanceOf(address(this));

197    IERC20(address(this)).safeTransfer(leverageFor, amount);

212    _unwrap(address(this), amount);

230   value: address(this).balance

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#l176

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

152    uint256 balanceBefore = balanceOf(address(this));

155    _creditTo(_srcChainId, address(this), borrowParams.amount);

158     uint256 balanceAfter = balanceOf(address(this));

172    IERC20(address(this)).safeTransfer(_from, borrowParams.amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L152

```solidity
file:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol

142   ISendFrom(address(this)).sendFrom{value: address(this).balance}(

177   uint256 balanceBefore = balanceOf(address(this));

187    uint256 balanceAfter = balanceOf(address(this));

206     IERC20(address(this)).safeTransfer(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L142

```solidity
file:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

143    uint256 balanceBefore = balanceOf(address(this));

146    _creditTo(_srcChainId, address(this), amount);

149    uint256 balanceAfter = balanceOf(address(this));

165    IERC20(address(this)).safeTransfer(onBehalfOf, amount);

206   _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));

211     LzLib.addressToBytes32(address(this)),

230    LzLib.addressToBytes32(address(this)),

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L143

```solidity
file:   contracts/YieldBox.sol

148     if (asset.contractAddress == address(this)) {

361      require(operator != address(this), "YieldBox: can't approve yieldBox");

383      require(operator != address(this), "YieldBox: can't approve yieldBox");

489     return depositAsset(registerAsset(TokenType.ERC1155, address(this), strategy, tokenId), from, to, amount, share);

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L148


###  Recommended Mitigation Steps

Use hardcoded address.


## [G-08] Emitting storage values instead of the memory one

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

###  Emit _computeSGLPoolWeights() instead of  totalSingularityPoolWeights (Save 1 SLOAD: 100 gas)

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

## [G-09] Optimizing check order for cost efficient function execution

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

### they require statment come before they if statment to save gas becouse require use state variable
```solidity
file:

522       function _releaseTap(
        uint256 _tokenId,
        address _to
    ) internal returns (uint256 releasedAmount) {
        Participation memory position = participants[_tokenId];
        if (position.tapReleased) {
            return 0;
        }
        require(position.expiry <= block.timestamp, "twTAP: Lock not expired");

```

### Recommanded code 

```solidity

      function _releaseTap(
        uint256 _tokenId,
        address _to
    ) internal returns (uint256 releasedAmount) {
        Participation memory position = participants[_tokenId];
    -    if (position.tapReleased) {
            return 0;
        }
    -    require(position.expiry <= block.timestamp, "twTAP: Lock not expired");
        
        function _releaseTap(
        uint256 _tokenId,
        address _to
    ) internal returns (uint256 releasedAmount) {
        Participation memory position = participants[_tokenId];
    +   require(position.expiry <= block.timestamp, "twTAP: Lock not expired");
    +    if (position.tapReleased) {
            return 0;
        }

```
### use require on they top first check they require after this convert they duration variable. to save gas 

```solidity

file:   contracts/tokens/BaseTapOFT.sol

88        function lockTwTapPosition(
        address to,
        uint256 amount, // Amount to add
        uint256 duration, // Duration of the position.
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_LOCK_TWTAP, // packet type
            msg.sender,
            to,
            amount,
            duration
        );

        require(duration > 0, "TapOFT: Small duration");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L88-L104

### Recommanded code 

```solidity

   function lockTwTapPosition(
        address to,
        uint256 amount, // Amount to add
        uint256 duration, // Duration of the position.
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams
    ) external payable {

        require(duration > 0, "TapOFT: Small duration");
        bytes memory lzPayload = abi.encode(
            PT_LOCK_TWTAP, // packet type
            msg.sender,
            to,
            amount,
            duration
        );

```


## [G-10] Use calldata instead of memory for function parameters


If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract.

```solidity
file:  contracts/Penrose.sol

209    function bigBangMarkets() public view returns (address[] memory markets)

424        function executeMarketFn(
        address[] calldata mc,
        bytes[] memory data,
        bool forceSuccess
    )
        external

485       function _withdrawAllProtocolFees(
        ISwapper[] calldata swappers_,
        IPenrose.SwapData[] calldata swapData_,
        IMarket[] memory markets_
    ) private 

542       function _getMasterContractLength(
        IPenrose.MasterContract[] memory array
    ) public view returns 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L209

```solidity
file:   contracts/tokens/BaseTapOFT.sol

163        function claimRewards(
        address to,
        uint256 tokenID,
        address[] memory rewardTokens,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams,
        IRewardClaimSendFromParams[] calldata rewardClaimSendParams
    ) external payable 

332    function _callApproval(ICommonData.IApproval[] memory approvals) private

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L163-L171



```solidity
file:    contracts/governance/twTAP.sol

361        function claimAndSendRewards(
        uint256 _tokenId,
        IERC20[] memory _rewardTokens
    ) external

499       function _claimRewardsOn(
        uint256 _tokenId,
        address _to,
        IERC20[] memory _rewardTokens
    ) internal

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L361-L364


```solidity
file:    contracts/usd0/modules/USDOMarketModule.sol

243       function _callApproval(ICommonData.IApproval[] memory approvals) private

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L243

```solidity
file:   contracts/usd0/modules/USDOOptionsModule.sol

214            ICommonData.IApproval[] memory approvals
    ) public

244   function _callApproval(ICommonData.IApproval[] memory approvals) private 
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L214

```solidity
file:   contracts/Magnetar/MagnetarV2.sol

965       function _singularityMarketInfo(
        address who,
        ISingularity[] memory markets

996        function _bigBangMarketInfo(
        address who,
        IBigBang[] memory markets
    ) private view returns 

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L965-L968

```solidity

file:    contracts/convex/ConvexTricryptoStrategy.sol

189        function compound(bytes memory data) public 

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L189



## [G-11] Nested if is cheaper than single statement

```solidity
file:   contracts/usd0/BaseUSDO.sol
 
370    if (!success && !_forwardRevert)

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L370

```solidity

file:  contracts/Magnetar/MagnetarV2.sol

1046   if (!success && !allowFailure)

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1046

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

402    if (lendAmount == 0 && depositData.deposit) 

611           if (
            !removeAndRepayData.assetWithdrawData.withdraw &&
            removeAndRepayData.repayAssetOnBB
        ) 

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

```solidity
file:   contracts/aave/AaveStrategy.sol
171     if (daysPassed && balanceOfStkAave > 0)

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity 
file:   contracts/Balancer.sol
226     if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L226

```solidity
file:  contracts/TapiocaWrapper.sol

121   if (_revertOnFailure && !success)

114    if (_call[i].revertOnFailure && !success)

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L121

```solidity
file:    contracts/tOFT/BaseTOFT.sol

412      if (!success && !_forwardRevert) 

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L412

```solidity
file:  contracts/BoringMath.sol

27    if (roundUp && (result * div) / mul < value)

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L27

```solidity
file:  contracts/YieldBoxRebase.sol

35     if (roundUp && (share * totalAmount) / totalShares_ < amount) 

58     if (roundUp && (amount * totalShares_) / totalAmount < share) 

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol#L35

## [G-12] Cache storage values in memory to minimize SLOADs

The code can be optimized by minimizing the number of SLOADs.

### penrose should be cached

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

327    require(penrose.swappers(swapper), "SGL: Invalid swapper");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L327

### stEth should be cached

```solidity
file:  contracts/lido/LidoEthStrategy.sol

131   require(!stEth.isStakingPaused(), "LidoStrategy: staking paused");

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L131

### penrose should be cached

```s;lidity 
file    contracts/markets/singularity/SGLLeverage.sol

29             require(
            penrose.swappers(ISwapper(externalData.swapper)),
            "SGL: Invalid swapper"
        );

65        require(
            penrose.swappers(ISwapper(externalData.swapper)),
            "SGL: Invalid swapper"
        );

155    require(penrose.swappers(swapper), "SGL: Invalid swapper");
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol#L29-L32

### wrappedNative should be cached 

```solidity
file:   contracts/compound/CompoundStrategy.sol

155        require(
            wrappedNative.balanceOf(address(this)) >= amount,
            "CompoundStrategy: not enough"
        );

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L155-L158


### penrose should be cached 
```solidity
file:     contracts/markets/bigBang/BigBang.sol  

344     require(penrose.swappers(swapper), "SGL: Invalid swapper");

391     require(penrose.swappers(swapper), "SGL: Invalid swapper");

593     require(penrose.swappers(swapper), "BigBang: Invalid swapper");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L344

###   wrappedNative should be cached 

```solidity
file:   contracts/convex/ConvexTricryptoStrategy.sol

340            require(
            wrappedNative.balanceOf(address(this)) >= amount,
            "ConvexTricryptoStrategy: not enough"
        );


```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L340-L342


### PCNFT should be cached 

```solidity

file:   contracts/option-airdrop/AirdropBroker.sol

447     require(PCNFT.ownerOf(_tokenID) == msg.sender, "adb: Not eligible");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L447
 
###   tOLP should be cached 

```solidity

file:      contracts/options/TapiocaOptionBroker.sol

224               require(
             tOLP.isApprovedOrOwner(msg.sender, _tOLPTokenID),
            "tOB: Not approved or owner"
        );

220       require(lock.lockDuration >= EPOCH_DURATION, "tOB: Duration too short");

297      require(oTAP.exists(_oTAPTokenID), "tOB: oTAP position does not exist");

372             require(
            oTAP.isApprovedOrOwner(msg.sender, _oTAPTokenID),
            "tOB: Not approved or owner"
        );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L224-227

### lpToken should be cached 

```solidity
file:   contracts/curve/TricryptoLPStrategy.sol

242           require(
            lpToken.balanceOf(address(this)) >= amount,
            "TricryptoLPStrategy: not enough"
        );

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L242-L245

### paymentTokens[_paymentToken] should be cached to save gas 

```solidity
file:

348        ) external onlyOwner {
        paymentTokens[_paymentToken].oracle = _oracle;
        paymentTokens[_paymentToken].oracleData = _oracleData;

        emit SetPaymentToken(_paymentToken, _oracle, _oracleData);
    }
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L348-L353


### pool.cumulative should be cached to save gas 

```solidity
file:  contracts/governance/twTAP.sol

536            if (position.hasVotingPower) {
            TWAMLPool memory pool = twAML;
            pool.totalParticipants--;

            // Inverse of the participation. The participation entry tracks
            // the average magnitude as it was at the time the participant
            // entered. When going the other way around, this value matches the
            // one in the pool, but here it does not.
            if (position.divergenceForce) {
                if (pool.cumulative > position.averageMagnitude) {
                    pool.cumulative -= position.averageMagnitude;
                } else {
                    pool.cumulative = 0;
                }
            } else {
                pool.cumulative += position.averageMagnitude;
            }

            // Save new weight
            pool.totalDeposited -= position.tapAmount;

            twAML = pool; // Save twAML exit
            emit AMLDivergence(
                pool.cumulative,
                pool.averageMagnitude,
                pool.totalParticipants
            ); // Register new voting power event
        }

```


https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L536-L563

## [G-13] Use the existing Local variable/global variable when equal to a state variable to avoid reading from state

###  Local variable _pendingOwner should be used instead of reading pendingOwner[tokenId]

```solidity
file:

74          address _pendingOwner = pendingOwner[tokenId];

        // Checks
        require(msg.sender == _pendingOwner, "NTF: caller != pending owner");

        // Effects
        emit OwnershipTransferred(tokenId, owner[tokenId], _pendingOwner);
        owner[tokenId] = _pendingOwner;
        pendingOwner[tokenId] = address(0);

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L74-L82

###  Local variable _singularities should be used instead of reading singularities

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

304                uint256[] memory _singularities = singularities;
            uint256 sglLength = _singularities.length;
            uint256 sglLastIndex = sglLength - 1;

            for (uint256 i = 0; i < sglLength; i++) {
                // If last element, just pop
                if (i == sglLastIndex) {
                    delete activeSingularities[singularity];
                    delete sglAssetIDToAddress[sglAssetID];
                    singularities.pop();
                } else if (

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L304-L314


###  Local variable  asset should be used instead of reading assets[assetId]


```solidity
file:    contracts/YieldBox.sol

198            Asset storage asset = assets[assetId];
        require(asset.tokenType == TokenType.ERC20 && asset.contractAddress == address(wrappedNative), "YieldBox: not wrappedNative");

        // Effects
        uint256 share = amount._toShares(totalSupply[assetId], _tokenBalanceOf(asset),

231            Asset storage asset = assets[assetId];
        require(asset.tokenType != TokenType.Native, "YieldBox: can't withdraw Native");

        // Handle ERC721 separately
        if (asset.tokenType == TokenType.ERC721) {
            return _withdrawNFT(asset, assetId, from, to);
        }

        return _withdrawFungible(asset, assetId, from, to, amount, share); 

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L198-L203

## [G-14] Use assembly to emit events

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs.

Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

```solidity
file:  contracts/Vesting.sol

120    emit Claimed(msg.sender, _claimable);

145    emit UserRegistered(_user, _amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L120

```solidity
file:  contracts/governance/twTAP.sol

301   emit AMLDivergence(

346    emit Participate(_participant, _amount, multiplier);

558   emit AMLDivergence(

568   emit ExitPosition(_tokenId, releasedAmount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L301

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

217     emit Participate(cachedEpoch, aoTAPTokenID);

269     emit ExerciseOption(

293    emit NewEpoch(epoch, epochTAPValuation);

315    emit SetTapOracle(_tapOracle, _tapOracleData);

352   emit SetPaymentToken(_paymentToken, _oracle, _oracleData);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L217

```solidity
file:  contracts/option-airdrop/aoTAP.sol

123    emit Mint(_to, tokenId, option);

135    emit Burn(msg.sender, _tokenId, options[_tokenId]);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L123

```solidity
file:  contracts/options/oTAP.sol

110    emit Mint(_to, tokenId, option);

122    emit Burn(msg.sender, _tokenId, options[_tokenId]);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L110

```solidity
file:  contracts/options/TapiocaOptionBroker.sol

264    emit AMLDivergence(

285   emit Participate(

328    emit AMLDivergence(

344   emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);

402      emit ExerciseOption(

431    emit NewEpoch(epoch, epochTAP, epochTAPValuation);

453      emit SetTapOracle(_tapOracle, _tapOracleData);

466   emit SetPaymentToken(_paymentToken, _oracle, _oracleData);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L264

```solidity
file:  contracts/options/TapiocaOptionLiquidityProvision.sol
 
104    emit UpdateTotalSingularityPoolWeights(totalSingularityPoolWeights);

200     emit Mint(_to, uint128(sglAssetID), lockPosition);

249    emit Burn(_to, lockPosition.sglAssetID, lockPosition);

269      emit SetSGLPoolWeight(address(singularity), weight);

292    emit RegisterSingularity(address(singularity), assetID);

328     emit UnregisterSingularity(address(singularity), sglAssetID);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L104

```solidity
file:  contracts/tokens/BaseTapOFT.sol

117    emit SendToChain(

143    emit CallFailedStr(_srcChainId, _payload, _reason);

146     emit CallFailedBytes(_srcChainId, _payload, _reason);

190     emit SendToChain(

241   emit CallFailedStr(_srcChainId, _payload, _reason);

243   emit CallFailedBytes(_srcChainId, _payload, _reason);

283       emit SendToChain(

320   emit CallFailedStr(_srcChainId, _payload, _reason);

322    emit CallFailedBytes(_srcChainId, _payload, _reason);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L117

```solidity
file:   contracts/tokens/TapOFT.sol

143     emit GovernanceChainIdentifierUpdated(

154     emit PausedUpdated(paused, val);

162      emit MinterUpdated(minter, _minter);

212     emit Emitted(week, emission);

228     emit Minted(msg.sender, _to, _amount);

235   emit Burned(msg.sender, _amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L143


```solidity
file:   contracts/Penrose.sol

247    emit ProtocolWithdrawal(markets_, block.timestamp);

258     emit BigBangEthMarketDebtRate(_rate);

265    emit BigBangEthMarketSet(_market);

274     emit PausedUpdated(paused, val);

283     emit ConservatorUpdated(conservator, _conservator);

310     emit UsdoTokenUpdated(_usdoToken, usdoAssetId);

332     emit RegisterSingularityMasterContract(mcAddress, contractType_);

354      emit RegisterBigBangMasterContract(mcAddress, contractType_);

375    emit RegisterSingularity(_contract, mc);

387   emit RegisterSingularity(_contract, mc);

408    emit RegisterBigBang(_contract, mc);

420    emit RegisterBigBang(_contract, mc);

457    emit FeeToUpdate(feeTo_);

466    emit SwapperUpdate(address(swapper), enable);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L247

```solidity
file:    contracts/markets/Market.sol

144     emit LogBorrowingFee(borrowOpeningFee, _val);

152     emit LogBorrowCapUpdated(totalBorrowCap, _cap);

173     emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);

179    emit OracleUpdated();

184    emit OracleDataUpdated();

188    emit ConservatorUpdated(conservator, _conservator);

229    emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);

248     emit PausedUpdated(paused, val);

346     emit LogExchangeRate(rate);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L144

```solidity
file:  contracts/markets/MarketERC20.sol

150    emit Transfer(msg.sender, to, amount);

185     emit Transfer(from, to, amount);

292   emit ApprovalBorrow(owner, spender, amount);

297     emit Approval(owner, spender, amount);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L150

```solidity
file:  contracts/markets/bigBang/BigBang.sol

477   emit MinDebtRateUpdated(minDebtRate, _minDebtRate);

483    emit MaxDebtRateUpdated(maxDebtRate, _maxDebtRate);

488    emit DebtRateAgainstEthUpdated(

500    emit LiquidationMultiplierUpdated(

540    emit LogAccrue(extraAmount, _accrueInfo.debtRate);

557    emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);

587    emit LogRemoveCollateral(user, address(swapper), collateralShare);

588    emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

629     emit Liquidated(

716     emit LogRemoveCollateral(from, to, share);

738    emit LogRepay(from, to, amount, part);

766     emit LogBorrow(from, to, amount, feeAmount, part);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L477

```solidity
file:   contracts/markets/singularity/SGLCommon.sol

151     emit LogAccrue(0, 0, startingInterestPerSecond, 0);

153    emit LogAccrue(

215     emit Transfer(address(0), to, fraction);

218      emit LogAddAsset(skim ? address(yieldBox) : from, to, share, fraction)

237      emit Transfer(from, address(0), fraction);

242     emit LogRemoveAsset(from, to, share, fraction);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L151

```solidity
file:  contracts/markets/singularity/SGLLendingCommon.sol

37     emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);

48      emit LogRemoveCollateral(from, to, share);

71      emit LogBorrow(from, to, amount, feeAmount, part);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLendingCommon.sol#L37

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

134   emit LogRemoveCollateral(

139    emit LogRepay(

184    emit Liquidated(

196    emit LogAddAsset(

286     emit LogAddAsset(

321    emit LogRemoveCollateral(user, address(swapper), collateralShare);

322     emit LogRepay(address(swapper), user, borrowAmount, borrowPart);

360    emit Liquidated(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L134

```solidity
file:  contracts/markets/singularity/Singularity.sol
 
466   emit Transfer(address(0), _feeTo, _feesEarnedFraction);

468   emit LogWithdrawFees(_feeTo, _feesEarnedFraction);

499   emit MinimumTargetUtilizationUpdated(

511   emit MaximumTargetUtilizationUpdated(

526   emit MinimumInterestPerSecondUpdated(

538   emit MaximumInterestPerSecondUpdated(

546    emit InterestElasticityUpdated(

558    emit LqCollateralizationRateUpdated(

567    emit LiquidationMultiplierUpdated(

587    emit BidExecutionSwapperUpdated(_bidExecutionSwapper);

592   emit UsdoSwapperUpdated(_usdoSwapper);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L466


```solidity
file: contracts/usd0/BaseUSDO.sol

89    emit MaxFlashMintUpdated(maxFlashMint, _val);

98    emit FlashMintFeeUpdated(flashMintFee, _val);

107   emit ConservatorUpdated(conservator, _conservator);

117   emit PausedUpdated(paused, val);

127   emit SetMinterStatus(_for, _status);

136   emit SetBurnerStatus(_for, _status);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L89

```solidity
file:   contracts/usd0/USDO.sol

112      emit Minted(_to, _amount);

121      emit Burned(_from, _amount);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol#L112

```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol

59     emit SendToChain(lzData.lzSrcChainId, msg.sender, senderBytes, 0);

90    emit SendToChain(lzData.lzDstChainId, msg.sender, senderBytes, amount);

187   emit ReceiveFromChain(_srcChainId, leverageFor, amount);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L59

```solidity
file:  contracts/usd0/modules/USDOMarketModule.sol

57     emit SendToChain(lzDstChainId, from, LzLib.addressToBytes32(to), 0);

96     emit SendToChain(

188     emit ReceiveFromChain(_srcChainId, to, lendParams.depositAmount);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L57

```solidity
file:  contracts/usd0/modules/USDOOptionsModule.sol

50     emit SendToChain(

95      emit SendToChain(

135    emit ReceiveFromChain(lzDstChainId, from, 0);

199     emit ReceiveFromChain(

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L50

```solidity
file:  contracts/aave/AaveStrategy.sol

123    emit DepositThreshold(depositThreshold, amount);

130    emit MultiSwapper(address(swapper), _swapper);

204    emit AmountDeposited(queued);

243    emit AmountDeposited(queued);

246     emit AmountQueued(amount);

274    emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L123

```solidity
file:  contracts/balancer/BalancerStrategy.sol

110    emit DepositThreshold(depositThreshold, amount);

142     emit AmountDeposited(queued);

144     emit AmountQueued(amount);

215    emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L110

```solidity
file:  contracts/compound/CompoundStrategy.sol
 
90    emit DepositThreshold(depositThreshold, amount);

129   emit AmountDeposited(queued);

132   emit AmountQueued(amount);

161   emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L90

```solidity
file:   contracts/convex/ConvexTricryptoStrategy.sol

164    emit DepositThreshold(depositThreshold, amount);

171     emit MultiSwapper(address(swapper), _swapper);

214    emit AmountDeposited(queued);

304   emit AmountDeposited(queued);

307    emit AmountQueued(amount);

349    emit AmountDeposited(queued);

351      emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L164


```solidity
file:   contracts/curve/TricryptoLPStrategy.sol

135     emit DepositThreshold(depositThreshold, amount);

142      emit MultiSwapper(address(swapper), _swapper);

151    emit LPGetterSet(address(lpGetter), _lpGetter);

193      emit AmountDeposited(lpAmount);

222     emit AmountDeposited(queued);

225     emit AmountQueued(amount);

253    emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L135

```solidity
file:   contracts/curve/TricryptoNativeStrategy.sol

126    emit DepositThreshold(depositThreshold, amount);

133    emit MultiSwapper(address(swapper), _swapper);

142   emit LPGetterSet(address(lpGetter), _lpGetter);

176    emit AmountDeposited(queued);

209    emit AmountDeposited(queued);

212    emit AmountQueued(amount);

244    emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L126

```solidity
file:  contracts/lido/LidoEthStrategy.sol

94    emit DepositThreshold(depositThreshold, amount);

134   emit AmountDeposited(queued);

137      emit AmountQueued(amount);

166    emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L94

```solidity
file:   contracts/stargate/StargateStrategy.sol
  
143    emit DepositThreshold(depositThreshold, amount);

150    emit MultiSwapper(address(swapper), _swapper);

228    emit AmountQueued(amount);

237    emit AmountDeposited(amount);

268     emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L143

```solidity
file:  contracts/yearn/YearnStrategy.sol

91    emit DepositThreshold(depositThreshold, amount);

125   emit AmountDeposited(queued);

128   emit AmountQueued(amount);

149     emit AmountWithdrawn(to, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L91

```solidity
file:  contracts/Balancer.sol

211    emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);

243    emit ConnectedChainUpdated(_srcOft, _dstChainId, _dstOft);

256    emit RebalanceAmountUpdated(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L211

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

75     emit SendToChain(lzDstChainId, from, toAddress, 0);

123    emit SendToChain(lzDstChainId, _from, toAddress, borrowParams.amount);

177    emit ReceiveFromChain(_srcChainId, _from, borrowParams.amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L75

```solidity
file:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol
 
65     emit SendToChain(

110    emit SendToChain(

150   emit ReceiveFromChain(lzDstChainId, from, 0);

214    emit ReceiveFromChain(

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L65

```solidity
file:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

79     emit SendToChain(lzDstChainId, _from, toAddress, amount);

119   emit SendToChain(lzDstChainId, msg.sender, toAddress, amount);

170    emit ReceiveFromChain(_srcChainId, onBehalfOf, amount);

227       emit SendToChain(

234     emit ReceiveFromChain(_srcChainId, _from, _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L79

```solidity
file:    contracts/NativeTokenFactory.sol

62          emit OwnershipTransferred(tokenId, owner[tokenId], newOwner);

80          emit OwnershipTransferred(tokenId, owner[tokenId], _pendingOwner);

99          emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);

100         emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);

101         emit OwnershipTransferred(tokenId, address(0), msg.sender);

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L62

```solidity
file:  contracts/YieldBox.sol

157     emit Deposited(msg.sender, from, to, assetId, amount, share, amountOut, shareOut, false);

185    emit Deposited(msg.sender, from, to, assetId, 1, 1, 1, 1, true);

212    emit Deposited(msg.sender, msg.sender, to, assetId, amount, share, amountOut, shareOut, false);

258     emit Withdraw(msg.sender, from, to, assetId, 1, 1, 1, 1);

293      emit Withdraw(msg.sender, from, to, assetId, amount, share, amountOut, shareOut);

328    emit TransferBatch(msg.sender, from, to, ids, values);

350    emit TransferSingle(msg.sender, from, to, assetId, share_);

373   emit ApprovalForAll(_owner, operator, approved);

397      emit ApprovalForAsset(_owner, operator, assetId, approved);

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L157

## [G-15] Use assembly to validate msg.sender

We can use assembly to efficiently validate msg.sender for the didPay and uniswapV3SwapCallback functions with the least amount of opcodes necessary. Additionally, we can use xor() instead of iszero(eq()), saving 3 gas. We can also potentially save gas on the unhappy path by using scratch space to store the error selector, potentially avoiding memory expansion costs.


```solidity
file:   contracts/Magnetar/MagnetarV2Storage.sol

337      require(_from == msg.sender, "MagnetarV2: operator not approved");

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol#L337

```solidity
file:   master/contracts/glp/GlpStrategy.sol
91      require(msg.sender == address(gmxWethPool), "Not the pool");

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L91

```solidity
file:  contracts/governance/twTAP.sol

365    require(msg.sender == address(tapOFT), "twTAP: only tapOFT");

390     require(msg.sender == address(tapOFT), "twTAP: only tapOFT");

474            require(
            msg.sender == tokenOwner ||
                _to == tokenOwner ||
                isApprovedForAll(tokenOwner, msg.sender) ||
                getApproved(_tokenId) == msg.sender,
            "twTAP: cannot claim"
        );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L365

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

246           require(
            aoTAP.isApprovedOrOwner(msg.sender, _aoTAPTokenID),
            "adb: Not approved or owner"
        );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L246-L249

```solidity
file:  master/contracts/option-airdrop/aoTAP.sol

46   require(msg.sender == broker, "AOTAP: only onlyBroker");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L46

```solidity
file:  master/contracts/options/oTAP.sol

40    require(msg.sender == broker, "OTAP: only onlyBroker");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L40

```solidity
file:  contracts/tokens/TapOFT.sol

221   require(msg.sender == minter, "unauthorized");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L221

```solidity
file:  master/contracts/Penrose.sol

272    require(msg.sender == conservator, "Penrose: unauthorized");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L272

```solidity
file:  master/contracts/markets/Market.sol

246     require(msg.sender == conservator, "Market: unauthorized");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L246

```solidity
file:  contracts/markets/MarketERC20.sol
76    if (from != msg.sender) 

85   if (from != msg.sender) 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L76


```solidity
file:   contracts/usd0/BaseUSDO.sol

115     require(msg.sender == conservator, "USDO: unauthorized");
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L115



```solidity
file:   contracts/tOFT/BaseTOFT.sol

353     if (_fromAddress != msg.sender)

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L353


```solidity
file:   contracts/NativeTokenFactory.sol
46      require(msg.sender == owner[tokenId], "NTF: caller is not the owner");

77     require(msg.sender == _pendingOwner, "NTF: caller != pending owner");
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L46

## [G-16] Cache calldata/memory pointers for complex types to avoid offset calculations

The function parameters in the following instances are complex types (i.e. arrays which contain structs) and thus will result in more complex offset calculations to retrieve specific data from calldata/memory. We can avoid peforming some of these offset calculations by instantiating calldata/memory pointers


```solidity
file:   master/contracts/TapiocaWrapper.sol

140           for (uint256 i = 0; i < _call.length; i++) {
            (success, results[i]) = payable(_call[i].toft).call{
                value: msg.value
            }(_call[i].bytecode);
            if (_call[i].revertOnFailure && !success) {
                revert TapiocaWrapper__TOFTExecutionFailed(results[i]);
            }
        }

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L140-L147

```solidity
file:   contracts/NativeTokenFactory.sol

141            for (uint256 i = 0; i < len; i++) {
            _requireTransferAllowed(froms[i], isApprovedForAsset[froms[i]][msg.sender][tokenId]);
            _burn(froms[i], tokenId, amounts[i]);
        }

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L141-L144

```solidity
file:    master/contracts/Penrose.sol

437            for (uint256 i = 0; i < len; ) {
            require(
                isSingularityMasterContractRegistered[
                    masterContractOf[mc[i]]
                ] || isBigBangMasterContractRegistered[masterContractOf[mc[i]]],
                "Penrose: MC not registered"
            );
            (success[i], result[i]) = mc[i].call(data[i]);
            if (forceSuccess) {
                require(success[i], _getRevertMsg(result[i]));
            }
            ++i;
        }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L437-L449


```solidity
file:   contracts/usd0/modules/USDOMarketModule.sol

243        function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L243-L297


```solidity
file:    contracts/usd0/modules/USDOOptionsModule.sol

244        function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L244-L298

```solidity
file:   contracts/governance/twTAP.sol

508                for (uint256 i = 0; i < len; ) {
                uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];
                uint256 amount = amounts[i];

                if (amount > 0) {
                    // Math is safe: `amount` calculated safely in `claimable()`
                    claimed[_tokenId][claimableIndex] += amount;
                    rewardTokens[claimableIndex].safeTransfer(_to, amount);
                }
                ++i;
            }
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L508-L518


```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

331            if (_phase == 1) {
            for (uint256 i = 0; i < _users.length; i++) {
                phase1Users[_users[i]] = _amounts[i];
            }
        } else if (_phase == 4) {
            for (uint256 i = 0; i < _users.length; i++) {
                phase4Users[_users[i]] = _amounts[i];
            }
        }

375           for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L331-L339

## [G-17] Forgo internal function to save 1 STATICCALL


The _readAll(inBase); internal function performs two external calls and returns both of the return values from those calls. Certain functions invoke _readAll(inBase); but only use the return value from the first external call, thus performing an unnecessary extra external call. We can forgo using the internal function and instead only perform our desired external call to save 1 STATICCALL (100 gas).


```solidity
file:   master/contracts/oracle/Seer.sol
 
52        function get(
        bytes calldata
    ) external virtual returns (bool success, uint256 rate) {
        (, uint256 high) = _readAll(inBase);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/Seer.sol#L52-L55


```solidity
file:  master/contracts/oracle/Seer.sol

64       function peek(
        bytes calldata
    ) external view virtual returns (bool success, uint256 rate) {
        (, uint256 high) = _readAll(inBase);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/Seer.sol#L64-L67

```solidity
file:  master/contracts/oracle/Seer.sol

75      function peekSpot(
        bytes calldata
    ) external view virtual returns (uint256 rate) {
        (, uint256 high) = _readAll(inBase);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/Seer.sol#L75-L78



The lockPositions[_tokenId] internal function performs two external calls and returns both of the return values from those calls. Certain functions invoke lockPositions[_tokenId] but only use the return value from the first external call, thus performing an unnecessary extra external call. We can forgo using the internal function and instead only perform our desired external call to save 1 STATICCALL (100 gas).


```solidity
file:   master/contracts/options/TapiocaOptionLiquidityProvision.sol

112       function getLock(
        uint256 _tokenId
    ) external view returns (bool, LockPosition memory) {
        LockPosition memory lockPosition = lockPositions[_tokenId];

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L112-L115


```solidity
file:   master/contracts/options/TapiocaOptionLiquidityProvision.sol

207        function unlock(
        uint256 _tokenId,
        IERC20 _singularity,
        address _to
    ) external returns (uint256 sharesOut) {
        require(_exists(_tokenId), "tOLP: Expired position");

        LockPosition memory lockPosition = lockPositions[_tokenId];

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L207-L214

## [G-18] A modifier used only once and not being inherited should be inlined to save gas

```solidity
file:  master/contracts/options/oTAP.sol

39       modifier onlyBroker() {
        require(msg.sender == broker, "OTAP: only onlyBroker");
        _;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L39-L42

```solidity
file:  master/contracts/markets/Market.sol

115        modifier notPaused() {
        require(!paused, "Market: paused");
        _;
    }

120       modifier solvent(address from) {
        updateExchangeRate();
        _accrue();

        _;

        require(_isSolvent(from, exchangeRate), "Market: insolvent");
    }

130      modifier onlyOnce() {
        require(!initialized, "Market: initialized");
        _;
        initialized = true;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L115-L118

```solidity
file:   master/contracts/Balancer.sol

117        modifier onlyValidSlippage(uint256 _slippage) {
        if (_slippage >= 1e5) revert SlippageNotValid();
        _;
    }

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L117-L120

## [G-19] Call environment variables directly instead of caching them

### there is no need to cache _paymentTokens use directly to save gas 

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

365       function collectPaymentTok    modifier onlyValidSlippage(uint256 _slippage) {
        if (_slippage >= 1e5) revert SlippageNotValid();
        _;
    }ens(
        address[] calldata _paymentTokens
    ) external onlyOwner {
        require(
            paymentTokenBeneficiary != address(0),
            "adb: Payment token beneficiary not set"
        );
        uint256 len = _paymentTokens.length;

        unchecked {
            for (uint256 i = 0; i < len; ++i) {
                ERC20 paymentToken = ERC20(_paymentTokens[i]);
                paymentToken.transfer(
                    paymentTokenBeneficiary,
                    paymentToken.balanceOf(address(this))
                );
            }
        }
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L365-L383



## [G-20] a modifier not inherited and not used by they contract should be remove to save gas 

```solidity
file:   master/contracts/Swapper/BaseSwapper.sol

18       modifier validAddress(address _addr) {
        if (_addr == address(0)) revert AddressNotValid();
        _;
    }

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L18-L21

```solidity
file:  contracts/tOFT/BaseTOFT.sol

45      modifier onlyHostChain() {
        require(block.chainid == hostChainID, "TOFT_host");
        _;
    }


```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L45-L48


## [G-21] Use assembly for loops

In the following instances, assembly is used for more gas efficient loops. The only memory slots that are manually used in the loops are scratch space (0x00-0x20), the free memory pointer (0x40), and the zero slot (0x60). This allows us to avoid using the free memory pointer to allocate new memory, which may result in memory expansion costs.


```solidity
file:   master/contracts/convex/ConvexTricryptoStrategy.sol

280           for (uint256 i = 0; i < tempData.tokens.length; i++) {
            finalBalances[i] = balancesAfter[i] - balancesBefore[i];
        }

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L280-L281

```solidity
file:  master/contracts/TapiocaWrapper.sol

98            for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {
            harvestableTapiocaOFTs[i].harvestFees();
        }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L98-L100

```solidity
file:  master/contracts/NativeTokenFactory.sol

129            for (uint256 i = 0; i < len; i++) {
            _mint(tos[i], tokenId, amounts[i]);
        }

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L129-L131

```solidity
file:   master/contracts/YieldBox.sol

309      for (uint256 i = 0; i < len; i++) {
            _requireTransferAllowed(from, isApprovedForAsset[from][msg.sender][assetIds_[i]]);
        }

339           for (uint256 i = 0; i < len; i++) {
            require(tos[i] != address(0), "YieldBox: to not set"); // To avoid a bad UI from burning funds
        }

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L309-L311

```solidity
file:   master/contracts/governance/twTAP.sol

413               for (uint256 i = 0; i < len; ) {
            
                next.totalDistPerVote[i] += prev.totalDistPerVote[i];
                unchecked {
                    ++i;
                }
            }
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L413-L419

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

339           for (uint256 i = 0; i < len; i++) {
            total += activeSingularities[sglAssetIDToAddress[singularities[i]]]
                .poolWeight;
        }
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L339-L342

```solidity
file:   master/contracts/Penrose.sol

492              for (uint256 i = 0; i < length; ) {
         _depositFeesToYieldBox(markets_[i], swappers_[i], swapData_[i]);
         ++i;
     }


550               for (uint256 i = 0; i < _masterContractLength; ) {
                marketsLength += clonesOfCount(array[i].location);

                ++i;
            }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L492-L495

## [G-22] Use assembly to write address storage values


```solidity
file:   contracts/options/TapiocaOptionBroker.sol

81        constructor(
        address _tOLP,
        address _oTAP,
        address payable _tapOFT,
        address _paymentTokenBeneficiary,
        uint256 _epochDuration,
        address _owner
    ) {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tOLP = TapiocaOptionLiquidityProvision(_tOLP);
        tapOFT = TapOFT(_tapOFT);
        oTAP = OTAP(_oTAP);
        EPOCH_DURATION = _epochDuration;
        owner = _owner;
    }

471        function setPaymentTokenBeneficiary(
        address _paymentTokenBeneficiary
    ) external onlyOwner {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L81-L95



```solidity
file:  master/contracts/option-airdrop/AirdropBroker.sol

98        constructor(
        address _aoTAP,
        address payable _tapOFT,
        address _pcnft,
        address _paymentTokenBeneficiary,
        address _owner
    ) {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tapOFT = TapOFT(_tapaymentTokenBeneficiarypOFT);
        aoTAP = AOTAP(_aoTAP);
        PCNFT = IERC721(_pcnft);
        owner = _owner;
    }


357       function setPaymentTokenBeneficiary(
        address _paymentTokenBeneficiary
    ) external onlyOwner {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L98-L110



###  Recommendation Code


```solidity

   98:     constructor(
- 105:        paymentTokenBeneficiary = _paymentTokenBeneficiary;
+                  assembly {                      
+                      sstore(vault. paymentTokenBeneficiary, _paymentTokenBeneficiary)
+                  }    


```

```solidity
file:  contracts/tokens/BaseTapOFT.sol

326        function setTwTap(address _twTap) external onlyOwner {
        twTap = TwTAP(_twTap);
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L326-L328

```solidity
file:  master/contracts/tokens/TapOFT.sol

160       function setMinter(address _minter) external onlyOwner {
        require(_minter != address(0), "address not valid");
        emit MinterUpdated(minter, _minter);
        minter = _minter;
    }

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L160-L164

```solidity
file:  contracts/markets/Market.sol

263        function setBigBangEthMarket(address _market) external onlyOwner {
        bigBangEthMarket = _market;
        emit BigBangEthMarketSet(_market);
    }

281       function setConservator(address _conservator) external onlyOwner {
        require(_conservator != address(0), "Penrose: address not valid");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L263-L266

```solidity
file:  master/contracts/glp/GlpStrategy.sol

113       function setFeeRecipient(address recipient) external onlyOwner {
        feeRecipient = recipient;
    }

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L113-L114

## [G‑23] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

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


## [G-24] Use assembly to check for address(0)

```solidity
file:   contracts/markets/singularity/Singularity.sol

581   if (address(_liquidationQueue) != address(0)) {

586     if (_bidExecutionSwapper != address(0)) {

591     if (_usdoSwapper != address(0)) {

611     if (module == address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L581


```solidity
file:  master/contracts/Vesting.sol

132    if (_user == address(0)) revert AddressNotValid();

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L132

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol
  
168     paymentTokenOracle.oracle != IOracle(address(0)),

243     paymentTokenOracle.oracle != IOracle(address(0)),

369      paymentTokenBeneficiary != address(0),

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L168

```solidity
file:  master/contracts/option-airdrop/aoTAP.sol

140  require(broker == address(0), "AOTAP: only once");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L140

```solidity
file:  contracts/options/oTAP.sol

127    require(broker == address(0), "OTAP: only once");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L127

```solidity
file:  contracts/options/TapiocaOptionBroker.sol

171    paymentTokenOracle.oracle != IOracle(address(0)),

369    paymentTokenOracle.oracle != IOracle(address(0)),

483     paymentTokenBeneficiary != address(0),

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L171

```solidity
file:  master/contracts/tokens/TapOFT.sol

118     require(_lzEndpoint != address(0), "LZ endpoint not valid");

161    require(_minter != address(0), "address not valid");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L118

```solidity
file:   tree/master/contracts/Penrose.sol

242       require(address(swappers_[0]) != address(0), "Penrose: zero address");

243       require(address(markets_[0]) != address(0), "Penrose: zero address");

282      require(_conservator != address(0), "Penrose: address not valid");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L242

```solidity
file:  contracts/markets/Market.sol
 
177   if (address(_oracle) != address(0)) 

187   if (_conservator != address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L177

```solidity
file: contracts/markets/MarketERC20.sol

144   require(to != address(0), "ERC20: no zero address");

179    require(to != address(0), "ERC20: no zero address"); 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L144

```solidity
file:   contracts/markets/bigBang/BigBang.sol

133           require(
            address(_collateral) != address(0) &&
                address(_asset) != address(0) &&
                address(_oracle) != address(0),
            "BigBang: bad pair"
        );
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L133-L138

```solidity
file:  contracts/markets/singularity/SGLCommon.sol

215    emit Transfer(address(0), to, fraction);

237     emit Transfer(from, address(0), fraction);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L215

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

40    if (address(liquidationQueue) != address(0)) 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L40


```solidity
file:  master/contracts/usd0/BaseUSDO.sol

106    require(_conservator != address(0), "USDO: address not valid");

354    if (module == address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L106

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

1057    if (module == address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1057

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

305    if (address(singularity) != address(0)) {

308     if (address(bigBang) != address(0)) {

487    if (address(singularity) != address(0)) {

490     if (address(bigBang) != address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L305

```solidity
file:  contracts/Swapper/BaseSwapper.sol

19     if (_addr == address(0)) revert AddressNotValid();

112    if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L19

```solidity
file:  master/contracts/glp/GlpStrategy.sol

62    require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router")

66    require(_gmx != address(0), "Bad GMX reward router");

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L62

```solidity
file:  master/contracts/Balancer.sol

112   if (connectedOFTs[_srcOft][_dstChainId].dstOft == address(0))

127   if (_router == address(0)) revert RouterNotValid();

128   if (_routerETH == address(0)) revert RouterNotValid();

142   if (ITapiocaOFT(_srcOft).erc20() == address(0)) {

201    bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);

225     bool isNative = ITapiocaOFT(_srcOft).erc20() == address(0);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L112

```solidity
file:  contracts/tOFT/BaseTOFT.sol

371    if (erc20 == address(0)) {

396     if (module == address(0)) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L371

```solidity
file:  contracts/tOFT/mTapiocaOFT.sol

94    if (erc20 == address(0)) {

144   bool _isNative = erc20 == address(0);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L94

```solidity
file:  master/contracts/NativeTokenFactory.sol

59    require(newOwner != address(0) || renounce, "NTF: zero address");

64   pendingOwner[tokenId] = address(0);

82   pendingOwner[tokenId] = address(0);

93   _registerAsset(TokenType.Native, address(0), NO_STRATEGY, tokenId);

100   emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);

101   emit OwnershipTransferred(tokenId, address(0), msg.sender);

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L59

```solidity
file:  master/contracts/YieldBox.sol

317   require(to != address(0), "No 0 address");

340   require(tos[i] != address(0), "YieldBox: to not set"); //

360   require(operator != address(0), "YieldBox: operator not set"); /

382    require(operator != address(0), "YieldBox: operator not set");

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L317

## [G-25] Don’t initialize variables with default value


```solidity
file:  contracts/governance/twTAP.sol

196    for (uint256 i = 0; i < len; )

213    for (uint256 i = 0; i < len; ) {

488   for (uint256 i = 0; i < len; ++i) {

508   for (uint256 i = 0; i < len; ) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L196

```solidity
file:  master/contracts/YieldBox.sol

309    for (uint256 i = 0; i < len; i++) {

320    for (uint256 i = 0; i < len; i++) {

339      for (uint256 i = 0; i < len; i++) {

345     for (uint256 i = 0; i < len; i++) {

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L309

```solidity
file:  master/contracts/Vesting.sol

29    uint256 public seeded = 0;

138    data.claimed = 0;

14Don’t initialize variables with default value0     data.latestClaimTimestamp = 

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L29

```solidity
file:  contracts/option-airdrop/AirdropBroker.sol

332   for (uint256 i = 0; i < _users.length; i++) {

336   for (uint256 i = 0; i < _users.length; i++) {

375    for (uint256 i = 0; i < len; ++i) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L332

```solidity
file:  contracts/options/TapiocaOptionBroker.sol

289    for (uint256 i = 0; i < len; ++i)

565      for (uint256 i = 0; i < len; ++i) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L289

```solidity
file:  contracts/options/TapiocaOptionLiquidityProvision.sol
 
136    for (uint256 i = 0; i < len; ++i) {

308     for (uint256 i = 0; i < sglLength; i++) {

339   for (uint256 i = 0; i < len; i++) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L136

```solidity
file:  master/contracts/tokens/BaseTapOFT.sol

228     for (uint i = 0; i < len; ) {

333     for (uint256 i = 0; i < approvals.length; ) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L228


```solidity
file:  master/contracts/Penrose.sol

437    for (uint256 i = 0; i < len; ) {

492     for (uint256 i = 0; i < length; ) {

550    for (uint256 i = 0; i < _masterContractLength; ) {

564   for (uint256 i = 0; i < _masterContractLength; ) {

569   for (uint256 j = 0; j < clonesOfLength; ) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L437

```solidity
file: contracts/markets/bigBang/BigBang.sol

215   for (uint256 i = 0; i < calls.length; i++) {

603    uint256 minAssetMount = 0;

665    uint256 liquidatedCount = 0;

666  for (uint256 i = 0; i < users.length; i++) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L215

```solidity
file:  contracts/markets/singularity/SGLCommon.sol

51       extraAmount = 0;

52        feeFraction = 0;

246       _yieldBoxShares[from][ASSET_SIG] = 0; 

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L51

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

44    uint256 needed = 0;

45     for (uint256 i = 0; i < maxBorrowParts.length; i++) 

107   for (uint256 i = 0; i < users.length; i++) {

337    uint256 minAssetAmount = 0;

383     uint256 liquidatedCount = 0;

384    for (uint256 i = 0; i < users.length; i++) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L44

```solidity
file:  contracts/markets/singularity/Singularity.sol

187    for (uint256 i = 0; i < calls.length; i++) {

467     accrueInfo.feesEarnedFraction = 0;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L187

```solidity
file:  master/contracts/Magnetar/MagnetarV2.sol

202   for (uint256 i = 0; i < length; i++) {

973   for (uint256 i = 0; i < len; i++) {

1004  for (uint256 i = 0; i < len; i++) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L202

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

401   uint256 fraction = 0;

414   uint256 tOLPTokenId = 0;

508    uint256 tOLPId = 0;

571      uint256 _removeAmount = 0;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L401

```solidity
file:  master/contracts/Multicall/Multicall3.sol

47     for (uint256 i = 0; i < length; ) {

70     for (uint256 i = 0; i < length; ) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L47

```solidity
file:  master/contracts/balancer/BalancerStrategy.sol

156    for (uint256 i = 0; i < poolTokens.length; i++) {

225   for (uint256 i = 0; i < poolTokens.length; i++) {

277   for (uint256 i = 0; i < poolTokens.length; i++) {

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L156

```solidity
file:  contracts/convex/ConvexTricryptoStrategy.sol

195     for (uint256 i = 0; i < tokens.length; i++) {

255     for (uint256 i = 0; i < tempData.tokens.length; i++) {

273    for (uint256 i = 0; i < tempData.tokens.length; i++) {

280    for (uint256 i = 0; i < tempData.tokens.length; i++) {
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L195

```solidity
file:  contracts/TapiocaWrapper.sol

98    for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {

140   for (uint256 i = 0; i < _call.length; i++) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L98

```solidity
file:  contracts/NativeTokenFactory.sol

129    for (uint256 i = 0; i < len; i++) {

141   for (uint256 i = 0; i < len; i++) {

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L129

## [G-26] Do not calculate constants

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


## [G-27] Amounts should be checked for 0 before calling a transfer

Checking non-zero transfer values can avoid an expensive external call and save gas.
While this is done at some places, it’s not consistently done in the solution.
I suggest adding a non-zero-value check here:

```solidity
file: contracts/governance/twTAP.sol

449   rewardToken.safeTransferFrom(msg.sender, address(this), _amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L449

```solidity
file:  master/contracts/option-airdrop/AirdropBroker.sol
 
514    tapOFT.transfer(msg.sender, tapAmount);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L514

```solidity
file:  contracts/tokens/BaseTapOFT.sol

144    _transferFrom(address(this), to, amount);

147     _transferFrom(address(this), to, amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L144

```solidity
file:  master/contracts/tokens/LTap.sol

42    tapToken.transferFrom(msg.sender, address(this), amount);

50     tapToken.transfer(msg.sender, amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol#L42

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

797    IERC20(_token).safeTransferFrom(_from, address(this), _amount);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L797

```solidity
file:  contracts/Swapper/BaseSwapper.sol

162    IERC20(token).safeTransferFrom(msg.sender, address(this), amount);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L162

```solidity
file:  contracts/Swapper/CurveSwapper.sol

140     IERC20(tokenOut).safeTransfer(to, amountOut);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol#L140

```solidity
file:  master/contracts/glp/GlpStrategy.sol

93     gmx.safeTransfer(address(gmxWethPool), amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L93

```solidity
file:  contracts/tOFT/BaseTOFT.sol

359    IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);

374    IERC20(erc20).safeTransfer(_toAddress, _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L359

```solidity
file:  contracts/tOFT/mTapiocaOFT.sol

146    _safeTransferETH(msg.sender, _amount);

148     IERC20(erc20).safeTransfer(msg.sender, _amount);
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L146

```solidity
file:   contracts/tOFT/modules/BaseTOFTLeverageModule.sol

273     _safeTransferETH(_toAddress, _amount);

275   IERC20(erc20).safeTransfer(_toAddress, _amount);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L273 


```solidity
file:  tree/master/contracts/YieldBox.sol

151    IERC1155(asset.contractAddress).safeTransferFrom(from, address(asset.strategy), asset.tokenId, amount, "");

209    wrappedNative.safeTransfer(address(asset.strategy), amount);
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L151

## [G-28] abi.encode() is less efficient than abi.encodePacked()

Consider changing it if possible.

There are 38 instances of this issue:

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

## [G-29] Using XOR (^) and OR (|) bitwise equivalents

Estimated savings: 73 gas
Max savings according to yarn profile: 282 gas

On Remix, given only uint256 types, the following are logical equivalents, but don’t cost the same amount of gas:

(a != b || c != d || e != f) costs 571
((a ^ b) | (c ^ d) | (e ^ f)) != 0 costs 498 (saving 73 gas)
Consider rewriting as following to save gas:

```solidity
file:  contracts/YieldBox.sol

121   require(asset.tokenType != TokenType.Native && asset.tokenType != TokenType.ERC721, "YieldBox: can't deposit type");

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L121

```solidity
file:   contracts/Swapper/BaseSwapper.sol

112     if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L112


## [G-30] Using a positive conditional flow to save a NOT opcode

Estimated savings: 3 gas
Max savings according to yarn profile: 150 gas

There are 55 instances of this issue:

The following function either revert or returns some value. To save some gas (NOT opcode costing 3 gas), switch to a positive statement:


```solidity
file:  contracts/tokens/BaseTapOFT.sol

345    if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L345

```solidity
file:  contracts/markets/bigBang/BigBang.sol
  
157    if (!_isEthMarket) {

474     if (!_isEthMarket) {

668      if (!_isSolvent(user, _exchangeRate)) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L157

```solidity
file:  contracts/markets/singularity/SGLLiquidation.sol

109    if (!_isSolvent(user, _exchangeRate)) {

386     if (!_isSolvent(user, _exchangeRate)) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L109

```solidity
file:   contracts/markets/singularity/Singularity.sol

626     if (!success) {

639      if (!success) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L626

```solidity
file:   master/contracts/usd0/BaseUSDO.sol

370    if (!success && !_forwardRevert) {

388    if (!success) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L370

```solidity
file:  contracts/usd0/modules/USDOLeverageModule.sol
 
163    if (!credited) {

180     if (!success) {

268     if (!approvals[i].allowFailure) {

283     if (!approvals[i].allowFailure) {

299     if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L163

```solidity
file:  contracts/usd0/modules/USDOMarketModule.sol

162    if (!credited) {

178      if (!success) {

257    if (!approvals[i].allowFailure) {

272    if (!approvals[i].allowFailure) {

288     if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L162

```solidity
file:  contracts/usd0/modules/USDOOptionsModule.sol

164     if (!credited) {

187     if (!success) {

258     if (!approvals[i].allowFailure) {

273     if (!approvals[i].allowFailure) {

289     if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L164

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

204     if (!_action.allowFailure) {

1046    if (!success && !allowFailure) {

1072     if (!success) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L204

```solidity
file:   contracts/Magnetar/modules/MagnetarMarketModule.sol

146     if (!extractFromSender) {

328     if (!mintData.collateralDepositData.extractFromSender) {

376    if (!depositData.extractFromSender) {

542     if (!removeAndRepayData.unlockData.unlock) {

774    if (!isApproved) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L146

```solidity
file:  contracts/Multicall/Multicall3.sol

54    if (!result.success) {

82    if (!result.success) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L54

```solidity
file:    contracts/Balancer.sol

226      if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

227      if (!_isValidOft(_srcOft, _dstOft, _dstChainId))

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L226

```solidity
file:   contracts/TapiocaWrapper.sol

190    if (!_linked) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L190

```solidity
file:  contracts/tOFT/BaseTOFT.sol

412    if (!success && !_forwardRevert) {

430    if (!success) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L430

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.sol

178    if (!credited) {

195     if (!success) {

298   if (!approvals[i].allowFailure) {

313   if (!approvals[i].allowFailure) {

329   if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L178

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

154     if (!credited) {

170    if (!success) {

274    if (!approvals[i].allowFailure) {

289   if (!approvals[i].allowFailure) {

305   if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L154


```solidity
file:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol

179     if (!credited) {

202     if (!success) {

273   if (!approvals[i].allowFailure) {

288   if (!approvals[i].allowFailure) {

304    if (!approvals[i].allowFailure) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L179

```solidity
file:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

145   if (!credited) {

163   if (!success) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L145


## [G-31] Make 3 event parameters indexed when possible

It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
file:  master/contracts/Vesting.sol
 
60    event UserRegistered(address indexed user, uint256 amount);

62   event Claimed(address indexed user, uint256 amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L60

```solidity
file:  contracts/governance/twTAP.sol

134     event Participate(
        address indexed participant,
        uint256 tapAmount,
        uint256 multiplier
    );

139   event AMLDivergence(
        uint256 cumulative,
        uint256 averageMagnitude,
        uint256 totalParticipants
    );

144   event ExitPosition(uint256 tokenId, uint256 amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L134-L137

```solidity
file: contracts/option-airdrop/AirdropBroker.sol

115  event Participate(uint256 indexed epoch, uint256 aoTAPTokenID);

123  event NewEpoch(uint256 indexed epoch, uint256 epochTAPValuation);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L115

```solidity
file:  contracts/option-airdrop/aoTAP.sol

53       event Mint(
        address indexed to,
        uint256 indexed tokenId,
        AirdropTapOption option
    );

58    event Burn(
        address indexed from,
        uint256 indexed tokenId,
        AirdropTapOption option
    );

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L53-L57

```solidity
file:

81       event Mint(
        address indexed to,
        uint128 indexed sglAssetID,
        LockPosition lockPosition
    );
86    event Burn(
        address indexed to,
        uint128 indexed sglAssetID,
        LockPosition lockPosition
    );
91    event UpdateTotalSingularityPoolWeights(
        uint256 totalSingularityPoolWeights
    );
94    event SetSGLPoolWeight(address sgl, uint256 poolWeight);

95    event RegisterSingularity(address sgl, uint256 assetID);

96   event UnregisterSingularity(address sgl, uint256 assetID);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L81-L85

```solidity
file:  contracts/tokens/BaseTapOFT.sol

41     event CallFailedStr(uint16 _srcChainId, bytes _payload, string _reason);

42     event CallFailedBytes(uint16 _srcChainId, bytes _payload, bytes _reason);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L41

```solidity
file:  contracts/tokens/TapOFT.sol

78       event Emitted(uint256 week, uint256 amount);
    /// @notice event emitted when new TAP is minted
    event Minted(address indexed _by, address indexed _to, uint256 _amount);
    /// @notice event emitted when new TAP is burned
    event Burned(address indexed _from, uint256 _amount);
    /// @notice event emitted when the governance chain identifier is updated
    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
    /// @notice event emitted when pause state is changed
    event PausedUpdated(bool oldState, bool newState);

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L78-L86

```solidity
file:  contracts/markets/bigBang/BigBang.sol

92       event MinDebtRateUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the maximum debt rate is updated
    event MaxDebtRateUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the debt rate against the main market is updated
    event DebtRateAgainstEthUpdated(uint256 oldVal, uint256 newVal);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L92-L96

```solidity
file:   contracts/markets/singularity/SGLStorage.sol

118       event LogWithdrawFees(address indexed feeTo, uint256 feesEarnedFraction);
    /// @notice event emitted when fees are deposited to YieldBox
    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);
    /// @notice event emitted when the minimum target utilization is updated
    event MinimumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the maximum target utilization is updated
    event MaximumTargetUtilizationUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the minimum interest per second is updated
    event MinimumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the maximum interest per second is updated
    event MaximumInterestPerSecondUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the interest elasticity updated
    event InterestElasticityUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the LQ collateralization rate is updated
    event LqCollateralizationRateUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the order book liquidation multiplier rate is updated
    event OrderBookLiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
    /// @notice event emitted when the bid execution swapper is updated
    event BidExecutionSwapperUpdated(address newAddress);
    /// @notice event emitted when the usdo swapper is updated
    event UsdoSwapperUpdated(address newAddress);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L118-L138

```solidity
file:  contracts/usd0/BaseUSDOStorage.sol
 
52      event Minted(address indexed _for, uint256 _amount);
    /// @notice event emitted when USDO is burned
    event Burned(address indexed _from, uint256 _amount);
    /// @notice event emitted when a new address is set or removed from minters array
    event SetMinterStatus(address indexed _for, bool _status);
    /// @notice event emitted when a new address is set or removed from burners array
    event SetBurnerStatus(address indexed _for, bool _status);
    /// @notice event emitted when conservator address is updated
    event ConservatorUpdated(address indexed old, address indexed _new);
    /// @notice event emitted when pause state is updated
    event PausedUpdated(bool oldState, bool newState);
    /// @notice event emitted when flash mint fee is updated
    event FlashMintFeeUpdated(uint256 _old, uint256 _new);
    /// @notice event emitted when max flash mintable amount is updated
    event MaxFlashMintUpdated(uint256 _old, uint256 _new);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDOStorage.sol#L52-L66

```solidity
file:  master/contracts/aave/AaveStrategy.sol

53     event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L53-L56

```solidity
file:  contracts/balancer/BalancerStrategy.sol
52         event RewardTokens(uint256 _count);
    event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L52-L56

```solidity
file:  master/contracts/compound/CompoundStrategy.sol

45       event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L45-L48

```solidity 
file:  contracts/yearn/YearnStrategy.sol
46        event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L46-L49

```solidity
file:   master/contracts/tOFT/mTapiocaOFT.sol
27        event ConnectedChainStatusUpdated(uint256 _chain, bool _old, bool _new);
    /// @notice event emitted when balancer status is updated
    event BalancerStatusUpdated(
        address indexed _balancer,
        bool _bool,
        bool _new
    );
    /// @notice event emitted when rebalancing is performed
    event Rebalancing(
        address indexed _balancer,
        uint256 _amount,
        bool _isNative
    );

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L27-L39

## [G-32] Assigning keccak operations to constant variables results in extra gas costs

"constants" expressions are expressions. As such, keccak assigned to a constant variable are re-computed at each use of the variable, which will consume gas unnecessarily. To save gas, Changing the variable from constant to immutable will make the computation run only once and therefore save gas.

```solidity
file:  contracts/markets/MarketERC20.sol

29       bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );


33    bytes32 private constant _PERMIT_TYPEHASH_BORROW =
        keccak256(
            "PermitBorrow(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L29-L32

```solidity 
file:   contracts/usd0/BaseUSDOStorage.sol

38     bytes32 internal constant FLASH_MINT_CALLBACK_SUCCESS =
        keccak256("ERC3156FlashBorrower.onFlashLoan");

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDOStorage.sol#L38-L39


```solidity
file:  master/contracts/YieldBoxPermit.sol

27      bytes32 private constant _PERMIT_TYPEHASH =
        keccak256("Permit(address owner,address spender,uint256 assetId,uint256 nonce,uint256 deadline)");
    

31    bytes32 private constant _PERMIT_ALL_TYPEHASH =
        keccak256("PermitAll(address owner,address spender,uint256 nonce,uint256 deadline)");

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L27-L29

## [G-33]  10 ** 18 can be changed to 1e18 and save some gas

```solidity
file:  master/contracts/compound/CompoundStrategy.sol

116    uint256 invested = (shares * pricePerShare) / (10 ** 18);

146   uint256 toWithdraw = (((amount - queued) * (10 ** 18)) /

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L116


## [G-34] With assembly, .call (bool success) transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.


```solidity
file:  contracts/Magnetar/MagnetarV2.sol

1045   (bool success, bytes memory returnData) = target.call(actionCalldata);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1045

```solidity
file:  contracts/tOFT/BaseTOFT.sol

380   (bool sent, ) = to.call{value: amount}("");

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L380

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.sol

280    (bool sent, ) = to.call{value: amount}("");

```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L280

```solidity
file:  contracts/Swapper/BaseSwapper.sol

99   (bool success, bytes memory data) = token.call(

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L99

```solidity
file:  master/contracts/convex/ConvexTricryptoStrategy.sol

356      (bool successEmtptyApproval, ) = token.call(
            abi.encodeWithSelector(
                bytes4(keccak256("approve(address,uint256)")),
                to,
                0
            )
        );

369   (bool success, bytes memory data) = token.call(
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L356-L362

## [G-35] Use constants instead of type(uintx).max

There are 34 instances of this issue:

type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
file:  master/contracts/governance/twTAP.sol

313   require(expiry < type(uint56).max, "twTAP: too long");

```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L313

```solidity
file:  contracts/markets/MarketERC20.sol

172     if (spenderAllowance != type(uint256).max) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L172

```solidity
file:  contracts/Magnetar/MagnetarV2.sol

1012  result[i].maxDebtRate = bigBang.maxDebtRate();

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1012

```solidity
file:  contracts/oracle/implementations/ARBTriCryptoOracle.sol

137   uint256 _discount = Math.max((_g ** 2 / 1 ether) * _a, 1e34); //

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L137

```solidity
file:   master/contracts/aave/AaveStrategy.sol

75           wrappedNative.approve(_lendingPool, type(uint256).max);

76          rewardToken.approve(_multiSwapper, type(uint256).max);

150       type(uint256).max,

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L75

```solidity
file:  master/contracts/balancer/BalancerStrategy.sol

77      wrappedNative.approve(_vault, type(uint256).max);

78      IERC20(address(pool)).approve(_vault, type(uint256).max)

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L77

```solidity
file:  master/contracts/compound/CompoundStrategy.sol

58   wrappedNative.approve(_cToken, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L58

```solidity
file:    master/contracts/convex/ConvexTricryptoStrategy.sol

105        wrappedNative.approve(_lpGetter, type(uint256).max);

106        lpToken.approve(_lpGetter, type(uint256).max);

107        lpToken.approve(_booster, type(uint256).max);

108        rewardToken.approve(_multiSwapper, type(uint256).max);

174        rewardToken.approve(_swapper, type(uint256).max);

183        wrappedNative.approve(_lpGetter, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L105

```solidity
file:  master/contracts/curve/TricryptoLPStrategy.sol

79       lpToken.approve(_lpGauge, type(uint256).max);

80        lpToken.approve(_lpGetter, type(uint256).max);

81        rewardToken.approve(_multiSwapper, type(uint256).max);

82        wrappedNative.approve(_lpGetter, type(uint256).max);

144     rewardToken.approve(_swapper, type(uint256).max);

154     wrappedNative.approve(_lpGetter, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L79

```solidity
file:   contracts/curve/TricryptoNativeStrategy.sol

78        IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);

79        IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);

80        rewardToken.approve(_multiSwapper, type(uint256).max);

81        wrappedNative.approve(_lpGetter, type(uint256).max);

135        rewardToken.approve(_swapper, type(uint256).max);

145     wrappedNative.approve(_lpGetter, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L78

```solidity
file:  contracts/lido/LidoEthStrategy.sol
  
62    IERC20(_stEth).approve(_curvePool, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L62

```solidity
file:  contracts/stargate/StargateStrategy.sol
 
89       stgNative.approve(_lpStaking, type(uint256).max);

90      stgNative.approve(address(router), type(uint256).max);

94     stgTokenReward.approve(_swapper, type(uint256).max);

153    stgTokenReward.approve(_swapper, type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L89

```solidity
file:   contracts/yearn/YearnStrategy.sol

59     wrappedNative.approve(address(vault), type(uint256).max);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L59

```solidity
file:   master/contracts/BoringMath.sol

6       require(a <= type(uint128).max, "BoringMath: uint128 Overflow");

11     require(a <= type(uint64).max, "BoringMath: uint64 Overflow");

16    require(a <= type(uint32).max, "BoringMath: uint32 Overflow");

```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L6

## [G-36] Use fixed size bytes array rather than string or bytes[]

If the string you are dealing with can be limited to max of 32 characters, use bytes[32] instead of dynamic bytes array or string.

```solidity
file:  contracts/markets/bigBang/BigBang.sol

212    ) external returns (bool[] memory successes, string[] memory results) 

214   results = new string[](calls.length);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L212

```solidity
file:  contracts/markets/singularity/Singularity.sol

184   ) external returns (bool[] memory successes, string[] memory results) 

186    results = new string[](calls.length);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L184

```solidity
file:  contracts/Penrose.sol

426    bytes[] memory data,

432   returns (bool[] memory success, bytes[] memory result)

436   result = new bytes[](len);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L426

```solidity
file:  contracts/markets/bigBang/BigBang.sol

210     bytes[] calldata calls,

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L210

```solidity
file:  contracts/markets/singularity/Singularity.sol

182    bytes[] calldata calls,

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L182

```solidity
file:  contracts/TapiocaWrapper.sol

137    returns (bool success, bytes[] memory results)

139    results = new bytes[](_call.length);


```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L137

