# VULN 1 

## [GAS] Use != 0 instead of > 0 for unsigned integer comparison
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 89 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(amount > 0, "USDO: amount not valid");


Found in line 171 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_borrowOpeningFee > 0) {


Found in line 192 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_callerFee > 0) {


Found in line 197 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_protocolFee > 0) {


Found in line 202 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_liquidationBonusAmount > 0) {


Found in line 210 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_minLiquidatorReward > 0) {


Found in line 219 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_maxLiquidatorReward > 0) {


Found in line 228 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_totalBorrowCap > 0) {


Found in line 233 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_collateralizationRate > 0) {


Found in line 344 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(rate > 0, "Market: invalid rate");


Found in line 152 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0


Found in line 348 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        if (supplyShare > 0) {


Found in line 449 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        if (totalFees > 0) {


Found in line 475 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            if (_minDebtRate > 0) {


Found in line 481 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            if (_maxDebtRate > 0) {


Found in line 487 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            if (_debtRateAgainstEthMarket > 0) {


Found in line 495 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            if (_liquidationMultiplier > 0) {


Found in line 680 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 734 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        if (toBurn > 0) {


Found in line 159 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        if (supplyShare > 0) {


Found in line 131 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0


Found in line 480 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (accrueInfo.feesEarnedFraction > 0) {


Found in line 498 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_minimumTargetUtilization > 0) {


Found in line 506 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_maximumTargetUtilization > 0) {


Found in line 521 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_minimumInterestPerSecond > 0) {


Found in line 533 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_maximumInterestPerSecond > 0) {


Found in line 545 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_interestElasticity > 0) {


Found in line 553 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_lqCollateralizationRate > 0) {


Found in line 565 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (_liquidationMultiplier > 0) {


Found in line 233 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        if (liquidationBonusAmount > 0) {


Found in line 397 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 149 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;


Found in line 364 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        require(msg.value > 0, "TOFT_0");


Found in line 56 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 181 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        _amount = _share > 0


Found in line 127 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

        if (amounts.amountIn > 0 || amounts.amountOut > 0) {


Found in line 131 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

            if (tokenInId > 0) {


Found in line 136 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

            if (tokenOutId > 0) {


Found in line 235 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                if (_action.value > 0) {


Found in line 171 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (collateralAmount > 0) {


Found in line 184 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (borrowAmount > 0) {


Found in line 228 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (depositAmount > 0) {


Found in line 245 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (repayAmount > 0) {


Found in line 248 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                depositAmount > 0 ? address(this) : user,


Found in line 258 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (collateralAmount > 0) {


Found in line 353 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            if (mintData.collateralDepositData.amount > 0) {


Found in line 405 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        if (lendAmount > 0) {


Found in line 416 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            if (lockData.fraction > 0) {


Found in line 511 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                removeAndRepayData.exitData.oTAPTokenID > 0,


Found in line 717 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            refundAddress: msg.value > 0 ? refundAddress : payable(this),


Found in line 751 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 gas = msg.value > 0 ? msg.value : address(this).balance;


Found in line 761 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            gas > 0 ? payable(msg.sender) : payable(this),


Found in line 127 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

        if (amounts.amountIn > 0 || amounts.amountOut > 0) {


Found in line 131 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

            if (tokenInId > 0) {


Found in line 136 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

            if (tokenOutId > 0) {


Found in line 119 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        if (feeAmount > 0) {


Found in line 133 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        if (claimable > 0) {


Found in line 196 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            if (rewards[i] > 0) {


Found in line 113 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        if (claimable > 0) {


Found in line 162 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        if (claimable > 0) {


Found in line 249 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        if (queued > 0) {


Found in line 106 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        if (claimable > 0) {


Found in line 153 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        if (claimable > 0) {


Found in line 241 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        if (queued > 0) {


Found in line 122 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        if (claimable > 0) {


Found in line 165 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        if (unclaimed > 0) {


Found in line 103 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        if (claimable > 0) {


Found in line 145 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        if (unclaimedStkAave > 0) {


Found in line 158 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        if (claimable > 0) {


Found in line 168 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        if (currentCooldown > 0) {


Found in line 171 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            if (daysPassed && balanceOfStkAave > 0) {


Found in line 174 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        } else if (balanceOfStkAave > 0) {


Found in line 120 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 calcEth = stEthBalance > 0


Found in line 12 at 2023-07-tapioca/tap-token-audit/contracts/twAML.sol:

        require(denominator > 0);


Found in line 68 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        require(_duration > 0, "Vesting: no vesting");


Found in line 131 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        if (start > 0) revert Initialized();


Found in line 134 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        if (users[_user].amount > 0) revert AlreadyRegistered();


Found in line 152 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        if (start > 0) revert Initialized();


Found in line 177 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_lockDuration > 0, "tOLP: lock duration must be > 0");


Found in line 178 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_amount > 0, "tOLP: amount must be > 0");


Found in line 181 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(sglAssetID > 0, "tOLP: singularity not active");


Found in line 264 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            activeSingularities[singularity].sglAssetID > 0,


Found in line 281 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(assetID > 0, "tOLP: invalid asset ID");


Found in line 288 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        activeSingularities[singularity].poolWeight = weight > 0 ? weight : 1;


Found in line 301 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(sglAssetID > 0, "tOLP: not registered");


Found in line 203 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(cachedEpoch > 0, "adb: Airdrop not started");


Found in line 392 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(_eligibleAmount > 0, "adb: Not eligible");


Found in line 467 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(_eligibleAmount > 0, "adb: Not eligible");


Found in line 104 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(duration > 0, "TapOFT: Small duration");


Found in line 201 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        if (emissionForWeek[week] > 0) return 0;


Found in line 222 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_amount > 0, "amount not valid");


Found in line 489 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                if (amount > 0) {


Found in line 511 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                if (amount > 0) {

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 for unsigned integer comparison.










# VULN 2 

## [GAS] Use shift Right/Left instead of division/multiplication if possible
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 78 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

        maxFlashMint = 100_000 * 1e18; // 100k USDO


Found in line 489 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        uint256 _quotient = ((_numerator / denominator) + 5) / 10;


Found in line 521 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        _accrueInfo.debtRate = uint64(annumDebtRate / 31536000); //per second


Found in line 133 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

                newInterestPerSecond = maximumInterestPerSecond; // 1000% APR maximum


Found in line 57 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

            _decimal / 2,


Found in line 171 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            uint256 fee = (wethAmount * FEE_BPS) / 10_000;


Found in line 310 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 buffer = (freeEsGmx + stakedEsGmx) / 20;


Found in line 143 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            result = result - (result * 50) / 10_000; //0.5%


Found in line 154 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 minAmount = (calcWithdraw * 50) / 10_000; //0.5%


Found in line 207 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

                uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 313 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 336 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%


Found in line 179 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

                uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 186 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

                minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 116 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            result = result - (result * 50) / 10_000; //0.5%


Found in line 170 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

                uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 188 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%


Found in line 232 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%


Found in line 249 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 133 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            result = result - (result * 50) / 10_000; //0.5%


Found in line 182 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

                uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 113 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            result = result - (result * 50) / 10_000; //0.5%


Found in line 193 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%


Found in line 108 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 minAmount = (toWithdraw * 50) / 10_000; //0.5%


Found in line 151 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

            uint256 minAmount = toWithdraw - (toWithdraw * 250) / 10_000; //2.5%


Found in line 122 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        toWithdraw = toWithdraw - (toWithdraw * 50) / 10_000; //0.5%


Found in line 177 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        bptOut = bptOut - (bptOut * 50) / 10_000; //0.5%


Found in line 250 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        bptIn = bptIn + (bptIn * 250) / 10_000; //2.5%


Found in line 150 at 2023-07-tapioca/tap-token-audit/contracts/twAML.sol:

            uint256 x = y / 2 + 1;


Found in line 153 at 2023-07-tapioca/tap-token-audit/contracts/twAML.sol:

                x = (y / x + x) / 2;

------------------------------------------------------------------------ 

### Mitigation 

Use shift Right/Left instead of division/multiplication if possible.










# VULN 3 

## [GAS] ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 248 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        current = _nonces[owner]++;


Found in line 215 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 666 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 669 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                liquidatedCount++;


Found in line 187 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                for (uint256 i = 0; i < maxBorrowParts.length; i++) {


Found in line 107 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 384 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 387 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                liquidatedCount++;


Found in line 28 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

            result++;


Found in line 28 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

        totalAmount++;


Found in line 36 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

            share++;


Found in line 51 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

        totalAmount++;


Found in line 59 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

            amount++;


Found in line 129 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 141 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {


Found in line 140 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < _call.length; i++) {


Found in line 202 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < length; i++) {


Found in line 973 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 1004 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 195 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tokens.length; i++) {


Found in line 255 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 273 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 280 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 225 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 277 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 308 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            for (uint256 i = 0; i < sglLength; i++) {


Found in line 339 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 243 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

            pool.totalParticipants++; // Save participation


Found in line 423 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        epoch++;


Found in line 288 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        epoch++;


Found in line 332 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 336 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 278 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            pool.totalParticipants++; // Save participation

------------------------------------------------------------------------ 

### Mitigation 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).










# VULN 4 

## [GAS] Don’t initialize variables with default value
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 437 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        for (uint256 i = 0; i < len; ) {


Found in line 492 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            for (uint256 i = 0; i < length; ) {


Found in line 512 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        uint256 amount = 0;


Found in line 546 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        uint256 marketsLength = 0;


Found in line 550 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            for (uint256 i = 0; i < _masterContractLength; ) {


Found in line 564 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            for (uint256 i = 0; i < _masterContractLength; ) {


Found in line 569 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

                for (uint256 j = 0; j < clonesOfLength; ) {


Found in line 244 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 245 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 255 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 215 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 527 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 extraAmount = 0;


Found in line 603 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 minAssetMount = 0;


Found in line 665 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 liquidatedCount = 0;


Found in line 666 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 187 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 44 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                uint256 needed = 0;


Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                for (uint256 i = 0; i < maxBorrowParts.length; i++) {


Found in line 107 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 337 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        uint256 minAssetAmount = 0;


Found in line 383 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        uint256 liquidatedCount = 0;


Found in line 384 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 129 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 141 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {


Found in line 140 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < _call.length; i++) {


Found in line 261 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 260 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 285 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 202 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < length; i++) {


Found in line 973 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 1004 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 401 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 fraction = 0;


Found in line 414 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 tOLPTokenId = 0;


Found in line 508 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 tOLPId = 0;


Found in line 571 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 _removeAmount = 0;


Found in line 47 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        for (uint256 i = 0; i < length; ) {


Found in line 70 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        for (uint256 i = 0; i < length; ) {


Found in line 195 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tokens.length; i++) {


Found in line 255 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 273 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 280 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 109 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 claimable = 0;


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 225 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 277 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 29 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

    uint256 public seeded = 0;


Found in line 136 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            for (uint256 i = 0; i < len; ++i) {


Found in line 308 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            for (uint256 i = 0; i < sglLength; i++) {


Found in line 339 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        for (uint256 i = 0; i < len; i++) {


Found in line 489 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

            for (uint256 i = 0; i < len; ++i) {


Found in line 565 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

            for (uint256 i = 0; i < len; ++i) {


Found in line 332 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 336 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 375 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < len; ++i) {


Found in line 228 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            for (uint i = 0; i < len; ) {


Found in line 333 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 196 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        for (uint256 i = 0; i < len; ) {


Found in line 413 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            for (uint256 i = 0; i < len; ) {


Found in line 487 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            for (uint256 i = 0; i < len; ++i) {


Found in line 507 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            for (uint256 i = 0; i < len; ) {

------------------------------------------------------------------------ 

### Mitigation 

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.










# VULN 5 

## [GAS] Use Custom Errors
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 190 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(!paused, "Penrose: paused");


Found in line 242 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(address(swappers_[0]) != address(0), "Penrose: zero address");


Found in line 243 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(address(markets_[0]) != address(0), "Penrose: zero address");


Found in line 272 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(msg.sender == conservator, "Penrose: unauthorized");


Found in line 273 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(val != paused, "Penrose: same state");


Found in line 282 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(_conservator != address(0), "Penrose: address not valid");


Found in line 505 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(swappers[swapper], "Penrose: Invalid swapper");


Found in line 506 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(isMarketRegistered[address(market)], "Penrose: Invalid market");


Found in line 26 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(!paused, "USDO: paused");


Found in line 68 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(token == address(this), "USDO: token not valid");


Found in line 87 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(token == address(this), "USDO: token not valid");


Found in line 88 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(maxFlashLoan(token) >= amount, "USDO: amount too big");


Found in line 89 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(amount > 0, "USDO: amount not valid");


Found in line 100 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(_allowance >= (amount + fee), "USDO: repay not approved");


Found in line 110 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(allowedMinter[_getChainId()][msg.sender], "USDO: unauthorized");


Found in line 119 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(allowedBurner[_getChainId()][msg.sender], "USDO: unauthorized");


Found in line 97 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");


Found in line 106 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        require(_conservator != address(0), "USDO: address not valid");


Found in line 115 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        require(msg.sender == conservator, "USDO: unauthorized");


Found in line 116 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        require(val != paused, "USDO: same state");


Found in line 355 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

            revert("USDO: module not found");


Found in line 498 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

                revert("OFTCoreV2: unknown packet type");


Found in line 116 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(!paused, "Market: paused");


Found in line 126 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(_isSolvent(from, exchangeRate), "Market: insolvent");


Found in line 131 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(!initialized, "Market: initialized");


Found in line 143 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(_val <= FEE_PRECISION, "Market: not valid");


Found in line 172 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_borrowOpeningFee <= FEE_PRECISION, "Market: not valid");


Found in line 193 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_callerFee <= FEE_PRECISION, "Market: not valid");


Found in line 198 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_protocolFee <= FEE_PRECISION, "Market: not valid");


Found in line 211 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");


Found in line 220 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_maxLiquidatorReward < FEE_PRECISION, "Market: not valid");


Found in line 246 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(msg.sender == conservator, "Market: unauthorized");


Found in line 247 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(val != paused, "Market: same state");


Found in line 344 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(rate > 0, "Market: invalid rate");


Found in line 142 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

            require(srcBalance >= amount, "ERC20: balance too low");


Found in line 144 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas


Found in line 167 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

            require(srcBalance >= amount, "ERC20: balance too low");


Found in line 179 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                require(to != address(0), "ERC20: no zero address"); // Moved down so other failed calls safe some gas


Found in line 261 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        require(block.timestamp <= deadline, "ERC20Permit: expired deadline");


Found in line 277 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        require(signer == owner, "ERC20Permit: invalid signature");


Found in line 344 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 371 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 391 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 413 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 476 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                require(_minDebtRate < maxDebtRate, "BigBang: not valid");


Found in line 482 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                require(_maxDebtRate > minDebtRate, "BigBang: not valid");


Found in line 593 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "BigBang: Invalid swapper");


Found in line 680 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 820 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(borrowAmount != 0, "SGL: solvent");


Found in line 104 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 126 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 155 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 182 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 240 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        require(_totalAsset.base >= 1000, "SGL: min limit");


Found in line 566 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(_liquidationMultiplier < FEE_PRECISION, "SGL: not valid");


Found in line 582 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(_liquidationQueue.onlyOnce(), "SGL: LQ not initalized");


Found in line 612 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            revert("SGL: module not set");


Found in line 152 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(allBorrowAmount != 0, "SGL: solvent");


Found in line 264 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(borrowAmount != 0, "SGL: solvent");


Found in line 327 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 397 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 75 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        require(_totalAsset.base >= 1000, "SGL: min limit");


Found in line 6 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

        require(a <= type(uint128).max, "BoringMath: uint128 Overflow");


Found in line 11 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

        require(a <= type(uint64).max, "BoringMath: uint64 Overflow");


Found in line 16 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

        require(a <= type(uint32).max, "BoringMath: uint32 Overflow");


Found in line 51 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");


Found in line 58 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(signer == owner, "YieldBoxPermit: invalid signature");


Found in line 80 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");


Found in line 87 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(signer == owner, "YieldBoxPermit: invalid signature");


Found in line 46 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        require(msg.sender == owner[tokenId], "NTF: caller is not the owner");


Found in line 59 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

            require(newOwner != address(0) || renounce, "NTF: zero address");


Found in line 77 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        require(msg.sender == _pendingOwner, "NTF: caller != pending owner");


Found in line 117 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");


Found in line 139 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");


Found in line 93 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        require(!balancers[msg.sender], "TOFT_auth");


Found in line 105 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        require(connectedChains[block.chainid], "TOFT_host");


Found in line 106 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        require(!balancers[msg.sender], "TOFT_auth");


Found in line 142 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        require(balancers[msg.sender], "TOFT_auth");


Found in line 46 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        require(block.chainid == hostChainID, "TOFT_host");


Found in line 364 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        require(msg.value > 0, "TOFT_0");


Found in line 381 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        require(sent, "TOFT_failed");


Found in line 397 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

            revert("TOFT_module");


Found in line 570 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

                revert("TOFT_packet");


Found in line 56 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 281 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        require(sent, "TOFT_failed");


Found in line 337 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        require(_from == msg.sender, "MagnetarV2: operator not approved");


Found in line 710 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                revert("MagnetarV2: action not valid");


Found in line 714 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        require(msg.value == valAccumulator, "MagnetarV2: value mismatch");


Found in line 1058 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

            revert("MagnetarV2: module not found");


Found in line 1080 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        if (_returnData.length < 68) revert("MagnetarV2: Reason unknown");


Found in line 468 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            require(tOLPTokenId != 0, "Magnetar: tOLPTokenId 0");


Found in line 743 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        require(withdrawData.length > 0, "MagnetarV2: withdrawData is empty");


Found in line 90 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        require(msg.value == valAccumulator, "Multicall3: value mismatch");


Found in line 96 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        if (_returnData.length < 68) revert("Reason unknown");


Found in line 156 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        require(outputAmount >= amountOutMin, "insufficient-amount-out");


Found in line 164 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        require(balanceAfter > balanceBefore, "swap failed");


Found in line 60 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(address(weth) == _gmxRewardRouter.weth(), "WETH mismatch");


Found in line 62 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router");


Found in line 66 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(_gmx != address(0), "Bad GMX reward router");


Found in line 91 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(msg.sender == address(gmxWethPool), "Not the pool");


Found in line 333 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(amountOut * priceDenom >= gmxAmount * priceNum, "Not enough");


Found in line 137 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        require(available >= amount, "YearnStrategy: amount not valid");


Found in line 234 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        require(available >= amount, "TricryptoLPStrategy: amount not valid");


Found in line 246 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        require(available >= amount, "StargateStrategy: amount not valid");


Found in line 255 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        require(available >= amount, "AaveStrategy: amount not valid");


Found in line 141 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        require(available >= amount, "CompoundStrategy: amount not valid");


Found in line 131 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

            require(!stEth.isStakingPaused(), "LidoStrategy: staking paused");


Found in line 146 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        require(available >= amount, "LidoStrategy: amount not valid");


Found in line 162 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        require(queued >= amount, "LidoStrategy: not enough");


Found in line 196 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        require(available >= amount, "BalancerStrategy: amount not valid");


Found in line 68 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        require(_duration > 0, "Vesting: no vesting");


Found in line 40 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

        require(msg.sender == broker, "OTAP: only onlyBroker");


Found in line 127 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

        require(broker == address(0), "OTAP: only once");


Found in line 177 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_lockDuration > 0, "tOLP: lock duration must be > 0");


Found in line 178 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_amount > 0, "tOLP: amount must be > 0");


Found in line 181 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(sglAssetID > 0, "tOLP: singularity not active");


Found in line 212 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_exists(_tokenId), "tOLP: Expired position");


Found in line 281 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(assetID > 0, "tOLP: invalid asset ID");


Found in line 301 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(sglAssetID > 0, "tOLP: not registered");


Found in line 175 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Option expired");


Found in line 187 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");


Found in line 190 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(tapAmount >= 1e18, "tOB: Too low");


Found in line 219 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Position is not active");


Found in line 220 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(lock.lockDuration >= EPOCH_DURATION, "tOB: Duration too short");


Found in line 297 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(oTAP.exists(_oTAPTokenID), "tOB: oTAP position does not exist");


Found in line 376 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Option expired");


Found in line 388 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");


Found in line 391 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(chosenAmount >= 1e18, "tOB: Too low");


Found in line 419 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(singularities.length > 0, "tOB: No active singularities");


Found in line 46 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

        require(msg.sender == broker, "AOTAP: only onlyBroker");


Found in line 140 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

        require(broker == address(0), "AOTAP: only once");


Found in line 158 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");


Found in line 174 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "adb: Too high");


Found in line 177 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(tapAmount >= 1e18, "adb: Too low");


Found in line 203 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(cachedEpoch > 0, "adb: Airdrop not started");


Found in line 204 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(cachedEpoch <= 4, "adb: Airdrop ended");


Found in line 233 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");


Found in line 255 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "adb: Too high");


Found in line 258 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(chosenAmount >= 1e18, "adb: Too low");


Found in line 329 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(_users.length == _amounts.length, "adb: invalid input");


Found in line 392 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(_eligibleAmount > 0, "adb: Not eligible");


Found in line 447 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(PCNFT.ownerOf(_tokenID) == msg.sender, "adb: Not eligible");


Found in line 467 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(_eligibleAmount > 0, "adb: Not eligible");


Found in line 47 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

        require(block.timestamp > lockedUntil, "Still locked");


Found in line 54 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

        require(_lockedUntil <= maxLockedUntil, "Too late");


Found in line 73 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                revert("TOFT_packet");


Found in line 104 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(duration > 0, "TapOFT: Small duration");


Found in line 222 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");


Found in line 307 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");


Found in line 89 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(!paused, "TAP: paused");


Found in line 118 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_lzEndpoint != address(0), "LZ endpoint not valid");


Found in line 153 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(val != paused, "TAP: same state");


Found in line 161 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_minter != address(0), "address not valid");


Found in line 198 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_getChainId() == governanceChainIdentifier, "chain not valid");


Found in line 221 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(msg.sender == minter, "unauthorized");


Found in line 222 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_amount > 0, "amount not valid");


Found in line 225 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(emissionForWeek[week] >= _amount, "exceeds allowable amount");


Found in line 257 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(_duration >= EPOCH_DURATION, "twTAP: Lock not a week");


Found in line 313 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(expiry < type(uint56).max, "twTAP: too long");


Found in line 365 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(msg.sender == address(tapOFT), "twTAP: only tapOFT");


Found in line 390 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(msg.sender == address(tapOFT), "twTAP: only tapOFT");


Found in line 530 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(position.expiry <= block.timestamp, "twTAP: Lock not expired");

------------------------------------------------------------------------ 

### Mitigation 

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.










# VULN 6 

## [GAS] Use calldata instead of memory for function arguments that do not get mutated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 104 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

    function remove(bytes memory _payload) public {


Found in line 243 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 103 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

    function sendFromDestination(bytes memory _payload) public {


Found in line 244 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 93 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

    function multiHop(bytes memory _payload) public {


Found in line 254 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 204 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

    function remove(bytes memory _payload) public {


Found in line 260 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 118 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

    function sendFromDestination(bytes memory _payload) public {


Found in line 259 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 111 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

    function multiHop(bytes memory _payload) public {


Found in line 284 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {


Found in line 1077 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

    function _getRevertMsg(bytes memory _returnData) private pure {


Found in line 93 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

    function _getRevertMsg(bytes memory _returnData) private pure {


Found in line 189 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function compound(bytes memory data) public {


Found in line 98 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    function compound(bytes memory) public {}


Found in line 160 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    function compound(bytes memory) public {


Found in line 151 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    function compound(bytes memory) public {


Found in line 159 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    function compound(bytes memory) public {


Found in line 138 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    function compound(bytes memory) public {


Found in line 97 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    function compound(bytes memory) public {}


Found in line 101 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    function compound(bytes memory) public {}


Found in line 117 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    function compound(bytes memory) public {}


Found in line 332 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {

------------------------------------------------------------------------ 

### Mitigation 

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.










# VULN 7 

## [GAS] Cache array length outside of loop
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 244 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 245 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 255 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 215 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 666 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 187 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                for (uint256 i = 0; i < maxBorrowParts.length; i++) {


Found in line 107 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 384 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        for (uint256 i = 0; i < users.length; i++) {


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {


Found in line 140 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

        for (uint256 i = 0; i < _call.length; i++) {


Found in line 261 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 260 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 285 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        for (uint256 i = 0; i < approvals.length; ) {


Found in line 195 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tokens.length; i++) {


Found in line 255 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 273 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 280 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        for (uint256 i = 0; i < tempData.tokens.length; i++) {


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 225 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 277 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        for (uint256 i = 0; i < poolTokens.length; i++) {


Found in line 332 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 336 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            for (uint256 i = 0; i < _users.length; i++) {


Found in line 333 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        for (uint256 i = 0; i < approvals.length; ) {

------------------------------------------------------------------------ 

### Mitigation 

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).










# VULN 8 

## [GAS] Use assembly to check for address(0)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 354 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        if (module == address(0)) {


Found in line 611 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        if (module == address(0)) {


Found in line 107 at 2023-07-tapioca/yieldbox/contracts/YieldBoxURIBuilder.sol:

                : abi.encodePacked(',"totalSupply":', totalSupply.toString(), ',"fixedSupply":', owner == address(0) ? "true" : "false")


Found in line 112 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (connectedOFTs[_srcOft][_dstChainId].dstOft == address(0))


Found in line 127 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (_router == address(0)) revert RouterNotValid();


Found in line 128 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (_routerETH == address(0)) revert RouterNotValid();


Found in line 142 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (ITapiocaOFT(_srcOft).erc20() == address(0)) {


Found in line 201 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);


Found in line 225 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        bool isNative = ITapiocaOFT(_srcOft).erc20() == address(0);


Found in line 74 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol:

        if (erc20 == address(0)) {


Found in line 94 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        if (erc20 == address(0)) {


Found in line 144 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        bool _isNative = erc20 == address(0);


Found in line 371 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        if (erc20 == address(0)) {


Found in line 396 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        if (module == address(0)) {


Found in line 272 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        if (erc20 == address(0)) {


Found in line 19 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

        if (_addr == address(0)) revert AddressNotValid();


Found in line 1057 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        if (module == address(0)) {


Found in line 19 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

        if (_addr == address(0)) revert AddressNotValid();


Found in line 62 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router");


Found in line 132 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        if (_user == address(0)) revert AddressNotValid();


Found in line 127 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

        require(broker == address(0), "OTAP: only once");


Found in line 140 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

        require(broker == address(0), "AOTAP: only once");

------------------------------------------------------------------------ 

### Mitigation 

Using assembly to check for the zero address can result in significant gas savings compared to using a Solidity expression, especially if the check is performed frequently or in a loop. However, it's important to note that using assembly can make the code less readable and harder to maintain, so it should be used judiciously and with caution.










# VULN 9 

## [GAS] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 143 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function decimals() public pure override returns (uint8) {


Found in line 159 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        uint16 lzDstChainId,


Found in line 254 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        uint16 lzDstChainId,


Found in line 316 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        uint16 lzDstChainId,


Found in line 378 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        uint16 _srcChainId,


Found in line 400 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

        uint16 _srcChainId,


Found in line 41 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_MARKET_MULTIHOP_BUY = 772;


Found in line 42 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_MARKET_REMOVE_ASSET = 773;


Found in line 43 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_YB_SEND_SGL_LEND_OR_REPAY = 774;


Found in line 44 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_LEVERAGE_MARKET_UP = 775;


Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_TAP_EXERCISE = 777;


Found in line 46 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    uint16 internal constant PT_SEND_FROM = 778;


Found in line 33 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        uint16 lzDstChainId,


Found in line 63 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        uint16 lzDstChainId,


Found in line 114 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                    uint16,


Found in line 136 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        uint16 _srcChainId,


Found in line 151 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                    uint16,


Found in line 25 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        uint16 lzDstChainId,


Found in line 109 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

            uint16 lzDstChainId,


Found in line 114 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                    uint16,


Found in line 118 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                    uint16,


Found in line 140 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        uint16 _srcChainId,


Found in line 155 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                    uint16,


Found in line 107 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                    uint16,


Found in line 135 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        uint16 _srcChainId,


Found in line 151 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                    uint16,


Found in line 217 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        uint8 v,


Found in line 229 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        uint8 v,


Found in line 257 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        uint8 v,


Found in line 184 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol:

    function decimals() external view returns (uint8) {


Found in line 15 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

    function to32(uint256 a) internal pure returns (uint32 c) {


Found in line 16 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

        require(a <= type(uint32).max, "BoringMath: uint32 Overflow");


Found in line 17 at 2023-07-tapioca/yieldbox/contracts/BoringMath.sol:

        c = uint32(a);


Found in line 47 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        uint8 v,


Found in line 76 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        uint8 v,


Found in line 9 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

    uint8 decimals;


Found in line 28 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

    event TokenCreated(address indexed creator, string name, string symbol, uint8 decimals, uint256 tokenId);


Found in line 90 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

    function createToken(string calldata name, string calldata symbol, uint8 decimals, string calldata uri) public returns (uint32 tokenId) {


Found in line 68 at 2023-07-tapioca/yieldbox/contracts/YieldBoxURIBuilder.sol:

    function decimals(Asset calldata asset, uint8 nativeDecimals) external view returns (uint8) {


Found in line 49 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    mapping(address => mapping(uint16 => OFTData)) public connectedOFTs;


Found in line 71 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 78 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 86 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 111 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    modifier onlyValidDestination(address _srcOft, uint16 _dstChainId) {


Found in line 139 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId


Found in line 174 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 221 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 252 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 270 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId


Found in line 283 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 300 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        uint16 _dstChainId,


Found in line 39 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol:

        uint8 _decimal,


Found in line 55 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

        uint8 _decimal,


Found in line 32 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint8 internal _decimalCache;


Found in line 34 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_YB_SEND_STRAT = 770;


Found in line 35 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_YB_RETRIEVE_STRAT = 771;


Found in line 36 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_MARKET_REMOVE_COLLATERAL = 772;


Found in line 37 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_MARKET_MULTIHOP_SELL = 773;


Found in line 38 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_YB_SEND_SGL_BORROW = 775;


Found in line 39 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_LEVERAGE_MARKET_DOWN = 776;


Found in line 40 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_TAP_EXERCISE = 777;


Found in line 41 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    uint16 internal constant PT_SEND_FROM = 778;


Found in line 51 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

        uint8 _decimal,


Found in line 56 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint8 _decimal,


Found in line 84 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

    function decimals() public view override returns (uint8) {


Found in line 100 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 lzDstChainId,


Found in line 193 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 lzDstChainId,


Found in line 230 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 lzDstChainId,


Found in line 261 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 lzDstChainId,


Found in line 293 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 lzDstChainId,


Found in line 420 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 _srcChainId,


Found in line 443 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        uint16 _srcChainId,


Found in line 30 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint8 _decimal,


Found in line 47 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint16 lzDstChainId,


Found in line 90 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint16 lzDstChainId,


Found in line 128 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint16 _srcChainId,


Found in line 143 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                    uint16,


Found in line 216 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                    uint16,


Found in line 26 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint8 _decimal,


Found in line 53 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint16 lzDstChainId,


Found in line 94 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint16 lzDstChainId,


Found in line 124 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint16 _srcChainId,


Found in line 140 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

                (uint16, bytes32, bytes32, uint256, uint256, uint256, address)


Found in line 189 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint16 _srcChainId,


Found in line 202 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

                (uint16, bytes32, bytes32, uint256, uint256, uint256, address)


Found in line 25 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        uint8 _decimal,


Found in line 40 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        uint16 lzDstChainId,


Found in line 124 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

            uint16 lzDstChainId,


Found in line 129 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                    uint16,


Found in line 133 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                    uint16,


Found in line 155 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        uint16 _srcChainId,


Found in line 170 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                    uint16,


Found in line 31 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        uint8 _decimal,


Found in line 124 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                    uint16,


Found in line 150 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        uint16 _srcChainId,


Found in line 166 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                    uint16,


Found in line 79 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 id;


Found in line 96 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint8 v;


Found in line 105 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint8 v;


Found in line 123 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 lzDstChainId;


Found in line 134 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 lzDstChainId;


Found in line 146 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 lzDstChainId;


Found in line 155 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 lzDstChainId;


Found in line 289 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

        uint16 lzDstChainId;


Found in line 298 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant PERMIT_ALL = 1;


Found in line 299 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant PERMIT = 2;


Found in line 301 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant YB_DEPOSIT_ASSET = 100;


Found in line 302 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant YB_WITHDRAW_TO = 102;


Found in line 304 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_ADD_COLLATERAL = 200;


Found in line 305 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_BORROW = 201;


Found in line 306 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_LEND = 203;


Found in line 307 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_REPAY = 204;


Found in line 308 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_YBDEPOSIT_AND_LEND = 205;


Found in line 309 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_YBDEPOSIT_COLLATERAL_AND_BORROW = 206;


Found in line 310 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_REMOVE_ASSET = 207;


Found in line 311 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_DEPOSIT_REPAY_REMOVE_COLLATERAL = 208;


Found in line 312 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_BUY_COLLATERAL = 209;


Found in line 313 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_SELL_COLLATERAL = 210;


Found in line 314 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_MULTIHOP_BUY = 211;


Found in line 315 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant MARKET_MULTIHOP_SELL = 212;


Found in line 317 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_WRAP = 300;


Found in line 318 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_SEND_FROM = 301;


Found in line 319 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_SEND_APPROVAL = 302;


Found in line 320 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_SEND_AND_BORROW = 303;


Found in line 321 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_SEND_AND_LEND = 304;


Found in line 322 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_DEPOSIT_TO_STRATEGY = 305;


Found in line 323 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_RETRIEVE_FROM_STRATEGY = 306;


Found in line 324 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TOFT_REMOVE_AND_REPAY = 307;


Found in line 326 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    uint16 internal constant TAP_EXERCISE_OPTION = 400;


Found in line 252 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                    uint16 dstChainId,


Found in line 260 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                            uint16,


Found in line 330 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                    uint16 dstChainId,


Found in line 342 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                            uint16,


Found in line 405 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                    uint16 lzDstChainId,


Found in line 416 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                            uint16,


Found in line 442 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                    uint16 dstChainId,


Found in line 453 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                            uint16,


Found in line 499 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                    uint16 lzDstChainId,


Found in line 509 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                            uint16,


Found in line 736 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        uint16 dstChainId,


Found in line 28 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint16 dstChainId,


Found in line 681 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint16 dstChainId,


Found in line 746 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            uint16 destChain,


Found in line 749 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        ) = abi.decode(withdrawData, (bool, uint16, bytes32, bytes));


Found in line 10 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

    uint8 public immutable override decimals;


Found in line 15 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint8 _decimals,


Found in line 18 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint8[] memory _circuitUniIsMultiplied,


Found in line 19 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint32 _twapPeriod,


Found in line 20 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint16 observationLength,


Found in line 21 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint8 _uniFinalCurrency,


Found in line 23 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint8[] memory _circuitChainIsMultiplied,


Found in line 24 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

        uint32 _stalePeriod,


Found in line 42 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

    function decimals() external view returns (uint8) {


Found in line 14 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol:

    function decimals() external pure returns (uint8) {


Found in line 62 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    function decimals() external pure returns (uint8) {


Found in line 38 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    uint24 public poolFee = 3000;


Found in line 61 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    function setPoolFee(uint24 _newFee) external onlyOwner {


Found in line 202 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            uint16(lpRouterPid),


Found in line 254 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

                uint16(lpRouterPid),


Found in line 33 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint8[4] amountsPerUsers;


Found in line 34 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint8[4] discountsPerUsers;


Found in line 77 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint8[4] public PHASE_2_AMOUNT_PER_USER = [200, 190, 200, 190];


Found in line 78 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint8[4] public PHASE_2_DISCOUNT_PER_USER = [50, 40, 40, 33];


Found in line 37 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    uint16 internal constant PT_LOCK_TWTAP = 870;


Found in line 38 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    uint16 internal constant PT_UNLOCK_TWTAP = 871;


Found in line 39 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    uint16 internal constant PT_CLAIM_REWARDS = 872;


Found in line 41 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    event CallFailedStr(uint16 _srcChainId, bytes _payload, string _reason);


Found in line 42 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    event CallFailedBytes(uint16 _srcChainId, bytes _payload, bytes _reason);


Found in line 47 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint8 _sharedDec,


Found in line 53 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 _srcChainId,


Found in line 92 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 lzDstChainId,


Found in line 126 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 _srcChainId,


Found in line 131 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            (uint16, address, address, uint256, uint256)


Found in line 167 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 lzDstChainId,


Found in line 199 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 _srcChainId,


Found in line 212 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                    uint16,


Found in line 261 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 lzDstChainId,


Found in line 292 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        uint16 _srcChainId,


Found in line 303 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                (uint16, address, address, uint256, LzCallParams)


Found in line 168 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    function decimals() public pure override returns (uint8) {


Found in line 40 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint24 multiplier; // Votes = multiplier * tapAmount


Found in line 335 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            multiplier: uint24(multiplier),

------------------------------------------------------------------------ 

### Mitigation 

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.










# VULN 10 

## [GAS] Public Functions to external
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 57 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

    function maxFlashLoan(address) public view override returns (uint256) {


Found in line 143 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function decimals() public pure override returns (uint8) {


Found in line 114 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

    function totalSupply() public view virtual override returns (uint256) {}


Found in line 191 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol:

    function totalSupply() public view override returns (uint256) {


Found in line 84 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

    function decimals() public view override returns (uint8) {


Found in line 47 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol:

    function name(bytes calldata) public pure override returns (string memory) {


Found in line 168 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    function decimals() public pure override returns (uint8) {

------------------------------------------------------------------------ 

### Mitigation 

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.










# VULN 11 

## [GAS] <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 551 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

                marketsLength += clonesOfCount(array[i].location);


Found in line 147 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                balanceOf[to] += amount;


Found in line 182 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                balanceOf[to] += amount;


Found in line 446 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        totalFees += balance;


Found in line 535 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        _totalBorrow.elastic += uint128(extraAmount);


Found in line 553 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        userCollateralShare[to] += share;


Found in line 755 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        userBorrowPart[from] += part;


Found in line 104 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        _totalBorrow.elastic += uint128(extraAmount);


Found in line 108 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        _accrueInfo.feesEarnedFraction += uint128(feeFraction);


Found in line 184 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        _yieldBoxShares[to][_asset_sig] += share;


Found in line 214 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        balanceOf[to] += fraction;


Found in line 465 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        balanceOf[_feeTo] += _feesEarnedFraction;


Found in line 46 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                    needed += maxBorrowParts[i];


Found in line 147 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                allCollateralShare += collateralShare;


Found in line 148 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                allBorrowAmount += borrowAmount;


Found in line 149 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

                allBorrowPart += borrowPart;


Found in line 195 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        totalAsset.elastic += uint128(returnedShare - callerShare);


Found in line 284 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);


Found in line 26 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        userCollateralShare[to] += share;


Found in line 70 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        userBorrowPart[from] += part;


Found in line 29 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

        totalShares_ += 1e8;


Found in line 52 at 2023-07-tapioca/yieldbox/contracts/YieldBoxRebase.sol:

        totalShares_ += 1e8;


Found in line 255 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;


Found in line 215 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                valAccumulator += _action.value;


Found in line 237 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                        valAccumulator += _action.value;


Found in line 77 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

                valAccumulator += val;


Found in line 210 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        tokenAvailable += tokenReserved;


Found in line 269 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

                amount += (obtainedWrapped - toWithdraw);


Found in line 115 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        _totalClaimed += _claimable;


Found in line 116 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        users[msg.sender].claimed += _claimable;


Found in line 143 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        _totalAmount += _amount;


Found in line 187 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        activeSingularities[_singularity].totalDeposited += _amount;


Found in line 340 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            total += activeSingularities[sglAssetIDToAddress[singularities[i]]]


Found in line 251 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                pool.cumulative += pool.averageMagnitude;


Found in line 261 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

            pool.totalDeposited += lock.amount;


Found in line 321 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                pool.cumulative += pool.averageMagnitude;


Found in line 392 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        oTAPCalls[_oTAPTokenID][cachedEpoch] += chosenAmount; // Adds up exercised amount to current epoch


Found in line 259 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        aoTAPCalls[_aoTAPTokenID][cachedEpoch] += chosenAmount; // Adds up exercised amount to current epoch


Found in line 209 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        emission += unclaimed;


Found in line 227 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        mintedInWeek[week] += _amount;


Found in line 287 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                pool.cumulative += pool.averageMagnitude;


Found in line 298 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            pool.totalDeposited += _amount;


Found in line 343 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        weekTotals[w0 + 1].netActiveVotes += int256(votes);


Found in line 412 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

            next.netActiveVotes += prev.netActiveVotes;


Found in line 414 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                next.totalDistPerVote[i] += prev.totalDistPerVote[i];


Found in line 445 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        totals.totalDistPerVote[_rewardTokenId] +=


Found in line 491 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                    claimed[_tokenId][i] += amount;


Found in line 513 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                    claimed[_tokenId][claimableIndex] += amount;


Found in line 550 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                pool.cumulative += position.averageMagnitude;

------------------------------------------------------------------------ 

### Mitigation 

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.










# VULN 12 

## [GAS] Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 62 and 63 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    // Used to check if a BigBang master contract is registered

    mapping(address => bool) public isBigBangMasterContractRegistered;


Found in line 27 and 28 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    /// @dev chainId>address>status

    mapping(uint256 => mapping(address => bool)) public allowedBurner;


Found in line 58 and 59 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

    /// @notice collateral share per user

    mapping(address => uint256) public userCollateralShare;


Found in line 49 and 50 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

    /// @notice owner > spender > allowance mapping.

    mapping(address => mapping(address => uint256)) public override allowance;


Found in line 53 and 54 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

    /// @notice owner > nonce mapping. Used in `permit`.

    mapping(address => uint256) private _nonces;


Found in line 24 and 25 at 2023-07-tapioca/yieldbox/contracts/NativeTokenFactory.sol:

    mapping(uint256 => NativeToken) public nativeTokens;

    mapping(uint256 => address) public owner;


Found in line 34 and 35 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

    mapping(uint256 => TapOption) public options; // tokenId => Option

    mapping(uint256 => string) public tokenURIs; // tokenId => tokenURI


Found in line 61 and 62 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

    mapping(IERC20 => SingularityPool) public activeSingularities;

    mapping(uint256 => IERC20) public sglAssetIDToAddress; // Singularity market YieldBox asset ID => Singularity market address


Found in line 64 and 65 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    mapping(uint256 => Participation) public participants; // tOLPTokenID => Participation

    mapping(uint256 => mapping(uint256 => uint256)) public oTAPCalls; // oTAPTokenID => epoch => amountExercised


Found in line 36 and 37 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

    mapping(uint256 => AirdropTapOption) public options; // tokenId => Option

    mapping(uint256 => string) public tokenURIs; // tokenId => tokenURI


Found in line 99 and 100 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    // tokenId -> rewardTokens index -> amount

    mapping(uint256 => mapping(uint256 => uint256)) public claimed;

------------------------------------------------------------------------ 

### Mitigation 

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.










# VULN 13 

## [GAS] Use hardcode address instead address(this)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 515 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

                address(this),


Found in line 536 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            yieldBox.transfer(address(this), feeTo, assetId, feeShares);


Found in line 68 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(token == address(this), "USDO: token not valid");


Found in line 87 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(token == address(this), "USDO: token not valid");


Found in line 99 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        uint256 _allowance = allowance(address(receiver), address(this));


Found in line 101 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        _approve(address(receiver), address(this), _allowance - (amount + fee));


Found in line 160 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 163 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

            _creditTo(_srcChainId, address(this), lendParams.depositAmount);


Found in line 166 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 180 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                IERC20(address(this)).safeTransfer(


Found in line 127 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        ISendFrom(address(this)).sendFrom{value: address(this).balance}(


Found in line 162 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 167 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                address(this),


Found in line 172 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 191 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                IERC20(address(this)).safeTransfer(


Found in line 227 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                address(this),


Found in line 161 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 164 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            _creditTo(_srcChainId, address(this), amount);


Found in line 167 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 182 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                IERC20(address(this)).safeTransfer(leverageFor, amount);


Found in line 198 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

        _approve(address(this), externalData.swapper, amount);


Found in line 201 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                address(this),


Found in line 212 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            address(this),


Found in line 219 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            address(this),


Found in line 220 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            address(this),


Found in line 227 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            value: address(this).balance


Found in line 229 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            address(this),


Found in line 216 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            (bool success, bytes memory result) = address(this).delegatecall(


Found in line 445 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 balance = asset.balanceOf(address(this));


Found in line 454 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                address(this),


Found in line 597 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            address(this),


Found in line 608 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 balanceBefore = yieldBox.balanceOf(address(this), assetId);


Found in line 618 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        swapper.swap(swapData, minAssetMount, address(this), "");


Found in line 619 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        uint256 balanceAfter = yieldBox.balanceOf(address(this), assetId);


Found in line 648 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);


Found in line 649 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 700 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                share <= yieldBox.balanceOf(address(this), _tokenId) - total,


Found in line 704 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            yieldBox.transfer(from, address(this), _tokenId, share);


Found in line 717 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.transfer(address(this), to, collateralId, share);


Found in line 732 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.withdraw(assetId, from, address(this), amount, 0);


Found in line 735 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            IUSDOBase(address(asset)).burn(address(this), toBurn);


Found in line 758 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        IUSDOBase(address(asset)).mint(address(this), amount);


Found in line 762 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        yieldBox.depositAsset(assetId, address(this), to, amount, 0);


Found in line 47 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);


Found in line 71 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        _removeCollateral(from, address(this), share);


Found in line 74 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

            address(this),


Found in line 75 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

            address(this),


Found in line 188 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

                share <= yieldBox.balanceOf(address(this), _assetId) - total,


Found in line 192 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

            yieldBox.transfer(from, address(this), _assetId, share);


Found in line 243 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        yieldBox.transfer(address(this), to, assetId, share);


Found in line 188 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            (bool success, bytes memory result) = address(this).delegatecall(


Found in line 167 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

            address(this),


Found in line 179 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -


Found in line 193 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 198 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

            address(this),


Found in line 275 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        uint256 returnedShare = yieldBox.balanceOf(address(this), assetId) -


Found in line 281 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);


Found in line 282 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);


Found in line 288 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

            address(this),


Found in line 331 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

            address(this),


Found in line 350 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        swapper.swap(swapData, minAssetAmount, address(this), "");


Found in line 49 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        yieldBox.transfer(address(this), to, collateralId, share);


Found in line 79 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        yieldBox.transfer(address(this), to, assetId, share);


Found in line 286 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (address(this).balance < _amount) revert ExceedsBalance();


Found in line 305 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

        if (erc20.balanceOf(address(this)) < _amount) revert ExceedsBalance();


Found in line 198 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

                                address(this),


Found in line 216 at 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol:

                                address(this),


Found in line 359 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);


Found in line 460 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

                    IERC20(address(this))


Found in line 152 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 155 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

            _creditTo(_srcChainId, address(this), borrowParams.amount);


Found in line 158 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 172 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                IERC20(address(this)).safeTransfer(_from, borrowParams.amount);


Found in line 143 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 146 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            _creditTo(_srcChainId, address(this), amount);


Found in line 149 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 159 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

                address(this),


Found in line 165 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

                IERC20(address(this)).safeTransfer(onBehalfOf, amount);


Found in line 206 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));


Found in line 209 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            address(this),


Found in line 211 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            LzLib.addressToBytes32(address(this)),


Found in line 225 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            address(this).balance


Found in line 230 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            LzLib.addressToBytes32(address(this)),


Found in line 142 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        ISendFrom(address(this)).sendFrom{value: address(this).balance}(


Found in line 177 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 182 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                address(this),


Found in line 187 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 206 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                IERC20(address(this)).safeTransfer(


Found in line 242 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                address(this),


Found in line 176 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        uint256 balanceBefore = balanceOf(address(this));


Found in line 179 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

            _creditTo(_srcChainId, address(this), amount);


Found in line 182 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        uint256 balanceAfter = balanceOf(address(this));


Found in line 197 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                IERC20(address(this)).safeTransfer(leverageFor, amount);


Found in line 212 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

        _unwrap(address(this), amount);


Found in line 221 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

            address(this),


Found in line 230 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

            value: address(this).balance


Found in line 232 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

            address(this),


Found in line 155 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

                address(this),


Found in line 156 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

                address(this),


Found in line 162 at 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol:

        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);


Found in line 29 at 2023-07-tapioca/tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol:

            address(this).balance >= amount,


Found in line 60 at 2023-07-tapioca/tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol:

        return computeAddress(salt, bytecodeHash, address(this));


Found in line 163 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 164 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 174 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                deposit ? address(this) : user,


Found in line 186 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                ? address(this)


Found in line 237 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 238 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 248 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                depositAmount > 0 ? address(this) : user,


Found in line 261 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                ? address(this)


Found in line 286 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this).balance


Found in line 345 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 346 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 358 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                        ? address(this)


Found in line 392 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 425 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 431 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 432 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 438 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            address lockTo = participateData.participate ? address(this) : user;


Found in line 478 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            IERC721(oTapAddress).approve(address(this), oTAPTokenId);


Found in line 480 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 528 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                ownerOfTapTokenId == user || ownerOfTapTokenId == address(this),


Found in line 534 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 544 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 582 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                ? address(this)


Found in line 599 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 617 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                address(this),


Found in line 625 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 643 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                ? address(this)


Found in line 664 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    address(this),


Found in line 712 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        yieldBox.withdraw(assetId, from, address(this), amount, 0);


Found in line 726 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            address(this),


Found in line 751 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        uint256 gas = msg.value > 0 ? msg.value : address(this).balance;


Found in line 771 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            address(this),


Found in line 784 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            address(this),


Found in line 797 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        IERC20(_token).safeTransferFrom(_from, address(this), _amount);


Found in line 155 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

                address(this),


Found in line 156 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

                address(this),


Found in line 162 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol:

        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);


Found in line 186 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

                    ? address(this)


Found in line 200 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

                address(this),


Found in line 155 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

            swapData.yieldBoxData.depositToYb ? address(this) : to,


Found in line 165 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

                address(this),


Found in line 134 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

                address(this),


Found in line 158 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));


Found in line 163 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        uint256 balanceAfter = IERC20(tokenOut).balanceOf(address(this));


Found in line 120 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            uint256 wethAmount = weth.balanceOf(address(this));


Found in line 131 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        amount = IERC20(contractAddress).balanceOf(address(this));


Found in line 141 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 freeGlp = stakedGlpTracker.balanceOf(address(this));


Found in line 167 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 wethAmount = weth.balanceOf(address(this));


Found in line 190 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 totalVestable = vester.getMaxVestableAmount(address(this));


Found in line 191 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 vestingNow = vester.balances(address(this));


Found in line 192 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 alreadyVested = vester.cumulativeClaimAmounts(address(this));


Found in line 205 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            address(this)


Found in line 209 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 tokenReserved = vester.pairAmounts(address(this));


Found in line 254 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 freeEsGmx = esGmx.balanceOf(address(this));


Found in line 256 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            address(this),


Found in line 263 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            stakedGlpTracker.balanceOf(address(this))


Found in line 281 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 freeEsGmx = esGmx.balanceOf(address(this));


Found in line 288 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 unusedStakedTokens = feeGmxTracker.balanceOf(address(this));


Found in line 302 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 freeEsGmx = esGmx.balanceOf(address(this));


Found in line 305 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            address(this),


Found in line 317 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        uint256 gmxAmount = gmx.balanceOf(address(this));


Found in line 325 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            address(this),


Found in line 131 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 claimable = rewardPool.rewards(address(this));


Found in line 151 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 lpBalance = rewardPool.balanceOf(address(this));


Found in line 208 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

                swapper.swap(swapData, minAmount, address(this), "");


Found in line 212 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 257 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

                address(this)


Found in line 275 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

                address(this)


Found in line 292 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 lpBalance = rewardPool.balanceOf(address(this));


Found in line 294 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 301 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 330 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 333 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            uint256 lpBalance = rewardPool.balanceOf(address(this));


Found in line 341 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

            wrappedNative.balanceOf(address(this)) >= amount,


Found in line 346 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        queued = wrappedNative.balanceOf(address(this));


Found in line 104 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        uint256 toWithdraw = vault.balanceOf(address(this));


Found in line 105 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        result = vault.withdraw(toWithdraw, address(this), 0);


Found in line 112 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        uint256 shares = vault.balanceOf(address(this));


Found in line 115 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 122 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 124 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

            vault.deposit(queued, address(this));


Found in line 139 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 145 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

            vault.withdraw(toWithdraw, address(this), 0);


Found in line 106 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            abi.encodeWithSignature("claimable_tokens(address)", address(this))


Found in line 161 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 claimable = lpGauge.claimable_tokens(address(this));


Found in line 163 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));


Found in line 165 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));


Found in line 180 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

                swapper.swap(swapData, minAmount, address(this), "");


Found in line 183 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

                    address(this)


Found in line 191 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

                lpGauge.deposit(lpAmount, address(this), false);


Found in line 202 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        result = lpGauge.balanceOf(address(this));


Found in line 211 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 lpBalance = lpGauge.balanceOf(address(this));


Found in line 212 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 queued = lpToken.balanceOf(address(this));


Found in line 219 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 queued = lpToken.balanceOf(address(this));


Found in line 221 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            lpGauge.deposit(queued, address(this), false);


Found in line 236 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        uint256 queued = lpToken.balanceOf(address(this));


Found in line 239 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            uint256 lpBalance = lpGauge.balanceOf(address(this));


Found in line 243 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            lpToken.balanceOf(address(this)) >= amount,


Found in line 248 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        queued = lpToken.balanceOf(address(this));


Found in line 250 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

            lpGauge.deposit(queued, address(this), false);


Found in line 104 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 claimable = lpGauge.claimable_tokens(address(this));


Found in line 152 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 claimable = lpGauge.claimable_tokens(address(this));


Found in line 154 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));


Found in line 171 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

                swapper.swap(swapData, minAmount, address(this), "");


Found in line 173 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

                uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 185 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 lpBalance = lpGauge.balanceOf(address(this));


Found in line 197 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 lpBalance = lpGauge.balanceOf(address(this));


Found in line 199 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 206 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 226 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 229 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            uint256 lpBalance = lpGauge.balanceOf(address(this));


Found in line 236 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

            wrappedNative.balanceOf(address(this)) >= amount,


Found in line 240 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        queued = wrappedNative.balanceOf(address(this));


Found in line 251 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        lpGauge.deposit(lpAmount, address(this), false);


Found in line 119 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            address(this)


Found in line 162 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            address(this)


Found in line 166 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            uint256 stgBalanceBefore = stgTokenReward.balanceOf(address(this));


Found in line 168 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            uint256 stgBalanceAfter = stgTokenReward.balanceOf(address(this));


Found in line 184 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

                swapper.swap(swapData, minAmount, address(this), "");


Found in line 186 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

                uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 198 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            address(this)


Found in line 204 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            address(this)


Found in line 206 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        result = address(this).balance;


Found in line 215 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 216 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        (amount, ) = lpStaking.userInfo(lpStakingPid, address(this));


Found in line 224 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 235 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        uint256 toStake = stgNative.balanceOf(address(this));


Found in line 248 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 256 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

                address(this)


Found in line 263 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

            amount <= wrappedNative.balanceOf(address(this)),


Found in line 100 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 139 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 aaveBalanceBefore = rewardToken.balanceOf(address(this));


Found in line 142 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 151 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

                address(this)


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 159 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            stakedRewardToken.claimRewards(address(this), claimable);


Found in line 164 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 167 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 balanceOfStkAave = stakedRewardToken.balanceOf(address(this));


Found in line 179 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 aaveBalanceAfter = rewardToken.balanceOf(address(this));


Found in line 194 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            swapper.swap(swapData, minAmount, address(this), "");


Found in line 197 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 201 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

                address(this),


Found in line 212 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 217 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

            address(this)


Found in line 226 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        (amount, , , , , ) = lendingPool.getUserAccountData(address(this));


Found in line 227 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 235 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 240 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

                address(this),


Found in line 257 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 266 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

                address(this)


Found in line 103 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        uint256 toWithdraw = cToken.balanceOf(address(this));


Found in line 105 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        INative(address(wrappedNative)).deposit{value: address(this).balance}();


Found in line 107 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        result = address(this).balance;


Found in line 114 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        uint256 shares = cToken.balanceOf(address(this));


Found in line 117 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 124 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 143 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 151 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

                value: address(this).balance


Found in line 156 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

            wrappedNative.balanceOf(address(this)) >= amount,


Found in line 107 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 toWithdraw = stEth.balanceOf(address(this));


Found in line 119 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 stEthBalance = stEth.balanceOf(address(this));


Found in line 123 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 129 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 148 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 161 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        queued = wrappedNative.balanceOf(address(this));


Found in line 131 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 139 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 150 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 lpBalanceBefore = pool.balanceOf(address(this));


Found in line 173 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this),


Found in line 174 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this),


Found in line 181 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        vault.joinPool(poolId, address(this), address(this), joinPoolRequest);


Found in line 182 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 lpBalanceAfter = pool.balanceOf(address(this));


Found in line 198 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 queued = wrappedNative.balanceOf(address(this));


Found in line 209 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            amount <= wrappedNative.balanceOf(address(this)),


Found in line 220 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this)


Found in line 241 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            pool.balanceOf(address(this))


Found in line 246 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this),


Found in line 251 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 maxBpt = pool.balanceOf(address(this));


Found in line 257 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        vault.exitPool(poolId, address(this), payable(this), exitRequest);


Found in line 260 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this)


Found in line 272 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        uint256 lpBalance = pool.balanceOf(address(this));


Found in line 292 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this),


Found in line 293 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

            address(this),


Found in line 157 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        uint256 availableToken = _token.balanceOf(address(this));


Found in line 186 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);


Found in line 242 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

            address(this),


Found in line 230 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        tOLP.transferFrom(msg.sender, address(this), _tOLPTokenID);


Found in line 342 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);


Found in line 493 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                    paymentToken.balanceOf(address(this))


Found in line 532 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

            address(this),


Found in line 379 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

                    paymentToken.balanceOf(address(this))


Found in line 511 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

            address(this),


Found in line 42 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

        tapToken.transferFrom(msg.sender, address(this), amount);


Found in line 134 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        _creditTo(_srcChainId, address(this), amount);


Found in line 144 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            _transferFrom(address(this), to, amount);


Found in line 147 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            _transferFrom(address(this), to, amount);


Found in line 232 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                    address(this),


Found in line 235 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                    IERC20(rewardTokens[i]).balanceOf(address(this)),


Found in line 312 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            this.sendFrom{value: address(this).balance}(


Found in line 313 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                address(this),


Found in line 260 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        tapOFT.transferFrom(msg.sender, address(this), _amount);


Found in line 448 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        rewardToken.safeTransferFrom(msg.sender, address(this), _amount);

------------------------------------------------------------------------ 

### Mitigation 

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.










# VULN 14 

## [GAS] Functions guaranteed to revert when called by normal users can be marked payable
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 256 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner {


Found in line 263 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setBigBangEthMarket(address _market) external onlyOwner {


Found in line 281 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setConservator(address _conservator) external onlyOwner {


Found in line 291 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setUsdoToken(address _usdoToken) external onlyOwner {


Found in line 455 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setFeeTo(address feeTo_) external onlyOwner {


Found in line 464 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    function setSwapper(ISwapper swapper, bool enable) external onlyOwner {


Found in line 88 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function setMaxFlashMintable(uint256 _val) external onlyOwner {


Found in line 96 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function setFlashMintFee(uint256 _val) external onlyOwner {


Found in line 105 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function setConservator(address _conservator) external onlyOwner {


Found in line 125 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function setMinterStatus(address _for, bool _status) external onlyOwner {


Found in line 134 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    function setBurnerStatus(address _for, bool _status) external onlyOwner {


Found in line 142 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

    function setBorrowOpeningFee(uint256 _val) external onlyOwner {


Found in line 151 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

    function setBorrowCap(uint256 _cap) external notPaused onlyOwner {


Found in line 61 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol:

    function setPoolFee(uint24 _newFee) external onlyOwner {


Found in line 113 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    function setFeeRecipient(address recipient) external onlyOwner {


Found in line 148 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 163 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 170 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function setMultiSwapper(address _swapper) external onlyOwner {


Found in line 179 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    function setTricryptoLPGetter(address _lpGetter) external onlyOwner {


Found in line 90 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 101 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 134 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 141 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    function setMultiSwapper(address _swapper) external onlyOwner {


Found in line 150 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    function setTricryptoLPGetter(address _lpGetter) external onlyOwner {


Found in line 199 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 125 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 132 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    function setMultiSwapper(address _swapper) external onlyOwner {


Found in line 141 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    function setTricryptoLPGetter(address _lpGetter) external onlyOwner {


Found in line 182 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 142 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 149 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    function setMultiSwapper(address _swapper) external onlyOwner {


Found in line 193 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 122 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 129 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    function setMultiSwapper(address _swapper) external onlyOwner {


Found in line 209 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 89 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 100 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 93 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 104 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 109 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    function setDepositThreshold(uint256 amount) external onlyOwner {


Found in line 120 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {


Found in line 130 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

    function registerUser(address _user, uint256 _amount) external onlyOwner {


Found in line 151 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

    function init(IERC20 _token, uint256 _seededAmount) external onlyOwner {


Found in line 53 at 2023-07-tapioca/tap-token-audit/contracts/tokens/LTap.sol:

    function setLockedUntil(uint256 _lockedUntil) external onlyOwner {


Found in line 326 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    function setTwTap(address _twTap) external onlyOwner {


Found in line 152 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    function updatePause(bool val) external onlyOwner {


Found in line 160 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    function setMinter(address _minter) external onlyOwner {


Found in line 455 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    function addRewardToken(IERC20 token) external onlyOwner returns (uint256) {

------------------------------------------------------------------------ 

### Mitigation 

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.










# VULN 15 

## [GAS] Do not calculate constants
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 40 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    uint256 public constant GAMMA0 = 28_000_000_000_000; // 2.8e-5


Found in line 41 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    uint256 public constant A0 = 2 * 3 ** 3 * 10_000;


Found in line 42 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    uint256 public constant DISCOUNT0 = 1_087_460_000_000_000; // 0.00108..


Found in line 75 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    uint256 constant MIN_WEIGHT_FACTOR = 10; // In BPS, 0.1%


Found in line 76 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    uint256 constant dMAX = 50 * 1e4; // 5% - 50% discount


Found in line 77 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    uint256 constant dMIN = 5 * 1e4;


Found in line 69 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4; // 50%


Found in line 85 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;


Found in line 93 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;


Found in line 41 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO


Found in line 45 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    uint256 constant decay_rate = 8800000000000000; // 0.88%


Found in line 81 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 constant MIN_WEIGHT_FACTOR = 10; // In BPS, 0.1%


Found in line 82 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 constant dMAX = 100 * 1e4; // 10% - 100% voting power multiplier


Found in line 83 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 constant dMIN = 10 * 1e4;


Found in line 95 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 constant DIST_PRECISION = 2 ** 128;

------------------------------------------------------------------------ 

### Mitigation 

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.










# VULN 16 

## [GAS] Using private rather than public for constants, saves gas
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 45 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    uint256 public immutable tapAssetId;


Found in line 53 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    uint256 public immutable wethAssetId;


Found in line 48 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    uint256 public immutable pid;


Found in line 78 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    uint256 public immutable EPOCH_DURATION; // 7 days = 604800


Found in line 53 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    uint256 public immutable emissionsStartTime;


Found in line 111 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    uint256 public immutable HOST_CHAIN_ID;

------------------------------------------------------------------------ 

### Mitigation 

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.










# VULN 17 

## [GAS] >= costs less gas than >
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 476 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        if (_returnData.length < 68) return "SGL: no return data";


Found in line 91 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

        if (_returnData.length < 68) return "USDO: no return data";


Found in line 285 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (borrowPartScaled < liquidationStartsAt) return 0;


Found in line 376 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (_returnData.length < 68) return "Market: no return data";


Found in line 450 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        if (borrowed < startTVLInAsset) return 0;


Found in line 198 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        if (debt > maxDebtRate) return maxDebtRate;


Found in line 71 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

        if (_returnData.length < 68) return "TOFT_data";


Found in line 1080 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        if (_returnData.length < 68) revert("MagnetarV2: Reason unknown");


Found in line 96 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        if (_returnData.length < 68) revert("Reason unknown");


Found in line 170 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        if (block.timestamp < start + cliff) return 0;


Found in line 179 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        if (timestamp < emissionsStartTime) return 0;


Found in line 201 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        if (emissionForWeek[week] > 0) return 0;

------------------------------------------------------------------------ 

### Mitigation 

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.










# VULN 18 

## [GAS] With assembly, .call (bool success) transfer can be done gas-optimized
------------------------------------------------------------------------ 

### Proof of concept 

Found in 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol (function _executeModule():



    function _executeModule(

        Module _module,

        bytes memory _data,

        bool _forwardRevert

    ) private returns (bool success, bytes memory returnData) {

        success = true;

        address module = _extractModule(_module);



        (success, returnData) = module.delegatecall(_data);

        if (!success && !_forwardRevert) {



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol (function _executeOnDestination():

        uint16 _srcChainId,

        bytes memory _srcAddress,

        uint64 _nonce,

        bytes memory _payload

    ) private {

        (bool success, bytes memory returnData) = _executeModule(

            _module,

            _data,

            true

        );

        if (!success) {



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol (function lend():

            _creditTo(_srcChainId, address(this), lendParams.depositAmount);

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.lendInternal.selector,

                to,

                lendParams,

                approvals,



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol (function exercise():

            );

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.exerciseInternal.selector,

                optionsData.from,

                optionsData.oTAPTokenID,

                optionsData.paymentToken,



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol (function leverageUp():

            _creditTo(_srcChainId, address(this), amount);

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.leverageUpInternal.selector,

                amount,

                swapData,

                externalData,



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol (function execute():

        bool revertOnFail

    ) external returns (bool[] memory successes, string[] memory results) {

        successes = new bool[](calls.length);

        results = new string[](calls.length);

        for (uint256 i = 0; i < calls.length; i++) {

            (bool success, bytes memory result) = address(this).delegatecall(

                calls[i]

            );

            require(success || !revertOnFail, _getRevertMsg(result));

            successes[i] = success;

            results[i] = _getRevertMsg(result);



Found in 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol (function execute():

        bool revertOnFail

    ) external returns (bool[] memory successes, string[] memory results) {

        successes = new bool[](calls.length);

        results = new string[](calls.length);

        for (uint256 i = 0; i < calls.length; i++) {

            (bool success, bytes memory result) = address(this).delegatecall(

                calls[i]

            );

            require(success || !revertOnFail, _getRevertMsg(result));

            successes[i] = success;

            results[i] = _getRevertMsg(result);



Found in 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol (function executeTOFT():

    /// @return result The error message if the execution failed.

    function executeTOFT(

        address _toft,

        bytes calldata _bytecode,

        bool _revertOnFailure

    ) external payable onlyOwner returns (bool success, bytes memory result) {

        (success, result) = payable(_toft).call{value: msg.value}(_bytecode);

        if (_revertOnFailure && !success) {

            revert TapiocaWrapper__TOFTExecutionFailed(result);

        }

    }



Found in 2023-07-tapioca/tapiocaz-audit/contracts/TapiocaWrapper.sol (function executeCalls():

        ExecutionCall[] calldata _call

    )

        external

        payable

        onlyOwner

        returns (bool success, bytes[] memory results)

    {

        results = new bytes[](_call.length);

        for (uint256 i = 0; i < _call.length; i++) {

            (success, results[i]) = payable(_call[i].toft).call{

                value: msg.value



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol (function _executeModule():



    function _executeModule(

        Module _module,

        bytes memory _data,

        bool _forwardRevert

    ) private returns (bool success, bytes memory returnData) {

        success = true;

        address module = _extractModule(_module);



        (success, returnData) = module.delegatecall(_data);

        if (!success && !_forwardRevert) {



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol (function _executeOnDestination():

        uint16 _srcChainId,

        bytes memory _srcAddress,

        uint64 _nonce,

        bytes memory _payload

    ) private {

        (bool success, bytes memory returnData) = _executeModule(

            _module,

            _data,

            true

        );

        if (!success) {



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol (function borrow():

            _creditTo(_srcChainId, address(this), borrowParams.amount);

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.borrowInternal.selector,

                _to,

                borrowParams,

                withdrawParams,



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol (function strategyDeposit():

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        address onBehalfOf = LzLib.bytes32ToAddress(from);

        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.depositToYieldbox.selector,

                assetId,

                amount,

                share,



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol (function exercise():

            );

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.exerciseInternal.selector,

                optionsData.from,

                optionsData.oTAPTokenID,

                optionsData.paymentToken,



Found in 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol (function leverageDown():

            _creditTo(_srcChainId, address(this), amount);

            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

        }

        uint256 balanceAfter = balanceOf(address(this));



        (bool success, bytes memory reason) = module.delegatecall(

            abi.encodeWithSelector(

                this.leverageDownInternal.selector,

                amount,

                swapData,

                externalData,



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/BaseSwapper.sol (function _safeApprove(address token, address to, uint256 value) internal {):

        swapData.amountData = swapAmountData;

        swapData.yieldBoxData = swapYBData;

    }



    function _safeApprove(address token, address to, uint256 value) internal {

        (bool success, bytes memory data) = token.call(

            abi.encodeWithSelector(0x095ea7b3, to, value)

        );

        require(

            success && (data.length == 0 || abi.decode(data, (bool))),

            "BaseSwapper::safeApprove: approve failed"



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol (function _permit():

                (PermitData)

            );

            _checkSender(permitData.owner);

        }



        (bool success, bytes memory returnData) = target.call(actionCalldata);

        if (!success && !allowFailure) {

            _getRevertMsg(returnData);

        }

    }





Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol (function get():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function get(

        bytes calldata

    ) external virtual returns (bool success, uint256 rate) {

        (, uint256 high) = _readAll(inBase);

        return (true, high);

    }



    /// @notice Check the last exchange rate without any state changes.



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol (function peek():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function peek(

        bytes calldata

    ) external view virtual returns (bool success, uint256 rate) {

        (, uint256 high) = _readAll(inBase);

        return (true, high);

    }



    /// @notice Check the current spot exchange rate without any state changes. For oracles like TWAP this will be different from peek().



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol (function get():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function get(

        bytes calldata

    ) external virtual returns (bool success, uint256 rate) {

        return (true, _get());

    }



    /// @notice Check the last exchange rate without any state changes.

    /// For example:



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol (function peek():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function peek(

        bytes calldata

    ) external view virtual returns (bool success, uint256 rate) {

        return (true, _get());

    }



    /// @notice Check the current spot exchange rate without any state changes. For oracles like TWAP this will be different from peek().

    /// For example:



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol (function get():



    // Get the latest exchange rate

    /// @inheritdoc IOracle

    function get(

        bytes calldata

    ) public view override returns (bool success, uint256 rate) {

        return (true, _get());

    }



    // Check the last exchange rate without any state changes

    /// @inheritdoc IOracle



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol (function peek():



    // Check the last exchange rate without any state changes

    /// @inheritdoc IOracle

    function peek(

        bytes calldata

    ) public view override returns (bool success, uint256 rate) {

        return (true, _get());

    }



    // Check the current spot exchange rate without any state changes

    /// @inheritdoc IOracle



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol (function get():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function get(

        bytes calldata

    ) external virtual returns (bool success, uint256 rate) {

        return (true, _get());

    }



    /// @notice Check the last exchange rate without any state changes.

    /// For example:



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol (function peek():

    /// (string memory collateralSymbol, string memory assetSymbol, uint256 division) = abi.decode(data, (string, string, uint256));

    /// @return success if no valid (recent) rate is available, return false else true.

    /// @return rate The rate of the requested asset / pair / pool.

    function peek(

        bytes calldata

    ) external view virtual returns (bool success, uint256 rate) {

        return (true, _get());

    }



    /// @notice Check the current spot exchange rate without any state changes. For oracles like TWAP this will be different from peek().

    /// For example:



Found in 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol (function _safeApprove(address token, address to, uint256 value) internal {):

        swapData.amountData = swapAmountData;

        swapData.yieldBoxData = swapYBData;

    }



    function _safeApprove(address token, address to, uint256 value) internal {

        (bool success, bytes memory data) = token.call(

            abi.encodeWithSelector(0x095ea7b3, to, value)

        );

        require(

            success && (data.length == 0 || abi.decode(data, (bool))),

            "BaseSwapper::safeApprove: approve failed"



Found in 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol (function _safeApprove(address token, address to, uint256 value) private {):

            successEmtptyApproval,

            "OperationsLib::safeApprove: approval reset failed"

        );



        // solhint-disable-next-line avoid-low-level-calls

        (bool success, bytes memory data) = token.call(

            abi.encodeWithSelector(

                bytes4(keccak256("approve(address,uint256)")),

                to,

                value

            )



Found in 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol (function compoundAmount() public view returns (uint256 result) {):

        return "Curve-Tricrypto strategy for TricryptoLP";

    }



    /// @notice returns compounded amounts in wrappedNative

    function compoundAmount() public view returns (uint256 result) {

        (bool success, bytes memory response) = address(lpGauge).staticcall(

            abi.encodeWithSignature("claimable_tokens(address)", address(this))

        );

        result = 0;

        uint256 claimable = 0;

        if (success) {


------------------------------------------------------------------------ 

### Mitigation 

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.










# VULN 19 

## [GAS] Empty blocks should be removed or emit something
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 51 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

    {}


Found in line 68 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    receive() external payable {}


Found in line 28 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in line 256 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 271 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 287 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 22 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in line 257 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 272 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 288 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 25 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in line 267 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 282 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 298 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 109 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

    constructor(string memory name) EIP712(name, "1") {}


Found in line 114 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

    function totalSupply() public view virtual override returns (uint256) {}


Found in line 98 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

    constructor() MarketERC20("Tapioca BigBang") {}


Found in line 429 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

    ) public override returns (bool) {}


Found in line 435 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

    ) public override returns (bool) {}


Found in line 148 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol:

    constructor() MarketERC20("Tapioca Singularity") {}


Found in line 195 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol:

    function _accrue() internal virtual override {}


Found in line 644 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

    receive() external payable {}


Found in line 40 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

    constructor(string memory name) EIP712(name, "1") {}


Found in line 342 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    receive() external payable {}


Found in line 59 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol:

    {}


Found in line 43 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    receive() external payable {}


Found in line 42 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

    {}


Found in line 273 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 288 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 304 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                {} catch Error(string memory reason) {


Found in line 38 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

    {}


Found in line 37 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

    {}


Found in line 272 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 287 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 303 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                {} catch Error(string memory reason) {


Found in line 43 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

    {}


Found in line 297 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 312 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 328 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                {} catch Error(string memory reason) {


Found in line 340 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol:

    receive() external payable virtual {}


Found in line 707 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        {} catch {


Found in line 273 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

            try gmxVester.withdraw() {} catch {}


Found in line 98 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    function compound(bytes memory) public {}


Found in line 271 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    receive() external payable {}


Found in line 97 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    function compound(bytes memory) public {}


Found in line 164 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    receive() external payable {}


Found in line 101 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    function compound(bytes memory) public {}


Found in line 169 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    receive() external payable {}


Found in line 117 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    function compound(bytes memory) public {}


Found in line 301 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    receive() external payable {}


Found in line 37 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

    constructor() ERC721("Option TAP", "oTAP") ERC721Permit("Option TAP") {}


Found in line 49 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    ) OFTV2(_name, _symbol, _sharedDec, _lzEndpoint) {}


Found in line 138 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        try twTap.participate(to, amount, duration) {} catch Error(


Found in line 330 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    receive() external payable virtual {}


Found in line 344 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

            {} catch Error(string memory reason) {

------------------------------------------------------------------------ 

### Mitigation 

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}).










# VULN 20 

## [GAS] bytes constants are more eficient than string constans
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 8 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

    string public _name;


Found in line 9 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

    string public _symbol;


Found in line 24 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

    string public _name;


Found in line 25 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/SGOracle.sol:

    string public _symbol;


Found in line 31 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    string public _name;


Found in line 32 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/ARBTriCryptoOracle.sol:

    string public _symbol;


Found in line 26 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    string public constant override name = "sGLP";


Found in line 27 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    string public constant override description =

------------------------------------------------------------------------ 

### Mitigation 

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.










# VULN 21 

## [GAS] Use a more recent version of solidity
------------------------------------------------------------------------ 

### Proof of concept 

Found in 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/implentations/GLPOracle.sol at line 2: pragma solidity >=0.8.0;
------------------------------------------------------------------------ 

### Mitigation 

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath * Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining * Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads * Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings * Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value * In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller. * In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.










# VULN 22 

## [GAS] Use assembly to write address storage values
------------------------------------------------------------------------ 

### Proof of concept 

Found in lines 86 to 91 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

    constructor(
        YieldBox _yieldBox,
        IERC20 tapToken_,
        IERC20 wethToken_,
        address _owner
    ) {


Found in lines 35 to 42 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox,
        address _owner,
        address payable _leverageModule,
        address payable _marketModule,
        address payable _optionsModule
    )


Found in lines 67 to 74 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox,
        address _owner,
        address payable _leverageModule,
        address payable _marketModule,
        address payable _optionsModule
    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) ERC20Permit("USDO") {


Found in lines 70 to 73 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox
    ) OFTV2("USDO", "USDO", 8, _lzEndpoint) {


Found in lines 25 to 28 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox
    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in lines 19 to 22 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox
    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in lines 22 to 25 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox
    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}


Found in lines 122 to 126 at 2023-07-tapioca/tapiocaz-audit/contracts/Balancer.sol:

    constructor(
        address _routerETH,
        address _router,
        address _owner
    ) Owned(_owner) {


Found in lines 33 to 45 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    )


Found in lines 49 to 61 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    )


Found in lines 45 to 53 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )


Found in lines 50 to 62 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    )


Found in lines 24 to 32 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )


Found in lines 20 to 28 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )


Found in lines 19 to 27 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )


Found in lines 25 to 33 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )


Found in lines 12 to 27 at 2023-07-tapioca/tapioca-periph-audit/contracts/oracle/Seer.sol:

    constructor(
        string memory __name,
        string memory __symbol,
        uint8 _decimals,
        address[] memory addressInAndOutUni,
        IUniswapV3Pool[] memory _circuitUniswap,
        uint8[] memory _circuitUniIsMultiplied,
        uint32 _twapPeriod,
        uint16 observationLength,
        uint8 _uniFinalCurrency,
        address[] memory _circuitChainlink,
        uint8[] memory _circuitChainIsMultiplied,
        uint32 _stalePeriod,
        address[] memory guardians,
        bytes32 _description
    )


Found in lines 30 to 34 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol:

    constructor(
        address _router,
        address _factory,
        IYieldBox _yieldBox
    )


Found in lines 36 to 39 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

    constructor(
        ICurvePool _curvePool,
        IYieldBox _yieldBox
    ) validAddress(address(_curvePool)) validAddress(address(_yieldBox)) {


Found in lines 53 to 58 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        IGmxRewardRouterV2 _gmxRewardRouter,
        IGmxRewardRouterV2 _glpRewardRouter,
        IERC20 _sGlp
    ) BaseERC20Strategy(_yieldBox, address(_sGlp)) {


Found in lines 84 to 92 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _rewadPool,
        address _booster,
        address _zap,
        address _lpGetter,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 51 to 55 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _vault
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 63 to 70 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _lpGauge,
        address _lpGetter,
        address _minter,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, ITricryptoLPGetter(_lpGetter).lpToken()) {


Found in lines 62 to 69 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _lpGauge,
        address _lpGetter,
        address _minter,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 67 to 76 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _ethRouter,
        address _lpStaking,
        uint256 _stakingPid,
        address _lpToken,
        address _swapper,
        address _stgEthPool
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 58 to 65 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _lendingPool,
        address _incentivesController,
        address _receiptToken,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 50 to 54 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _cToken
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 52 to 57 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _stEth,
        address _curvePool
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 58 to 65 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _vault,
        bytes32 _poolId,
        address _bal,
        address _helpers
    ) BaseERC20Strategy(_yieldBox, _token) {


Found in lines 67 to 70 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

    constructor(
        address _yieldBox,
        address _owner
    )


Found in lines 81 to 88 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

    constructor(
        address _tOLP,
        address _oTAP,
        address payable _tapOFT,
        address _paymentTokenBeneficiary,
        uint256 _epochDuration,
        address _owner
    ) {


Found in lines 39 to 41 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

    constructor(
        address _owner
    ) ERC721("Airdrop Option TAP", "aoTAP") ERC721Permit("Airdrop Option TAP") {


Found in lines 98 to 104 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    constructor(
        address _aoTAP,
        address payable _tapOFT,
        address _pcnft,
        address _paymentTokenBeneficiary,
        address _owner
    ) {


Found in lines 44 to 49 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

    constructor(
        string memory _name,
        string memory _symbol,
        uint8 _sharedDec,
        address _lzEndpoint
    ) OFTV2(_name, _symbol, _sharedDec, _lzEndpoint) {}


Found in lines 107 to 117 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

    constructor(
        address _lzEndpoint,
        address _contributors,
        address _earlySupporters,
        address _supporters,
        address _lbp,
        address _dao,
        address _airdrop,
        uint256 _governanceChainId,
        address _conservator
    ) BaseTapOFT("TapOFT", "TAP", 8, _lzEndpoint) ERC20Permit("TapOFT") {


Found in lines 115 to 121 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    constructor(
        address payable _tapOFT,
        address _owner,
        address _layerZeroEndpoint,
        uint256 _hostChainID,
        uint256 _minGas
    )

------------------------------------------------------------------------ 

### Mitigation 

Use assembly to write address storage values. Here are a few reasons: * Reduced opcode usage: When using assembly, you can directly manipulate storage values using lower-level instructions like sstore (storage store) instead of relying on higher-level Solidity storage assignments. These direct operations typically result in fewer opcode executions, reducing gas costs. * Avoiding unnecessary checks: Solidity storage assignments often involve additional checks and operations, such as enforcing security modifiers or triggering events. By using assembly, you can bypass these additional checks and perform the necessary storage operations directly, resulting in gas savings. * Optimized packing: Assembly provides greater flexibility in packing and unpacking data structures. By carefully arranging and manipulating the storage layout in assembly, you can achieve more efficient storage utilization and minimize wasted storage space. * Fine-grained control: Assembly allows for precise control over gas-consuming operations. You can optimize gas usage by selecting specific instructions and minimizing unnecessary operations or data copying.










# VULN 23 

## [GAS] Use ERC721A instead ERC721
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 469 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            IERC721(lockData.target).approve(


Found in line 478 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            IERC721(oTapAddress).approve(address(this), oTAPTokenId);


Found in line 479 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            IERC721(oTapAddress).safeTransferFrom(


Found in line 524 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            address ownerOfTapTokenId = IERC721(oTapAddress).ownerOf(


Found in line 532 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                IERC721(oTapAddress).safeTransferFrom(


Found in line 543 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                IERC721(oTapAddress).safeTransferFrom(


Found in line 30 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

contract OTAP is ERC721, ERC721Permit, BaseBoringBatchable {


Found in line 37 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

    constructor() ERC721("Option TAP", "oTAP") ERC721Permit("Option TAP") {}


Found in line 49 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

    ERC721,


Found in line 50 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

    ERC721Permit,


Found in line 71 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        ERC721("TapiocaOptionLiquidityProvision", "tOLP")


Found in line 72 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        ERC721Permit("TapiocaOptionLiquidityProvision")


Found in line 32 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

contract AOTAP is ERC721, ERC721Permit, BaseBoringBatchable, BoringOwnable {


Found in line 41 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

    ) ERC721("Airdrop Option TAP", "aoTAP") ERC721Permit("Airdrop Option TAP") {


Found in line 48 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

    IERC721 public immutable PCNFT;


Found in line 108 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        PCNFT = IERC721(_pcnft);


Found in line 71 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

contract TwTAP is TWAML, ONFT721, ERC721Permit {


Found in line 123 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        ERC721Permit("Time Weighted TAP")


Found in line 584 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

    ) public view virtual override(ONFT721, ERC721) returns (bool) {

------------------------------------------------------------------------ 

### Mitigation 

ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereum’s sky-rocketing gas fee. Reference: https://nextrope.com/erc721-vs-erc721a-2/










# VULN 24 

## [GAS] Using delete instead of setting struct 0 saves gas
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 256 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                    pool.cumulative = 0;


Found in line 318 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

                    pool.cumulative = 0;


Found in line 293 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                    pool.cumulative = 0;


Found in line 547 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

                    pool.cumulative = 0;

------------------------------------------------------------------------ 

### Mitigation 

When you define a struct in Solidity, each member of the struct occupies a specific slot in storage. When you use the delete keyword on a struct, it effectively clears the entire storage slot associated with the struct, resetting all its members to their default values.










# VULN 25 

## [GAS] require() or revert() statements that check input arguments should be at the top of the function (Also restructured some if’s)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 174 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(


Found in line 182 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(


Found in line 190 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(!paused, "Penrose: paused");


Found in line 242 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(address(swappers_[0]) != address(0), "Penrose: zero address");


Found in line 243 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(address(markets_[0]) != address(0), "Penrose: zero address");


Found in line 438 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

            require(


Found in line 446 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

                require(success[i], _getRevertMsg(result[i]));


Found in line 506 at 2023-07-tapioca/tapioca-bar-audit/contracts/Penrose.sol:

        require(isMarketRegistered[address(market)], "Penrose: Invalid market");


Found in line 26 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(!paused, "USDO: paused");


Found in line 87 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(token == address(this), "USDO: token not valid");


Found in line 88 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(maxFlashLoan(token) >= amount, "USDO: amount too big");


Found in line 89 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(amount > 0, "USDO: amount not valid");


Found in line 93 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(


Found in line 100 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/USDO.sol:

        require(_allowance >= (amount + fee), "USDO: repay not approved");


Found in line 355 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

            revert("USDO: module not found");


Found in line 371 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

            revert(_getRevertMsg(returnData));


Found in line 498 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol:

                revert("OFTCoreV2: unknown packet type");


Found in line 185 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 258 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                        revert(reason);


Found in line 273 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                        revert(reason);


Found in line 289 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol:

                        revert(reason);


Found in line 196 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 259 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                        revert(reason);


Found in line 274 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                        revert(reason);


Found in line 290 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol:

                        revert(reason);


Found in line 184 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 269 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                        revert(reason);


Found in line 284 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                        revert(reason);


Found in line 300 at 2023-07-tapioca/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol:

                        revert(reason);


Found in line 116 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(!paused, "Market: paused");


Found in line 126 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(_isSolvent(from, exchangeRate), "Market: insolvent");


Found in line 131 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

        require(!initialized, "Market: initialized");


Found in line 172 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_borrowOpeningFee <= FEE_PRECISION, "Market: not valid");


Found in line 193 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_callerFee <= FEE_PRECISION, "Market: not valid");


Found in line 198 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_protocolFee <= FEE_PRECISION, "Market: not valid");


Found in line 203 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(


Found in line 211 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");


Found in line 212 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(


Found in line 220 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(_maxLiquidatorReward < FEE_PRECISION, "Market: not valid");


Found in line 221 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(


Found in line 234 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/Market.sol:

            require(


Found in line 142 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

            require(srcBalance >= amount, "ERC20: balance too low");


Found in line 144 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas


Found in line 167 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

            require(srcBalance >= amount, "ERC20: balance too low");


Found in line 173 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                    require(


Found in line 179 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

                require(to != address(0), "ERC20: no zero address"); // Moved down so other failed calls safe some gas


Found in line 261 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        require(block.timestamp <= deadline, "ERC20Permit: expired deadline");


Found in line 277 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/MarketERC20.sol:

        require(signer == owner, "ERC20Permit: invalid signature");


Found in line 133 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(


Found in line 219 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            require(success || !revertOnFail, _getRevertMsg(result));


Found in line 344 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 371 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 391 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 413 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 476 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                require(_minDebtRate < maxDebtRate, "BigBang: not valid");


Found in line 482 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                require(_maxDebtRate > minDebtRate, "BigBang: not valid");


Found in line 496 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

                require(


Found in line 593 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(penrose.swappers(swapper), "BigBang: Invalid swapper");


Found in line 680 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 699 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

            require(


Found in line 750 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(


Found in line 820 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol:

        require(borrowAmount != 0, "SGL: solvent");


Found in line 29 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(


Found in line 65 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(


Found in line 104 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 126 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 155 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 182 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol:

        require(amountOut >= minAmountOut, "SGL: not enough");


Found in line 187 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

            require(


Found in line 240 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol:

        require(_totalAsset.base >= 1000, "SGL: min limit");


Found in line 100 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

        require(


Found in line 191 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(success || !revertOnFail, _getRevertMsg(result));


Found in line 507 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(


Found in line 522 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(


Found in line 534 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(


Found in line 554 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(


Found in line 566 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(_liquidationMultiplier < FEE_PRECISION, "SGL: not valid");


Found in line 582 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            require(_liquidationQueue.onlyOnce(), "SGL: LQ not initalized");


Found in line 612 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            revert("SGL: module not set");


Found in line 627 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            revert(_getRevertMsg(returnData));


Found in line 640 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/Singularity.sol:

            revert(_getRevertMsg(returnData));


Found in line 152 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(allBorrowAmount != 0, "SGL: solvent");


Found in line 264 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(borrowAmount != 0, "SGL: solvent");


Found in line 327 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(penrose.swappers(swapper), "SGL: Invalid swapper");


Found in line 397 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol:

        require(liquidatedCount > 0, "SGL: no users found");


Found in line 66 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        require(


Found in line 75 at 2023-07-tapioca/tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol:

        require(_totalAsset.base >= 1000, "SGL: min limit");


Found in line 51 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");


Found in line 58 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(signer == owner, "YieldBoxPermit: invalid signature");


Found in line 80 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");


Found in line 87 at 2023-07-tapioca/yieldbox/contracts/YieldBoxPermit.sol:

        require(signer == owner, "YieldBoxPermit: invalid signature");


Found in line 46 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

        require(block.chainid == hostChainID, "TOFT_host");


Found in line 354 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

            require(


Found in line 397 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

            revert("TOFT_module");


Found in line 413 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

            revert(_getRevertMsg(returnData));


Found in line 570 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol:

                revert("TOFT_packet");


Found in line 174 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 275 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                        revert(reason);


Found in line 290 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                        revert(reason);


Found in line 306 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol:

                        revert(reason);


Found in line 56 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 98 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

        require(amount > 0, "TOFT_0");


Found in line 167 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 211 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 274 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                        revert(reason);


Found in line 289 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                        revert(reason);


Found in line 305 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol:

                        revert(reason);


Found in line 199 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor


Found in line 299 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                        revert(reason);


Found in line 314 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                        revert(reason);


Found in line 330 at 2023-07-tapioca/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol:

                        revert(reason);


Found in line 28 at 2023-07-tapioca/tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol:

        require(


Found in line 32 at 2023-07-tapioca/tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol:

        require(


Found in line 43 at 2023-07-tapioca/tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol:

        require(


Found in line 205 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                require(


Found in line 710 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

                revert("MagnetarV2: action not valid");


Found in line 714 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        require(msg.value == valAccumulator, "MagnetarV2: value mismatch");


Found in line 1058 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

            revert("MagnetarV2: module not found");


Found in line 1086 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol:

        revert(abi.decode(_returnData, (string))); // All that remains is the revert string


Found in line 456 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    require(


Found in line 464 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            require(


Found in line 468 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            require(tOLPTokenId != 0, "Magnetar: tOLPTokenId 0");


Found in line 510 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            require(


Found in line 527 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

            require(


Found in line 556 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

                    require(


Found in line 743 at 2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol:

        require(withdrawData.length > 0, "MagnetarV2: withdrawData is empty");


Found in line 90 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        require(msg.value == valAccumulator, "Multicall3: value mismatch");


Found in line 102 at 2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol:

        revert(abi.decode(_returnData, (string))); // All that remains is the revert string


Found in line 156 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        require(outputAmount >= amountOutMin, "insufficient-amount-out");


Found in line 164 at 2023-07-tapioca/tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol:

        require(balanceAfter > balanceBefore, "swap failed");


Found in line 60 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(address(weth) == _gmxRewardRouter.weth(), "WETH mismatch");


Found in line 62 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router");


Found in line 66 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(_gmx != address(0), "Bad GMX reward router");


Found in line 333 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol:

        require(amountOut * priceDenom >= gmxAmount * priceNum, "Not enough");


Found in line 245 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        require(


Found in line 249 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        require(


Found in line 340 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        require(


Found in line 363 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        require(


Found in line 376 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol:

        require(


Found in line 242 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol:

        require(


Found in line 235 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol:

        require(


Found in line 262 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol:

        require(


Found in line 155 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol:

        require(


Found in line 162 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol:

        require(queued >= amount, "LidoStrategy: not enough");


Found in line 184 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        require(


Found in line 208 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        require(


Found in line 263 at 2023-07-tapioca/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol:

        require(


Found in line 12 at 2023-07-tapioca/tap-token-audit/contracts/twAML.sol:

        require(denominator > 0);


Found in line 44 at 2023-07-tapioca/tap-token-audit/contracts/twAML.sol:

        require(prod1 < denominator);


Found in line 68 at 2023-07-tapioca/tap-token-audit/contracts/Vesting.sol:

        require(_duration > 0, "Vesting: no vesting");


Found in line 40 at 2023-07-tapioca/tap-token-audit/contracts/options/oTAP.sol:

        require(msg.sender == broker, "OTAP: only onlyBroker");


Found in line 177 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_lockDuration > 0, "tOLP: lock duration must be > 0");


Found in line 178 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(_amount > 0, "tOLP: amount must be > 0");


Found in line 181 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(sglAssetID > 0, "tOLP: singularity not active");


Found in line 215 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(


Found in line 220 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(


Found in line 226 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(


Found in line 282 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol:

        require(


Found in line 170 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(


Found in line 175 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Option expired");


Found in line 187 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");


Found in line 190 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(tapAmount >= 1e18, "tOB: Too low");


Found in line 219 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Position is not active");


Found in line 220 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(lock.lockDuration >= EPOCH_DURATION, "tOB: Duration too short");


Found in line 224 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(


Found in line 303 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(


Found in line 368 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(


Found in line 372 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(


Found in line 376 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(isPositionActive, "tOB: Option expired");


Found in line 388 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");


Found in line 391 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(chosenAmount >= 1e18, "tOB: Too low");


Found in line 419 at 2023-07-tapioca/tap-token-audit/contracts/options/TapiocaOptionBroker.sol:

        require(singularities.length > 0, "tOB: No active singularities");


Found in line 46 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/aoTAP.sol:

        require(msg.sender == broker, "AOTAP: only onlyBroker");


Found in line 158 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");


Found in line 167 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 174 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "adb: Too high");


Found in line 177 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(tapAmount >= 1e18, "adb: Too low");


Found in line 233 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");


Found in line 242 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 246 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 255 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(eligibleTapAmount >= _tapAmount, "adb: Too high");


Found in line 258 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(chosenAmount >= 1e18, "adb: Too low");


Found in line 419 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 425 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 449 at 2023-07-tapioca/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol:

        require(


Found in line 73 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                revert("TOFT_packet");


Found in line 104 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(duration > 0, "TapOFT: Small duration");


Found in line 222 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");


Found in line 307 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

        require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");


Found in line 346 at 2023-07-tapioca/tap-token-audit/contracts/tokens/BaseTapOFT.sol:

                    revert(reason);


Found in line 89 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(!paused, "TAP: paused");


Found in line 118 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

        require(_lzEndpoint != address(0), "LZ endpoint not valid");


Found in line 127 at 2023-07-tapioca/tap-token-audit/contracts/tokens/TapOFT.sol:

            require(


Found in line 313 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(expiry < type(uint56).max, "twTAP: too long");


Found in line 530 at 2023-07-tapioca/tap-token-audit/contracts/governance/twTAP.sol:

        require(position.expiry <= block.timestamp, "twTAP: Lock not expired");

------------------------------------------------------------------------ 

### Mitigation 

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.