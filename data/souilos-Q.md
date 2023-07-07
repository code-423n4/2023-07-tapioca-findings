# VULN 1 

## [LOW] Empty receive()/payable fallback() function does not authenticate requests
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 68 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    receive() external payable {}


Found in line 644 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

    receive() external payable {}


Found in line 342 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    receive() external payable {}


Found in line 43 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    receive() external payable {}


Found in line 340 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    receive() external payable virtual {}


Found in line 271 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    receive() external payable {}


Found in line 164 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    receive() external payable {}


Found in line 169 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    receive() external payable {}


Found in line 301 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    receive() external payable {}


Found in line 330 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    receive() external payable virtual {}

------------------------------------------------------------------------ 

### Mitigation 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))).Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas. Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.










# VULN 2 

## [LOW] Division before multiplication causing significant loss of precision
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 265 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            borrowPartScaled = borrowPart / (10 ** (borrowPartDecimals - 18));


Found in line 555 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        paymentAmount = paymentAmount / (10 ** (18 - _paymentTokenDecimals));


Found in line 535 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        paymentAmount = paymentAmount / (10 ** (18 - _paymentTokenDecimals));

------------------------------------------------------------------------ 

### Mitigation 

Multiply first before dividing to keep the precision.










# VULN 3 

## [LOW] Use .call instead of .transfer to send ether
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 514 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            yieldBox.transfer(


Found in line 536 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            yieldBox.transfer(address(this), feeTo, assetId, feeShares);


Found in line 349 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            yieldBox.transfer(from, address(swapper), assetId, supplyShare);


Found in line 596 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(


Found in line 648 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);


Found in line 649 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 704 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            yieldBox.transfer(from, address(this), _tokenId, share);


Found in line 717 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), to, collateralId, share);


Found in line 160 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

            yieldBox.transfer(from, address(swapper), assetId, supplyShare);


Found in line 192 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

            yieldBox.transfer(from, address(this), _assetId, share);


Found in line 243 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        yieldBox.transfer(address(this), to, assetId, share);


Found in line 166 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(


Found in line 193 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 281 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);


Found in line 282 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 330 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(


Found in line 49 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        yieldBox.transfer(address(this), to, collateralId, share);


Found in line 79 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        yieldBox.transfer(address(this), to, assetId, share);


Found in line 624 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                yieldBox.transfer(


Found in line 186 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);


Found in line 241 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        yieldBox.transfer(


Found in line 491 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                paymentToken.transfer(


Found in line 377 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

                paymentToken.transfer(


Found in line 514 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        tapOFT.transfer(msg.sender, tapAmount);


Found in line 50 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

        tapToken.transfer(msg.sender, amount);


Found in line 565 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        tapOFT.transfer(_to, releasedAmount);

------------------------------------------------------------------------ 

### Mitigation 

.transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues.Replace .transfer with .call. Note that the result of .call need to be checked.










# VULN 4 

## [LOW] Use the safe variant and ERC721.mint
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 91 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        _mint(address(receiver), amount);


Found in line 111 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        _mint(_to, _amount);


Found in line 110 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        _mint(to, tokenId, amount);


Found in line 130 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

            _mint(tos[i], tokenId, amounts[i]);


Found in line 360 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        _mint(_toAddress, _amount);


Found in line 365 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        _mint(_toAddress, msg.value);


Found in line 43 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

        _mint(msg.sender, amount);


Found in line 121 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_contributors, 1e18 * 15_000_000);


Found in line 122 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_earlySupporters, 1e18 * 3_686_595);


Found in line 123 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_supporters, 1e18 * 12_500_000);


Found in line 124 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_lbp, 1e18 * 5_000_000);


Found in line 125 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_dao, 1e18 * 8_000_000);


Found in line 126 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            _mint(_airdrop, 1e18 * 2_500_000);


Found in line 226 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        _mint(_to, _amount);

------------------------------------------------------------------------ 

### Mitigation 

.mint won’t check if the recipient is able to receive the NFT. If an incorrect address is passed, it will result in a silent failure and loss of asset. OpenZeppelin recommendation is to use the safe variant of _mint. Replace _mint() with _safeMint().










# VULN 5 

## [LOW] Usage of deprecated chainlink API
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 51 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

            uint256(UNDERLYING.latestAnswer())) / SG_POOL.totalSupply();


Found in line 121 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

        uint256 _btcPrice = uint256(BTC_FEED.latestAnswer()) * 1e10;


Found in line 122 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

        uint256 _wbtcPrice = uint256(WBTC_FEED.latestAnswer()) * 1e10;


Found in line 123 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

        uint256 _ethPrice = uint256(ETH_FEED.latestAnswer()) * 1e10;


Found in line 124 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

        uint256 _usdtPrice = uint256(USDT_FEED.latestAnswer()) * 1e10;

------------------------------------------------------------------------ 

### Mitigation 

