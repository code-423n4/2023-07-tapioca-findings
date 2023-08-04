## [G-01] Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it’s still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract, and cases where a constructor is involved

    File: tapioca-bar-audit/contracts/usd0/BaseUSDO.sol 💰 👥	

    215: function initMultiHopBuy(
    216:     address from,
    217:     uint256 collateralAmount,
    218:     uint256 borrowAmount,
    219:     IUSDOBase.ILeverageSwapData calldata swapData,
    220:     IUSDOBase.ILeverageLZData calldata lzData,
    221:     IUSDOBase.ILeverageExternalContractsData calldata externalData,
    222:     bytes calldata airdropAdapterParams,
    223:     ICommonData.IApproval[] memory approvals
    223: ) external payable {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

    File: tapioca-bar-audit/contracts/Penrose.sol 🖥 💰 📤 🌀 Σ	

    424: function executeMarketFn(
    425:     address[] calldata mc,
    426:     bytes[] memory data,
    427:     bool forceSuccess
    428: )
    429:     external

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol

    File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol 💰 👥	

    89: function retrieveFromStrategy(
    90:     address _from,
    91:     uint256 amount,
    92:     uint256 share,
    93:     uint256 assetId,
    94:     uint16 lzDstChainId,
    95:     address zroPaymentAddress,
    96:     bytes memory airdropAdapterParam
    97: ) external payable {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol

    File: tapiocaz-audit/contracts/Balancer.sol 💰	

    172: function rebalance(
    173:     address payable _srcOft,
    174:     uint16 _dstChainId,
    175:     uint256 _slippage,
    176:     uint256 _amount,
    177:     bytes memory _ercData
    178: )
    179:     external

    219: function initConnectedOFT(
    220:     address _srcOft,
    221:     uint16 _dstChainId,
    222:     address _dstOft,
    223:     bytes memory _ercData
    224: ) external onlyOwner {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol

    File: tapiocaz-audit/contracts/tOFT/BaseTOFT.sol 💰 👥	

    156: function initMultiSell(
    157:     address from,
    158:     uint256 share,
    158:     IUSDOBase.ILeverageSwapData calldata swapData,
    159:     IUSDOBase.ILeverageLZData calldata lzData,
    160:     IUSDOBase.ILeverageExternalContractsData calldata externalData,
    161:     bytes calldata airdropAdapterParams,
    162:     ICommonData.IApproval[] memory approvals
    163: ) external payable {

    256: function retrieveFromStrategy(
    257:     address from,
    258:     uint256 amount,
    259:     uint256 share,
    260:     uint256 assetId,
    261:     uint16 lzDstChainId,
    262:     address zroPaymentAddress,
    263:     bytes memory airdropAdapterParam
    264: ) external payable {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol

    File: tap-token-audit/contracts/governance/twTAP.sol 🖥 📤 Σ	

    361: function claimAndSendRewards(
    362:     uint256 _tokenId,
    363:     IERC20[] memory _rewardTokens
    364: ) external {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol

    File: tap-token-audit/contracts/tokens/BaseTapOFT.sol 💰 ♻️ Σ	

    163: function claimRewards(
    164:     address to,
    165:     uint256 tokenID,
    166:     address[] memory rewardTokens,
    167:     uint16 lzDstChainId,
    168:     address zroPaymentAddress,
    169:     bytes calldata adapterParams,
    170:     IRewardClaimSendFromParams[] calldata rewardClaimSendParams
    171: ) external payable {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

    File: tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol 🖥 💰 🌀	

    22: function deploy(
    23:     uint256 amount,
    24:     bytes32 salt,
    25:     bytes memory bytecode,
    26:     string memory contractName
    27: ) external payable returns (address addr) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol

    File: tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol	

    94: function swap(
    95:     SwapData calldata swapData,
    96:     uint256 amountOutMin,
    97:     address to,
    98:     bytes memory data
    99: ) external override returns (uint256 amountOut, uint256 shareOut) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol  

    File: tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol	

    107: function swap(
    108:     SwapData calldata swapData,
    109:     uint256 amountOutMin,
    110:     address to,
    111:     bytes memory data
    112: )
    113:     external

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol

    File: tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol	

    142: function swap(
    143:     SwapData calldata swapData,
    144:     uint256 amountOutMin,
    145:     address to,
    146:     bytes memory data
    147: ) external override returns (uint256 amountOut, uint256 shareOut) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol

    File: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol 💰 📤 ♻️	

    24: function withdrawToChain(
    25:     IYieldBoxBase yieldBox,
    26:     address from,
    27:     uint256 assetId,
    28:     uint16 dstChainId,
    29:     bytes32 receiver,
    30:     uint256 amount,
    31:     uint256 share,
    32:     bytes memory adapterParams,
    33:     address payable refundAddress,
    34:     uint256 gas
    35: ) external payable {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol

    File: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol 🖥 💰 👥 Σ	

    732: function withdrawToChain(
    733:     IYieldBoxBase yieldBox,
    734:     address from,
    735:     uint256 assetId,
    736:     uint16 dstChainId,
    737:     bytes32 receiver,
    738:     uint256 amount,
    739:     uint256 share,
    740:     bytes memory adapterParams,
    741:     address payable refundAddress,
    742:     uint256 gas
    743: ) external payable {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized("Naman");
            c1.optimized("Naman");
        }
    }
    
    contract Contract0 {
    
         function not_optimized(string memory a) public returns(string memory){
             return a;
         }
    }
    
    contract Contract1 {
    
         function optimized(string calldata a) public returns(string calldata){
             return a;
         }
    }

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 100747                                    | 535             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 790             | 790 | 790    | 790 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 66917                                     | 366             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 556             | 556 | 556    | 556 | 1       |

## [G-02] Use assembly to write address storage values

When writing value for variables whose type is address, make use of assembly code instead of solidity code.

    File: tapioca-bar-audit/contracts/Penrose.sol 🖥 💰 📤 🌀 Σ	

    94: owner = _owner;

    264: bigBangEthMarket = _market;

    284: conservator = _conservator;

    456: feeTo = feeTo_;

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol

    File: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol 💰 👥	

    98: owner = address(penrose);

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol

    File: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol 📤 👥	

    129: owner = address(penrose);

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
 
    File: tapioca-bar-audit/contracts/markets/Market.sol 🖥	

    189: conservator = _conservator;

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol

    File: tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol 🖥 💰	

    61: erc20 = _erc20;

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFTStorage.sol

    File: tap-token-audit/contracts/options/oTAP.sol	

    128: broker = msg.sender;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol

    File: tap-token-audit/contracts/option-airdrop/aoTAP.sol	

    42: owner = _owner;

    141: broker = msg.sender;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol

    File: tap-token-audit/contracts/Vesting.sol	

    72: owner = _owner;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol

    File: tap-token-audit/contracts/tokens/TapOFT.sol	

    163: minter = _minter;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol

    File: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol 📤 🧮 Σ	

    75: owner = _owner;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol

    File: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol 📤 🧮 Σ	

    105: paymentTokenBeneficiary = _paymentTokenBeneficiary;

    109: owner = _owner;

    360: paymentTokenBeneficiary = _paymentTokenBeneficiary;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol

    File: tap-token-audit/contracts/options/TapiocaOptionBroker.sol 📤 Σ	

    89: paymentTokenBeneficiary = _paymentTokenBeneficiary;

    94: owner = _owner;

    474: paymentTokenBeneficiary = _paymentTokenBeneficiary;

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol

    File: tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol 💰	

    79: stgEthPool = _stgEthPool;

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol

    File: tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol 🧪 ♻️	

    82: feeRecipient = owner;

    114: feeRecipient = recipient;

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
    
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
    
        function testGas() public {
            c0.setOwnerAssembly(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
            c1.setOwner(0xFD2dabe9DFcc4d88a12A9D0D40D834E81217Cccf);
        }
    }
    
    contract Contract0 {
    
        address owner;
        function setOwnerAssembly(address _owner) public {
            assembly{
                sstore(owner.slot,_owner)
            }
        }
    
    }
    
    contract Contract1 {
        address owner;
        function setOwner(address _owner) public {
            owner = _owner;
        }
    
    }

#### Gas Report

|  Contract0 contract                       |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 35287                                     | 207             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| setOwnerAssembly                          | 22324           | 22324 | 22324  | 22324 | 1       |


|  Contract1 contract                       |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 48499                                     | 273             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| setOwner                                  | 22363           | 22363 | 22363  | 22363 | 1       |

## [G-03] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

    File: tapioca-bar-audit/contracts/usd0/BaseUSDO.sol 💰 👥	

    370: if (!success && !_forwardRevert) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

    File: tapiocaz-audit/contracts/TapiocaWrapper.sol 💰 🧮	

    121: if (_revertOnFailure && !success) {

    144: if (_call[i].revertOnFailure && !success) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol

    File: tapiocaz-audit/contracts/Balancer.sol 💰	

    226: if (!isNative && _ercData.length == 0) revert PoolInfoRequired();  

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol

    File: tapiocaz-audit/contracts/tOFT/BaseTOFT.sol 💰 👥	   

    412: if (!success && !_forwardRevert) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol

    File: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol 📤 🧮 Σ	

    314: } else if (
    315:     _singularities[i] == sglAssetID && i < sglLastIndex
    316: ) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol

    File: tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol 💰 📤 ♻️	

    402: if (lendAmount == 0 && depositData.deposit) {

    611: if (
    612:     !removeAndRepayData.assetWithdrawData.withdraw &&
    613:     removeAndRepayData.repayAssetOnBB
    614: ) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol

    File: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol 🖥 💰 👥 Σ	

    1046: if (!success && !allowFailure) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol

    File: tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol	

    171: if (daysPassed && balanceOfStkAave > 0) {

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol

    File: YieldBox/contracts/BoringMath.sol	

    27: if (roundUp && (result * div) / mul < value) {

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol

    File: YieldBox/contracts/YieldBoxRebase.sol 🧪	

    35: if (roundUp && (share * totalAmount) / totalShares_ < amount) {

    58: if (roundUp && (amount * totalShares_) / totalAmount < share) {

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol

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

#### Gas Report

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

## [G-04] Using XOR (^) and AND (&) bitwise equivalents

Given 4 variables a, b, c and d represented as such:

    0 0 0 0 0 1 1 0 <- a
    0 1 1 0 0 1 1 0 <- b
    0 0 0 0 0 0 0 0 <- c
    1 1 1 1 1 1 1 1 <- d

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

    File: tapioca-bar-audit/contracts/markets/Market.sol 🖥	

    485: if (numerator == 0 || denominator == 0) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol

    File: tap-token-audit/contracts/Vesting.sol	

    111: if (start == 0 || seeded == 0) revert NotStarted();

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol
 
    File: tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol	

    112: if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol

    File: YieldBox/contracts/YieldBox.sol 🧪 💰	

    435: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

    448: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

    459: if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol

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

#### Gas Report

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

## [G-05] Instead of counting down in for statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

    File: tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol 💰 👥 ♻️ Σ	

    244: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol

    File: tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol 💰 👥 ♻️ Σ	

    245: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol

    File: tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol 💰 👥 ♻️ Σ	

    255: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol	

    File: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol 📤	

    45: for (uint256 i = 0; i < maxBorrowParts.length; i++) {

    107: for (uint256 i = 0; i < users.length; i++) {

    384: for (uint256 i = 0; i < users.length; i++) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol

    File: tapioca-bar-audit/contracts/Penrose.sol 🖥 💰 📤 🌀 Σ	

    437: for (uint256 i = 0; i < len; ) {
    448:    ++i;

    492: for (uint256 i = 0; i < length; ) {
    494:    ++i;

    550: for (uint256 i = 0; i < _masterContractLength; ) {

    564: for (uint256 i = 0; i < _masterContractLength; ) {

    569: for (uint256 j = 0; j < clonesOfLength; ) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol

    File: tapioca-bar-audit/contracts/markets/singularity/Singularity.sol 💰 👥	

    187: for (uint256 i = 0; i < calls.length; i++) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol

    File: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol 📤 👥	

    215: for (uint256 i = 0; i < calls.length; i++) {

    666: for (uint256 i = 0; i < users.length; i++) {

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol

    File: tapiocaz-audit/contracts/TapiocaWrapper.sol 💰 🧮	

    98: for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {

    140: for (uint256 i = 0; i < _call.length; i++) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol

    File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol 💰 👥 ♻️ Σ	

    261: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol

    File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol 💰 👥 ♻️ Σ	

    260: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol

    File: tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol 💰 👥 ♻️ Σ	

    285: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol

    File: tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol 📤 🧮 Σ	

    136: for (uint256 i = 0; i < len; ++i) { 

    308: for (uint256 i = 0; i < sglLength; i++) {

    339: for (uint256 i = 0; i < len; i++) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol

    File: tap-token-audit/contracts/option-airdrop/AirdropBroker.sol 📤 🧮 Σ	

    332: for (uint256 i = 0; i < _users.length; i++) {

    336: for (uint256 i = 0; i < _users.length; i++) {

    375: for (uint256 i = 0; i < len; ++i) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol

    File: tap-token-audit/contracts/governance/twTAP.sol 🖥 📤 Σ	

    196: for (uint256 i = 0; i < len; ) {

    413: for (uint256 i = 0; i < len; ) {

    487: for (uint256 i = 0; i < len; ++i) {
 
    507: for (uint256 i = 0; i < len; ) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol

    File: tap-token-audit/contracts/options/TapiocaOptionBroker.sol 📤 Σ	

    489: for (uint256 i = 0; i < len; ++i) {

    565: for (uint256 i = 0; i < len; ++i) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol

    File: tap-token-audit/contracts/tokens/BaseTapOFT.sol 💰 ♻️ Σ	
 
    228: for (uint i = 0; i < len; ) { 

    333: for (uint256 i = 0; i < approvals.length; ) {

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

    File: tapioca-periph-audit/contracts/Multicall/Multicall3.sol 🖥 💰 Σ	

    47: for (uint256 i = 0; i < length; ) {

    70: for (uint256 i = 0; i < length; ) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol

    File: tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol 🖥 💰 👥 Σ	

    202: for (uint256 i = 0; i < length; i++) {

    973: for (uint256 i = 0; i < len; i++) {

    1004: for (uint256 i = 0; i < len; i++) {

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol

    File: tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol 💰	

    156: for (uint256 i = 0; i < poolTokens.length; i++) {

    225: for (uint256 i = 0; i < poolTokens.length; i++) {

    277: for (uint256 i = 0; i < poolTokens.length; i++) {

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol

    File: tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol 🧮	

    195: for (uint256 i = 0; i < tokens.length; i++) {

    255: for (uint256 i = 0; i < tempData.tokens.length; i++) {

    273: for (uint256 i = 0; i < tempData.tokens.length; i++) {

    280: for (uint256 i = 0; i < tempData.tokens.length; i++) {

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol

    File: YieldBox/contracts/NativeTokenFactory.sol	

    129: for (uint256 i = 0; i < len; i++) {

    141: for (uint256 i = 0; i < len; i++) {

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol

    File: YieldBox/contracts/YieldBox.sol 🧪 💰	

    309: for (uint256 i = 0; i < len; i++) {

    320: for (uint256 i = 0; i < len; i++) {

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol

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

#### Gas Report

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