Use latestRoundData() instead of latestAnswer(). Also, adding checks for additional fields returned from latestRoundData() is recommended. https://docs.chain.link/data-feeds/price-feeds/api-reference/#latestrounddata 










# VULN 6 

## [LOW] Open TODOs
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 238 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            // (TODO: Check that the multiplication does not overflow!)


Found in line 241 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            // (TODO: Check edge case where the average calculation drops to


Found in line 331 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        // TODO: Check the cast?


Found in line 26 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

//TODO: decide if we need to start with ETH and wrap it into WETH; stargate allows ETH. not WETH, while others allow WETH, not ETH


Found in line 27 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

//TODO: handle rewards deposits to yieldbox


Found in line 200 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        // uint256 compoundAmount = compoundAmount();//TODO: not view


Found in line 31 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

//TODO: decide if we need to start with ETH and wrap it into WETH; stargate allows ETH. not WETH, while others allow WETH, not ETH


Found in line 32 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

//TODO: handle rewards deposits to yieldbox


Found in line 29 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

//TODO: update withdrawal and currentBalance after Lido allows withdrawals


Found in line 233 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            //    (TODO: Word better?)


Found in line 289 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                // TODO: Strongly suspect this is never less. Prove it.


Found in line 347 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        // TODO: Mint event?


Found in line 398 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        // TODO: Make whole function unchecked


Found in line 411 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            // TODO: Prove that math is safe


Found in line 444 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        // TODO: Word this better

------------------------------------------------------------------------ 

### Mitigation 

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.










# VULN 7 

## [LOW] Use safeTransferOwnership instead of transferOwnership function
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 79 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        transferOwnership(_owner);


Found in line 56 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

    function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {


Found in line 45 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        transferOwnership(_owner);


Found in line 134 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        transferOwnership(_conservator);


Found in line 126 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        transferOwnership(_owner);

------------------------------------------------------------------------ 

### Mitigation 

Use a 2 structure transferOwnership which is safer. safeTransferOwnership, use it is more secure due to 2-stage ownership transfer. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol










# VULN 8 

## [LOW] safeApprove of openZeppelin is deprecated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 98 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

    function _safeApprove(address token, address to, uint256 value) internal {


Found in line 104 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

            "BaseSwapper::safeApprove: approve failed"


Found in line 98 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

    function _safeApprove(address token, address to, uint256 value) internal {


Found in line 104 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

            "BaseSwapper::safeApprove: approve failed"


Found in line 172 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

        TransferHelper.safeApprove(tokenIn, address(swapRouter), amountIn);


Found in line 197 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

            _safeApprove(tokenOut, address(yieldBox), amountOut);


Found in line 146 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

        _safeApprove(tokenIn, address(swapRouter), amountIn);


Found in line 162 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

            _safeApprove(path[path.length - 1], address(yieldBox), amountOut);


Found in line 131 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

            _safeApprove(tokenOut, address(yieldBox), amountOut);


Found in line 160 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        _safeApprove(tokenIn, address(curvePool), amountIn);


Found in line 197 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

                _safeApprove(tokens[i], address(swapper), rewards[i]);


Found in line 354 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function _safeApprove(address token, address to, uint256 value) private {


Found in line 365 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            "OperationsLib::safeApprove: approval reset failed"


Found in line 378 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            "OperationsLib::safeApprove: approve failed"

------------------------------------------------------------------------ 

### Mitigation 

Deprecated, its usage is discouraged. https://docs.openzeppelin.com/contracts/3.x/api/token/erc20. Whenever possible, use safeIncreaseAllowance and safeDecreaseAllowance instead. https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20-safeIncreaseAllowance-contract-IERC20-address-uint256- & https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20-safeDecreaseAllowance-contract-IERC20-address-uint256-










# VULN 9 

## [LOW] Named imports can be used
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 10 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

import "./MarketERC20.sol";

------------------------------------------------------------------------ 

### Mitigation 

It’s possible to name the imports to improve code readability. E.g. import “@openzeppelin/contracts/token/ERC20/IERC20.sol”; can be rewritten as import {IERC20} from `import `@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol` 










# VULN 10 

## [LOW] Add parameter to event-emit
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 98 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

    event OracleDataUpdated();


Found in line 100 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

    event OracleUpdated();

------------------------------------------------------------------------ 

### Mitigation 

Some event-emit description doesn’t have parameter. Add parameter for front-end website or client app, they can has that something has happened on the blockchain.










# VULN 11 

## [LOW] Immutables should be in uppercase
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 41 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    YieldBox public immutable yieldBox;


Found in line 43 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    IERC20 public immutable tapToken;


Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    uint256 public immutable tapAssetId;


Found in line 51 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    IERC20 public immutable wethToken;


Found in line 53 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    uint256 public immutable wethAssetId;


Found in line 19 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    IYieldBoxBase public immutable yieldBox;


Found in line 59 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    IStargateRouter public immutable routerETH;


Found in line 61 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    IStargateRouter public immutable router;


Found in line 10 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

    uint8 public immutable override decimals;


Found in line 27 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

    IStargatePool public immutable SG_POOL;


Found in line 28 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

    AggregatorV2V3Interface public immutable UNDERLYING;


Found in line 8 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol:

    IGmxGlpManager private immutable glpManager;


Found in line 34 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    ICurvePool public immutable TRI_CRYPTO;


Found in line 35 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    AggregatorV2V3Interface public immutable BTC_FEED;


Found in line 36 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    AggregatorV2V3Interface public immutable ETH_FEED;


Found in line 37 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    AggregatorV2V3Interface public immutable USDT_FEED;


Found in line 38 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    AggregatorV2V3Interface public immutable WBTC_FEED;


Found in line 34 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    IYieldBox private immutable yieldBox;


Found in line 35 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    ISwapRouter public immutable swapRouter;


Found in line 36 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    IUniswapV3Factory public immutable factory;


Found in line 26 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

    IUniswapV2Router02 public immutable swapRouter;


Found in line 27 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

    IUniswapV2Factory public immutable factory;


Found in line 28 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

    IYieldBox public immutable yieldBox;


Found in line 29 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

    IYieldBox public immutable yieldBox;


Found in line 30 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IERC20 private immutable gmx;


Found in line 31 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IERC20 private immutable esGmx;


Found in line 32 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IERC20 private immutable weth;


Found in line 34 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxRewardTracker private immutable feeGmxTracker;


Found in line 35 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGlpManager private immutable glpManager;


Found in line 36 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxRewardRouterV2 private immutable glpRewardRouter;


Found in line 37 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxRewardRouterV2 private immutable gmxRewardRouter;


Found in line 38 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxVester private immutable glpVester;


Found in line 39 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxVester private immutable gmxVester;


Found in line 40 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxRewardTracker private immutable stakedGlpTracker;


Found in line 41 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    IGmxRewardTracker private immutable stakedGmxTracker;


Found in line 41 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 45 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IConvexBooster public immutable booster;


Found in line 46 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IConvexRewardPool public immutable rewardPool;


Found in line 47 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IConvexZap public immutable zap;


Found in line 48 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    uint256 public immutable pid;


Found in line 50 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IERC20 public immutable lpToken;


Found in line 52 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    IERC20 public immutable rewardToken;


Found in line 36 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 37 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    IYearnVault public immutable vault;


Found in line 40 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    IERC20 public immutable lpToken;


Found in line 41 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 44 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    ITricryptoLPGauge public immutable lpGauge;


Found in line 45 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    ICurveMinter public immutable minter;


Found in line 47 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    IERC20 public immutable rewardToken; //CRV token


Found in line 40 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 43 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    ITricryptoLPGauge public immutable lpGauge;


Found in line 44 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    ICurveMinter public immutable minter;


Found in line 46 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    IERC20 public immutable rewardToken; //CRV token


Found in line 41 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 45 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    IRouterETH public immutable addLiquidityRouter;


Found in line 46 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    IRouter public immutable router;


Found in line 47 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    ILPStaking public immutable lpStaking;


Found in line 35 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 39 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    IStkAave public immutable stakedRewardToken;


Found in line 40 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    IERC20 public immutable rewardToken;


Found in line 41 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    IERC20 public immutable receiptToken;


Found in line 42 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    ILendingPool public immutable lendingPool;


Found in line 43 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    IIncentivesController public immutable incentivesController;


Found in line 34 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 36 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    ICToken public immutable cToken;


Found in line 36 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 37 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    IStEth public immutable stEth;


Found in line 34 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    IERC20 public immutable wrappedNative;


Found in line 35 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    IERC20 public immutable bal;


Found in line 38 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    IBalancerVault public immutable vault;


Found in line 39 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    IBalancerPool public immutable pool; //lp token


Found in line 40 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    IBalancerHelpers public immutable helpers;


Found in line 58 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

    IYieldBox public immutable yieldBox;


Found in line 54 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    TapiocaOptionLiquidityProvision public immutable tOLP;


Found in line 56 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    TapOFT public immutable tapOFT;


Found in line 57 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    OTAP public immutable oTAP;


Found in line 78 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    uint256 public immutable EPOCH_DURATION; // 7 days = 604800


Found in line 45 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    TapOFT public immutable tapOFT;


Found in line 46 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    AOTAP public immutable aoTAP;


Found in line 48 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    IERC721 public immutable PCNFT;


Found in line 53 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    uint256 public immutable emissionsStartTime;


Found in line 74 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    TapOFT public immutable tapOFT;


Found in line 111 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 public immutable HOST_CHAIN_ID;

------------------------------------------------------------------------ 

### Mitigation 

Immutables should be in uppercase, it helps to distinguish immutables from other types of variables and provides better code readability.
