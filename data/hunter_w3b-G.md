# Gas Optimization

While striving for enhanced code clarity in the provided snippets, certain functions have been abbreviated to emphasize affected sections.

Developers should remain vigilant during the incorporation of these proposed modifications to avert potential vulnerabilities. Despite prior testing of the optimizations, developers bear the responsibility of conducting comprehensive reevaluation.

Conducting code reviews and additional testing is highly recommended to mitigate any plausible hazards that may arise from the refactoring endeavor

# Summary

| Number | Issue                                                                                   | Instances |
| :----: | :-------------------------------------------------------------------------------------- | :-------: |
| [G-01] | Use `calldata` instead of memory for function parameters                                |    12     |
| [G-02] | Should use `arguments` instead of state variable                                        |    56     |
| [G-03] | Can Make The Variable Outside The Loop To Save Gas                                      |    14     |
| [G-04] | Ensure `Non-Zero` Amounts before Initiating Transfers To Save Gas                       |    18     |
| [G-05] | Public Functions Not Called By The Contract Should Be Declared External Instead         |    73     |
| [G-06] | Efficient Write Of `Address Storage` with Assembly To Save Gas                          |    71     |
| [G-07] | Achieving Gas Savings Optimizing Value Assignment to Structures                         |     4     |
| [G-08] | Use Bitmap To Save Gas                                                                  |     6     |
| [G-09] | Using `> 0` costs more gas than `!= 0` when used on a uint in a require() statement     |    15     |
| [G-10] | Use Nested If statements instead of `&&` avoid multiple check combinations              |    10     |
| [G-11] | Make 3 event indexed when possible                                                      |    83     |
| [G-12] | Use hardcode address instead `address(this)`                                            |    220    |
| [G-13] | Sort Solidity operations using `short-circuit` mode                                     |     8     |
| [G-14] | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements  |     7     |
| [G-15] | `abi.encode()` is less efficient than `abi.encodePacked()`                              |    35     |
| [G-16] | Use constants instead of `type(uintx).max`                                              |    30     |
| [G-17] | Use `assembly` for loops                                                                |     8     |
| [G-18] | Use assembly to check for `address(0)`                                                  |     7     |
| [G-19] | Bytes `constants` are more efficient than String constants                              |     1     |
| [G-20] | Use `selfbalance()` instead of `address(this).balance`                                  |    15     |
| [G-21] | Use `of emit inside` a loop                                                             |     1     |
| [G-22] | Use assembly to hash instead of solidity                                                |     3     |
| [G-23] | Use ERC721A instead ERC721                                                              |     5     |
| [G-24] | With assembly, `.call` (bool success)  transfer can be done gas-optimized               |     8     |
| [G-25] | `Require() or Revert()` statement should be used sorted from cheapest to most expensive |     1     |
| [G-26] | Use assembly to emit events                                                             |    13     |

## [G-01] Use `calldata` instead of memory for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

When you specify a data location as memory, that value will be copied into memory. When you specify the location as calldata, the value will stay static within calldata. If the value is a large, complex type, using memory may result in extra memory expansion costs.

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract.

Penrose.sol.`bigBangMarkets()`: use calldata for `data`

Penrose.sol.`_getMasterContractLength()`: use calldata for `array`

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L209

```solidity
file:  contracts/Penrose.sol


424        function executeMarketFn(
425            address[] calldata mc,
426            bytes[] memory data,
427            bool forceSuccess
428        )
429        external


542       function _getMasterContractLength(
543           IPenrose.MasterContract[] memory array
544     ) public view returns

```

```diff
424        function executeMarketFn(
425            address[] calldata mc,
-              bytes[] memory data,
+              bytes[] calldata data,
427            bool forceSuccess
428        )
429        external


542       function _getMasterContractLength(
-           IPenrose.MasterContract[] memory array
+           IPenrose.MasterContract[] calldata array
544     ) public view returns
```

BaseTapOFT.sol.`claimRewards()`: use calldata for rewardTokens

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L163-L171

```solidity
file:   contracts/tokens/BaseTapOFT.sol

163        function claimRewards(
164        address to,
165        uint256 tokenID,
166        address[] memory rewardTokens,
167        uint16 lzDstChainId,
168        address zroPaymentAddress,
169        bytes calldata adapterParams,
170        IRewardClaimSendFromParams[] calldata rewardClaimSendParams
171   ) external payable



332    function _callApproval(ICommonData.IApproval[] memory approvals) private

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L361-L364

```diff
file:    contracts/governance/twTAP.sol

361        function claimAndSendRewards(
362        uint256 _tokenId,
-        IERC20[] memory _rewardTokens
+        IERC20[] calldata _rewardTokens
364    ) external




499       function _claimRewardsOn(
500        uint256 _tokenId,
501        address _to,
-        IERC20[] memory _rewardTokens
+        IERC20[] calldata _rewardTokens
503    ) internal

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L243

```solidity
file:    contracts/usd0/modules/USDOMarketModule.sol

243       function _callApproval(ICommonData.IApproval[] memory approvals) private

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L214

```solidity
file:   contracts/usd0/modules/USDOOptionsModule.sol

214            ICommonData.IApproval[] memory approvals
215    ) public


244   function _callApproval(ICommonData.IApproval[] memory approvals) private
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L965-L968

```solidity
file:   contracts/Magnetar/MagnetarV2.sol

965       function _singularityMarketInfo(
966        address who,
967        ISingularity[] memory markets


```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L189

```solidity

file:    contracts/convex/ConvexTricryptoStrategy.sol

189        function compound(bytes memory data) public

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

```solidity
File:  tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol

223        ICommonData.IApproval[] memory approvals
```

## [G-02] Should use `arguments` instead of state variable

Using state variables within the emit statement is not recommended as it can lead to unnecessary gas consumption during contract execution. By avoiding this practice, substantial gas savings of up to 97 units can be achieved, contributing to more efficient and cost-effective smart contract deployments.

State variables, which are variables declared at the contract level and store persistent data, can have complex gas costs when accessed within emit statements. Emitting an event involves recording data on the blockchain, and including state variables can cause additional gas overhead due to their storage and retrieval operations.

To optimize gas usage and enhance contract performance, it is advisable to avoid referencing state variables directly in emit statements. Instead, developers should pass the required data as arguments to the emit function, utilizing local variables or function parameters when possible.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L293

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol


293        emit NewEpoch(epoch, epochTAPValuation);

```

```diff
diff --git a/AirdropBroker.sol b/AirdropBroker.org.sol
index 9add6cd..fba0949 100644
--- a/AirdropBrokernot.sol
+++ b/AirdropBroker.org.sol
@@ -4,9 +4,6 @@
             "adb: too soon"
         );

-        uint64 Epoch = epoch;
-        uint128 EpochTAPValuation = epochTAPValuation;
-
         // Update epoch info
         lastEpochUpdate = uint64(block.timestamp);
         epoch++;
@@ -14,5 +11,5 @@
         // Get epoch TAP valuation
         (, uint256 _epochTAPValuation) = tapOracle.get(tapOracleData);
         epochTAPValuation = uint128(_epochTAPValuation);
-        emit NewEpoch(Epoch, EpochTAPValuation);
+        emit NewEpoch(epoch, epochTAPValuation);
     }

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L154

```solidity
file: /contracts/tokens/TapOFT.sol


154        emit PausedUpdated(paused, val);

162        emit MinterUpdated(minter, _minter);

```

```diff

diff --git a/TapOFT.sol b/TapOFT.org.sol
index b97c861..5d359c2 100644
--- a/TapOFT.sol
+++ b/TapOFT.org.sol
@@ -151,8 +151,7 @@ contract TapOFT is BaseTapOFT, ERC20Permit {
     /// @param val the new value
     function updatePause(bool val) external onlyOwner {
         require(val != paused, "TAP: same state");
-        bool _paused = paused;
-        emit PausedUpdated(_paused, val);
+        emit PausedUpdated(paused, val);
         paused = val;
     }

@@ -160,8 +159,7 @@ contract TapOFT is BaseTapOFT, ERC20Permit {
     /// @param _minter the new address
     function setMinter(address _minter) external onlyOwner {
         require(_minter != address(0), "address not valid");
-        address _minter = minter;
-        emit MinterUpdated(_minter, _minter);
+        emit MinterUpdated(minter, _minter);
         minter = _minter;
     }


```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L293

```solidity
File:  contracts/option-airdrop/AirdropBroker.sol

293    emit NewEpoch(epoch, epochTAPValuation);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L123

```solidity
File: contracts/option-airdrop/aoTAP.sol


123  emit Mint(_to, tokenId, option);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L110

```solidity
File:  contracts/options/oTAP.sol

110          emit Mint(_to, tokenId, option);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L264

```solidity
File: contracts/options/TapiocaOptionBroker.sol

264         emit AMLDivergence(
265                epoch,
266                pool.cumulative,
267                pool.averageMagnitude,
268                pool.totalParticipants
269            ); // Register new voting power event



285     emit Participate(
286            epoch,
287            lock.sglAssetID,
288            pool.totalDeposited,
289            lock,
290            target
291        );



328        emit AMLDivergence(
329                epoch,
330                pool.cumulative,
331                pool.averageMagnitude,
332                pool.totalParticipants
333            ); // Register new voting power event



344   emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);



431   emit NewEpoch(epoch, epochTAP, epochTAPValuation);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L104

```solidity
File:  contracts/options/TapiocaOptionLiquidityProvision.sol


104   emit UpdateTotalSingularityPoolWeights(totalSingularityPoolWeights);


200   emit Mint(_to, uint128(sglAssetID), lockPosition);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L143

```solidity
File:  contracts/tokens/TapOFT.sol

143  emit GovernanceChainIdentifierUpdated(
144            governanceChainIdentifier,
145            _identifier
146        );



154    emit PausedUpdated(paused, val);


162    emit MinterUpdated(minter, _minter);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L274

```solidity
File:  contracts/Penrose.sol


274  emit PausedUpdated(paused, val);


283  emit ConservatorUpdated(conservator, _conservator);


310  emit UsdoTokenUpdated(_usdoToken, usdoAssetId);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L144

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/Market.sol


144   emit LogBorrowingFee(borrowOpeningFee, _val);

152   emit LogBorrowCapUpdated(totalBorrowCap, _cap);

173   emit LogBorrowingFee(borrowOpeningFee, _borrowOpeningFee);

188   emit ConservatorUpdated(conservator, _conservator);

229   emit LogBorrowCapUpdated(totalBorrowCap, _totalBorrowCap);

248   emit PausedUpdated(paused, val);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L483

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol


483   emit MaxDebtRateUpdated(maxDebtRate, _maxDebtRate);


488    emit DebtRateAgainstEthUpdated(
489                    debtRateAgainstEthMarket,
490                    _debtRateAgainstEthMarket
491                );



500   emit LiquidationMultiplierUpdated(
501                    liquidationMultiplier,
502                    _liquidationMultiplier
503                );
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L499

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol
499  emit MinimumTargetUtilizationUpdated(
                minimumTargetUtilization,
                _minimumTargetUtilization
            );
511  emit MaximumTargetUtilizationUpdated(
                maximumTargetUtilization,
                _maximumTargetUtilization
            );

526   emit MinimumInterestPerSecondUpdated(
                minimumInterestPerSecond,
                _minimumInterestPerSecond
            );
538   emit MaximumInterestPerSecondUpdated(
                maximumInterestPerSecond,
                _maximumInterestPerSecond
            );
546   emit InterestElasticityUpdated(
                interestElasticity,
                _interestElasticity
            );

558  emit LqCollateralizationRateUpdated(
                lqCollateralizationRate,
                _lqCollateralizationRate
            );
567   emit LiquidationMultiplierUpdated(
                liquidationMultiplier,
                _liquidationMultiplier
            );
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L98

```solidity
File:  contracts/usd0/BaseUSDO.sol

98   emit FlashMintFeeUpdated(flashMintFee, _val);

107  emit ConservatorUpdated(conservator, _conservator);

117  emit PausedUpdated(paused, val);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L123

```solidity
File: contracts/aave/AaveStrategy.sol


123    emit DepositThreshold(depositThreshold, amount);

130    emit MultiSwapper(address(swapper), _swapper);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L90

```solidity
File:  contracts/compound/CompoundStrategy.sol

90   emit DepositThreshold(depositThreshold, amount);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L164

```solidity
File:  contracts/convex/ConvexTricryptoStrategy.sol


164   emit DepositThreshold(depositThreshold, amount);


171   emit MultiSwapper(address(swapper), _swapper);


180    emit LPGetterSet(address(lpGetter), _lpGetter);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L135

```solidity
File: contracts/curve/TricryptoLPStrategy.sol



135     emit DepositThreshold(depositThreshold, amount);


142     emit MultiSwapper(address(swapper), _swapper);


151     emit LPGetterSet(address(lpGetter), _lpGetter);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L126

```solidity
File:  contracts/curve/TricryptoNativeStrategy.sol


126  emit DepositThreshold(depositThreshold, amount);


133   emit MultiSwapper(address(swapper), _swapper);


142   emit LPGetterSet(address(lpGetter), _lpGetter);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L94

```solidity
File: contracts/lido/LidoEthStrategy.sol

94   emit DepositThreshold(depositThreshold, amount);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L143

```solidity
File:  contracts/stargate/StargateStrategy.sol


143   emit DepositThreshold(depositThreshold, amount);

150  emit MultiSwapper(address(swapper), _swapper);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L91

```solidity
File:  contracts/yearn/YearnStrategy.so

91   emit DepositThreshold(depositThreshold, amount);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L120

```solidity
File:  contracts/tOFT/mTapiocaOFT.sol


120  emit ConnectedChainStatusUpdated(
121            _chain,
122            connectedChains[_chain],
123            _status
124        );
```

## [G-03] Can Make The Variable Outside The Loop To Save Gas

In Solidity, declaring variables inside loops can lead to unnecessary gas costs due to the creation of new instances of the variable for each iteration. To enhance the efficiency of your contract and reduce gas consumption, it is recommended to declare such variables outside the loop.

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;

        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }

        return total;
    }
}
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L235

```solcontracts/governance/twTAP.sol

235      uint256 net = cur.totalDistPerVote[i] - prev.totalDistPerVote[i];

488      uint256 amount = amounts[i];

508      uint256 claimableIndex = rewardTokenIndex[_rewardTokens[i]];

509      uint256 amount = amounts[i];
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L566

```solidity
File: contracts/options/TapiocaOptionBroker.sol


566   uint256 currentPoolWeight = sglPools[i].poolWeight;



567   uint256 quotaPerSingularity = muldiv(
568                    currentPoolWeight,
569                    _epochTAP,
570                    totalWeights
571                );
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L565

```solidity
File: contracts/Penrose.sol

565      address mcLocation = array[i].location;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L216

```solidity
File:  contracts/markets/bigBang/BigBang.sol

667         address user = users[i];
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L108

```solidity
File:  contracts/markets/singularity/SGLLiquidation.sol

108       address user = users[i];

385       address user = users[i];
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L188

```solidity
File: contracts/markets/singularity/Singularity.sol

188            (bool success, bytes memory result) = address(this).delegatecall(
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L978

```solidity
File: contracts/Magnetar/MagnetarV2.sol

978      (uint128 totalAssetElastic, uint128 totalAssetBase) = sgl //

1008     (uint64 debtRate, uint64 lastAccrued) = bigBang.accrueInfo();

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L73

```solidity
File:  contracts/Multicall/Multicall3.sol

73            uint256 val = calli.value;
```

## [G-04] Ensure `Non-Zero` Amounts before Initiating Transfers To Save Gas

Optimizing Gas Usage: `Adding Non-Zero Value Check for Transfers`

To improve gas efficiency and minimize the cost of external calls, it is advisable to include a non-zero-value check before executing token transfers in the smart contract. By consistently applying this check throughout the solution, unnecessary gas expenses can be reduced, resulting in more cost-effective contract operations. Here's an example of where the non-zero-value check can be implemented:

By introducing this non-zero-value check before performing token transfers, your smart contract can avoid unnecessary external calls for zero-value transfers and optimize gas consumption. It is essential to ensure that this check is consistently applied in all relevant functions to achieve maximum gas savings.

`Example: `

```
function transfer(address payable recipient, uint256 amount) public {
    require(amount > 0, "Amount must be greater than zero");
    recipient.transfer(amount);
}
```

In the above example, we check to make sure that the amount parameter is greater than zero before making the transfer to the recipient address. If the amount is zero, the function will revert and the transfer will not be made.

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol#L42

```solidity
file:  contracts/tokens/LTap.sol

42    tapToken.transferFrom(msg.sender, address(this), amount);

50     tapToken.transfer(msg.sender, amount);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L797

```solidity
file:  contracts/Magnetar/modules/MagnetarMarketModule.sol

797    IERC20(_token).safeTransferFrom(_from, address(this), _amount);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol#L162

```solidity
file:  contracts/Swapper/BaseSwapper.sol

162    IERC20(token).safeTransferFrom(msg.sender, address(this), amount);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol#L140

```solidity
file:  contracts/Swapper/CurveSwapper.sol

140     IERC20(tokenOut).safeTransfer(to, amountOut);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L93

```solidity
file:  contracts/glp/GlpStrategy.sol

93     gmx.safeTransfer(address(gmxWethPool), amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L359

```solidity
file:  contracts/tOFT/BaseTOFT.sol

359    IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);

374    IERC20(erc20).safeTransfer(_toAddress, _amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol#L146

```solidity
file:  contracts/tOFT/mTapiocaOFT.sol

146    _safeTransferETH(msg.sender, _amount);

148     IERC20(erc20).safeTransfer(msg.sender, _amount);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L273

```solidity
file:   contracts/tOFT/modules/BaseTOFTLeverageModule.sol

273     _safeTransferETH(_toAddress, _amount);

275   IERC20(erc20).safeTransfer(_toAddress, _amount);

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L151

```solidity
file:  contracts/YieldBox.sol

151    IERC1155(asset.contractAddress).safeTransferFrom(from, address(asset.strategy), asset.tokenId, amount, "");

209    wrappedNative.safeTransfer(address(asset.strategy), amount);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L449

```solidity
file: contracts/governance/twTAP.sol

449   rewardToken.safeTransferFrom(msg.sender, address(this), _amount);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L514

```solidity
file: contracts/option-airdrop/AirdropBroker.sol

514    tapOFT.transfer(msg.sender, tapAmount);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L144

```solidity
file:  contracts/tokens/BaseTapOFT.sol

144    _transferFrom(address(this), to, amount);

147     _transferFrom(address(this), to, amount);

```

## [G-05] Public Functions Not Called By The Contract Should Be Declared External Instead

To optimize gas usage and improve the overall code quality, consider changing certain functions from public to external. In Solidity, external functions are more gas-efficient than public functions, as they utilize a different calling mechanism that saves gas costs. Making this change can lead to improved contract performance and more economical contract interactions.

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L111

```solidity
file:  contracts/tOFT/modules/BaseTOFTLeverageModule.sol

111   function multiHop(bytes memory _payload) public {

148       function leverageDown(
149        address module,
150        uint16 _srcChainId,
151        bytes memory _srcAddress,
152        uint64 _nonce,
153        bytes memory _payload
154    ) public {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L126-L132

```solidity
file:  contracts/tOFT/modules/BaseTOFTMarketModule.sol

126       function borrow(
128        address module,
129        uint16 _srcChainId,
130        bytes memory _srcAddress,
131        uint64 _nonce,
132        bytes memory _payload
133    ) public payable {



204     function remove(bytes memory _payload) public {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L122-L129

```solidity
file:   contracts/tOFT/modules/BaseTOFTStrategyModule.sol

122       function strategyDeposit(
123       address module,
124        uint16 _srcChainId,
125        bytes memory _srcAddress,
126        uint64 _nonce,
127        bytes memory _payload,
128        IERC20 _erc20
129    ) public {



188      function strategyWithdraw(
189        uint16 _srcChainId,
190        bytes memory _payload
191    ) public {


```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L168-L172

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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L143

```solidity
file:  contracts/usd0/BaseUSDO.sol

143   function decimals() public pure override returns (uint8) {

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L56

```solidity
file:  contracts/NativeTokenFactory.sol

56   function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {


73  function claimOwnership(uint256 tokenId) public {


109    function mint(uint256 tokenId, address to, uint256 amount) public onlyOwner(tokenId) {


116    function burn(uint256 tokenId, address from, uint256 amount) public allowed(from, tokenId) {


127    function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner(tokenId) {


138   function batchBurn(uint256 tokenId, address[] calldata froms, uint256[] calldata amounts) public {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L80

```solidity
file:  contracts/compound/CompoundStrategy.sol

80   function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L103

```solidity
file:  contracts/curve/TricryptoNativeStrategy.sol

103   function compoundAmount() public returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L114

```solidity
file:   contracts/markets/MarketERC20.sol

114    function totalSupply() public view virtual override returns (uint256) {}

135        function transfer(
136        address to,
137        uint256 amount
138    ) public virtual returns (bool)


159       function transferFrom(
160        address from,
161        address to,
162        uint256 amount
163    ) public virtual returns (bool)


201       function approveBorrow(
202        address spender,
203        uint256 amount
204    ) public returns (bool)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L155-L157

```solidity
file:  contracts/governance/twTAP.sol

155       function getParticipation(
156        uint _tokenId
157    ) public view returns (Participation memory participant)



397   function advanceWeek(uint256 _limit) public

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L68-L70

```solidity
file:   contracts/option-airdrop/aoTAP.sol

68        function tokenURI(
69        uint256 _tokenId
70    ) public view override returns (string memory)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L54-L56

```solidity
file:   contracts/options/oTAP.sol
54        function tokenURI(
55        uint256 _tokenId
56    ) public view override returns (string memory)

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol#L168

```solidity
file:   contracts/tokens/TapOFT.sol

168    function decimals() public pure override returns (uint8)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L209

```solidity
file:  contracts/Penrose.sol

209    function bigBangMarkets() public view returns (address[] memory markets)


214    function singularityMasterContractLength() public view returns (uint256)


219     function bigBangMasterContractLength() public view returns (uint256)



232       function withdrawAllMarketFees(
233        IMarket[] calldata markets_,
234        ISwapper[] calldata swappers_,
235        IPenrose.SwapData[] calldata swapData_
236    ) public notPaused

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol

```solidity
file:   contracts/markets/bigBang/BigBang.sol

242         function borrow(
243        address from,
244        address to,
245        uint256 amount
246    ) public notPaused solvent(from) returns (uint256 part, uint256 share)



263       function repay(
264        address from,
265        address to,
266        bool,
267        uint256 part
268    ) public notPaused allowedBorrow(from, part) returns (uint256 amount)




282       function addCollateral(
283        address from,
284        address to,
285        bool skim,
286        uint256 amount,
287        uint256 share
288    ) public allowedBorrow(from, share) notPaused



296       function removeCollateral(
297        address from,
298        address to,
299        uint256 share
300    ) public notPaused solvent(from) allowedBorrow(from, share)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLBorrow.sol#L21-L25

```solidity
file:   contracts/markets/singularity/SGLBorrow.sol

21       function borrow(
22        address from,
23        address to,
24        uint256 amount
25    ) public notPaused solvent(from) returns (uint256 part, uint256 share)



45       function repay(
46        address from,
47        address to,
48        bool skim,
49        uint256 part
50    ) public notPaused allowedBorrow(from, part) returns (uint256 amount)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCollateral.sol

```solidity
file:   contracts/markets/singularity/SGLCollateral.sol

21       function addCollateral(
22        address from,
23        address to,
24        bool skim,
25        uint256 amount,
26        uint256 share
27    ) public notPaused allowedBorrow(from, share)



35       function removeCollateral(
36        address from,
37        address to,
38        uint256 share
39    ) public notPaused solvent(from) allowedBorrow(from, share)

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol#L17

```solidity
file:  contracts/markets/singularity/SGLCommon.sol

17     function accrue() public {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L154

```solidity
file:  contracts/markets/singularity/SGLStorage.sol

154    function symbol() public view returns (string memory) {

191    function totalSupply() public view override returns (uint256) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L204-L209

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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L93

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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L104

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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L103

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

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L76-L79

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

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol#L41-L43

```solidity
file:  contracts/Multicall/Multicall3.sol

41       function multicall(
        Call[] calldata calls
    ) public payable returns (Result[] memory returnData) {

63       function multicallValue(
        CallValue[] calldata calls
    ) public payable returns (Result[] memory returnData) {
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L104

```solidity
file:  contracts/glp/GlpStrategy.sol

104    function harvestGmx(uint256 priceNum, uint256 priceDenom) public onlyOwner {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L84

```solidity
file:  contracts/lido/LidoEthStrategy.sol

84    function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L81

```solidity
file:  contracts/yearn/YearnStrategy.sol

81    function compoundAmount() public pure returns (uint256 result) {

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L42-L50

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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol

```solidity
file:   contracts/markets/Market.sol

256        function computeClosingFactor(
257        uint256 borrowPart,
258        uint256 collateralPartInAsset,
259        uint256 borrowPartDecimals,
260        uint256 collateralPartDecimals,
261        uint256 ratesPrecision
262    ) public view returns (uint256)



356       function computeLiquidatorReward(
357        address user,
358        uint256 _exchangeRate
359    ) public view returns (uint256)

```

## [G-06] Efficient Write Of `Address Storage` with Assembly To Save Gas

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.

Using assembly can potentially save gas in certain scenarios, especially when dealing with complex or repetitive storage operations. However, it is essential to keep in mind that assembly is low-level and can introduce security risks if not handled correctly.

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L264

```solidity
File:  contracts/Penrose.sol

264        bigBangEthMarket = _market;

284        conservator = _conservator;

292        usdoToken = IERC20(_usdoToken);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L189

```solidity
File:  contracts/markets/Market.sol


189            conservator = _conservator;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol#L92

```solidity
File:  contracts/markets/singularity/Singularity.sol


92      liquidationModule = SGLLiquidation(_liquidationModule);
93        collateralModule = SGLCollateral(_collateralModule);
94        borrowModule = SGLBorrow(_borrowModule);
95        leverageModule = SGLLeverage(_leverageModule);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L75

```solidity
File: contracts/usd0/BaseUSDO.sol


75        leverageModule = USDOLeverageModule(_leverageModule);
76        marketModule = USDOMarketModule(_marketModule);
77        optionsModule = USDOOptionsModule(_optionsModule);


108        conservator = _conservator;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol#L39

```solidity
File: contracts/Swapper/UniswapV2Swapper.sol

39   swapRouter = IUniswapV2Router02(_router);

40   factory = IUniswapV2Factory(_factory);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol

```solidity
File:  contracts/compound/CompoundStrategy.sol


55     wrappedNative = IERC20(_token);

56     cToken = ICToken(_cToken);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol

```solidity
File: contracts/convex/ConvexTricryptoStrategy.sol


93      wrappedNative = IERC20(_token);
94        swapper = ISwapper(_multiSwapper);
95        zap = IConvexZap(_zap);
96      booster = IConvexBooster(_booster);
97    lpGetter = ITricryptoLPGetter(_lpGetter);
98  rewardPool = IConvexRewardPool(_rewadPool);


173     swapper = ISwapper(_swapper);


182     lpGetter = ITricryptoLPGetter(_lpGetter);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L125

```solidity
File: contracts/governance/twTAP.sol

125        tapOFT = TapOFT(_tapOFT);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L105

```solidity
File:  contracts/option-airdrop/AirdropBroker.sol

105     paymentTokenBeneficiary = _paymentTokenBeneficiary;
106        tapOFT = TapOFT(_tapOFT);
107        aoTAP = AOTAP(_aoTAP);
108        PCNFT = IERC721(_pcnft);



360        paymentTokenBeneficiary = _paymentTokenBeneficiary;
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L89

```solidity
File:  contracts/options/TapiocaOptionBroker.sol

89      paymentTokenBeneficiary = _paymentTokenBeneficiary;
90        tOLP = TapiocaOptionLiquidityProvision(_tOLP);
91        tapOFT = TapOFT(_tapOFT);
92        oTAP = OTAP(_oTAP);



474        paymentTokenBeneficiary = _paymentTokenBeneficiary;
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L74

```solidity
File:  contracts/options/TapiocaOptionLiquidityProvision.sol


74        yieldBox = IYieldBox(_yieldBox);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L327

```solidity
File: contracts/tokens/BaseTapOFT.sol

327        twTap = TwTAP(_twTap);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L72

```solidity
File:  contracts/curve/TricryptoLPStrategy.sol


72      swapper = ISwapper(_multiSwapper);
73      lpGetter = ITricryptoLPGetter(_lpGetter);
74      lpGauge = ITricryptoLPGauge(_lpGauge);
75      minter = ICurveMinter(_minter);


145      swapper = ISwapper(_swapper);


153      lpGetter = ITricryptoLPGetter(_lpGetter);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L70

```solidity
File: contracts/curve/TricryptoNativeStrategy.sol

70      wrappedNative = IERC20(_token);
71        swapper = ISwapper(_multiSwapper);
72        lpGauge = ITricryptoLPGauge(_lpGauge);
73        minter = ICurveMinter(_minter);
74        lpGetter = ITricryptoLPGetter(_lpGetter);


136     swapper = ISwapper(_swapper);

144     lpGetter = ITricryptoLPGetter(_lpGetter);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol#L114

```solidity
File:  contracts/glp/GlpStrategy.sol

114      feeRecipient = recipient;
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L58

```solidity
File: contracts/lido/LidoEthStrategy.sol


58      wrappedNative = IERC20(_token);
59        stEth = IStEth(_stEth);
60        curveStEthPool = ICurveEthStEthPool(_curvePool);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L77

```solidity
File: contracts/stargate/StargateStrategy.sol


77        wrappedNative = IERC20(_token);
78        swapper = ISwapper(_swapper);
79        stgEthPool = _stgEthPool;
80        addLiquidityRouter = IRouterETH(_ethRouter);
81        lpStaking = ILPStaking(_lpStaking);


152     swapper = ISwapper(_swapper);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L56

```solidity
File:  contracts/yearn/YearnStrategy.sol

56      wrappedNative = IERC20(_token);
        vault = IYearnVault(_vault);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L129

```solidity
contracts/Balancer.sol

129     routerETH = IStargateRouter(_routerETH);
130      router = IStargateRouter(_router);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L74

```solidity
contracts/tOFT/BaseTOFT.sol


74      leverageModule = BaseTOFTLeverageModule(_leverageModule);
75        strategyModule = BaseTOFTStrategyModule(_strategyModule);
76        marketModule = BaseTOFTMarketModule(_marketModule);
77        optionsModule = BaseTOFTOptionsModule(_optionsModule);
```

## [G-07] Achieving Gas Savings Optimizing Value Assignment to Structures

To achieve better gas efficiency in smart contracts, it's essential to optimize the approach used for assigning values to data structures. By adopting a more gas-friendly method for struct assignment, you can potentially reduce gas costs during contract execution. Here are some techniques to realize gas savings by optimizing the model for value assignment to structures:

```solidity
struct MyStruct {
    uint256 a;
    uint256 b;
    uint256 c;
}

function assignValues(uint256 _a, uint256 _b, uint256 _c) public {
    MyStruct memory myStruct = MyStruct(_a, _b, _c);
    // Perform operations with myStruct
}


```

In this example, we have a simple data structure called MyStruct with three uint256 fields. The assignValues function takes three input parameters `_a, _b, and _c`, and efficiently assigns them to the corresponding fields in a new myStruct variable. This optimization is achieved using the struct constructor syntax, which instantiates a new instance of the MyStruct with the provided field values.

Using this approach can be more gas-efficient than assigning values to struct fields individually, as shown in the following alternative:

```solidity
function assignValues(uint256 _a, uint256 _b, uint256 _c) public {
    MyStruct memory myStruct;
    myStruct.a = _a;
    myStruct.b = _b;
    myStruct.c = _c;
    // Perform operations with myStruct
}

```

In this alternative, the values of `_a, _b, and _c` are assigned to the corresponding fields of myStruct one by one. However, this can result in higher gas consumption due to additional memory operations required for each field assignment.

By employing the struct constructor syntax to assign values to the struct fields, you can reduce the number of memory operations and achieve gas savings in struct initialization.

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol#L231

```solidity
File: contracts/usd0/modules/USDOOptionsModule.sol


231             ISendFrom.LzCallParams({
232                    refundAddress: payable(from),
233                    zroPaymentAddress: tapSendData.zroPaymentAddress,
234                    adapterParams: LzLib.buildDefaultAdapterParams(
235                        tapSendData.extraGas
236                    )
237                })
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol#L218-L238

```solidity
File:  contracts/usd0/modules/USDOMarketModule.sol


218                IUSDOBase.IMintData({
219                    mint: false,
220                    mintAmount: 0,
221                    collateralDepositData: ICommonData.IDepositData({
222                        deposit: false,
223                        amount: 0,
224                        extractFromSender: false
225                    })
226                }),
227                ICommonData.IDepositData({
228                    deposit: true,
229                    amount: lendParams.depositAmount,
230                    extractFromSender: true
231                }),
232                lendParams.lockData,
233               lendParams.participateData,
234                ICommonData.ICommonExternalContracts({
235                   magnetar: address(0),
236                    singularity: lendParams.market,
237                    bigBang: address(0)
238                })
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L236-L264

```solidity
File:  contracts/usd0/modules/USDOLeverageModule.sol


236              IUSDOBase.ILendOrRepayParams({
237                repay: true,
238               depositAmount: amountOut,
239               repayAmount: repayableAmount,
240                marketHelper: externalData.magnetar,
241                market: externalData.srcMarket,
242                removeCollateral: false,
243                removeCollateralShare: 0,
244                lockData: ITapiocaOptionLiquidityProvision.IOptionsLockData({
245                    lock: false,
246                    target: address(0),
247                    lockDuration: 0,
248                    amount: 0,
249                    fraction: 0
250                }),
251                participateData: ITapiocaOptionsBroker.IOptionsParticipateData({
252                    participate: false,
253                    target: address(0),
254                    tOLPTokenId: 0
255                })
256            }),
257            approvals,
258            ICommonData.IWithdrawParams({
259                withdraw: false,
260                withdrawLzFeeAmount: 0,
261                withdrawOnOtherChain: false,
262                withdrawLzChainId: 0,
263                withdrawAdapterParams: "0x"
264            }),
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol#L233-L249

```solidity
File:  contracts/usd0/modules/USDOLeverageModule.sol



233            ITapiocaOFT.IBorrowParams({
234                amount: amountOut,
235                borrowAmount: 0,
236                marketHelper: externalData.magnetar,
237                market: externalData.srcMarket
238            }),
239           ICommonData.IWithdrawParams({
240               withdraw: false,
241               withdrawLzFeeAmount: 0,
242               withdrawOnOtherChain: false,
243                withdrawLzChainId: 0,
244                withdrawAdapterParams: "0x"
245            }),
246            ICommonData.ISendOptions({
247                extraGasLimit: lzData.srcExtraGasLimit,
248                zroPaymentAddress: lzData.zroPaymentAddress
249           }),
```

## [G-8] Use Bitmap To Save Gas

Using a bitmap is a gas-efficient technique to represent multiple Boolean flags or small integers compactly within a single variable. It allows you to store multiple true/false (1/0) values or small integer values using a minimal amount of storage. By doing so, it can significantly reduce the gas cost of storage and operations compared to using separate variables for each flag or integer.

Here's an explanation of how using a bitmap can save gas:

Reduced Storage Costs: Using separate variables for each flag or small integer would consume more storage, which directly impacts the gas cost of your smart contract. In contrast, a bitmap allows you to store multiple values compactly in a single variable, reducing the overall storage cost and saving gas.

Reduced Read and Write Operations: Reading and writing data from storage are costly operations in terms of gas. When using a bitmap, you read or write multiple values at once, reducing the number of storage access operations and leading to significant gas savings.

Batch Operations: Bitmaps enable batch operations, which means you can perform multiple changes to the flags or integers with a single operation. This reduces the number of separate transactions needed to update multiple values, thus saving gas.

Bitwise Operations: Bitmaps use bitwise operations to manipulate and check individual bits within the variable. These bitwise operations are more efficient and consume less gas compared to conditional statements when working with individual flags.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol#L75-L76

```soldiity
file: /contracts/usd0/BaseUSDOStorage.sol

75       allowedMinter[chain][msg.sender] = true;

76        allowedBurner[chain][msg.sender] = true;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L77

```solidity
file: /contracts/tOFT/mTapiocaOFT.sol

77            connectedChains[_hostChainID] = true;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L147

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

147            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L456

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

456        userParticipation[tokenIDToAddress][3] = true;

```

## [G‑09] Using `> 0` costs more gas than `!= 0` when used on a uint in a require() statement

Gas-Save: `15 * 6 = 90`

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol#L12

```solidity
file: contracts/twAML.sol

12   require(denominator > 0);

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L203

```solidity
file:   contracts/option-airdrop/AirdropBroker.sol

203    require(cachedEpoch > 0, "adb: Airdrop not started");

392     require(_eligibleAmount > 0, "adb: Not eligible");

467    require(_eligibleAmount > 0, "adb: Not eligible");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L419

```solidity
file:  contracts/options/TapiocaOptionBroker.sol

419   require(singularities.length > 0, "tOB: No active singularities");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L177

```solidity
file:   contracts/options/TapiocaOptionLiquidityProvision.sol

177     require(_lockDuration > 0, "tOLP: lock duration must be > 0");

178     require(_amount > 0, "tOLP: amount must be > 0");

181     require(sglAssetID > 0, "tOLP: singularity not active");

281      require(assetID > 0, "tOLP: invalid asset ID");

301    require(sglAssetID > 0, "tOLP: not registered");

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L104

```solidity
file:  contracts/tokens/BaseTapOFT.sol

104    require(duration > 0, "TapOFT: Small duration");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L344

```solidity
file:  contracts/markets/Market.sol

344   require(rate > 0, "Market: invalid rate");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol#L397

```solidity
file:   contracts/markets/singularity/SGLLiquidation.sol

397    require(liquidatedCount > 0, "SGL: no users found");

```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L56

```solidity
file:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

56   require(amount > 0, "TOFT_0");

98    require(amount > 0, "TOFT_0");

```

## [G-10] Use Nested If statements instead of `&&` avoid multiple check combinations

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol#L370

```solidity
File:  contracts/usd0/BaseUSDO.sol

370          if (!success && !_forwardRevert) {
```

```diff

-370          if (!success && !_forwardRevert) {


+       if (!success) {
+            if (!_forwardRevert) {
+                   ...
+            }
+        }
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol#L1046

```solidity
File:  contracts/Magnetar/MagnetarV2.sol

1046         if (!success && !allowFailure) {
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L402

```solidity
File: contracts/Magnetar/modules/MagnetarMarketModule.sol

402        if (lendAmount == 0 && depositData.deposit) {
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File: contracts/aave/AaveStrategy.sol

171    if (daysPassed && balanceOfStkAave > 0) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L226

```solidity
File:  contracts/Balancer.sol

226        if (!isNative && _ercData.length == 0) revert PoolInfoRequired();
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol#L121

```solidity
File: contracts/TapiocaWrapper.sol

121    if (_revertOnFailure && !success) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol#L412

```solidity
File: contracts/tOFT/BaseTOFT.sol

412       if (!success && !_forwardRevert) {
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L27

```solidity
File: contracts/BoringMath.sol

27        if (roundUp && (result * div) / mul < value) {
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol#L35

```solidity
File: contracts/YieldBoxRebase.sol

35    if (roundUp && (share * totalAmount) / totalShares_ < amount) {

58    if (roundUp && (amount * totalShares_) / totalAmount < share) {
```

## [G-11] Make 3 event indexed when possible

events are used to emit information about state changes in a contract. When defining an event, it's important to consider the gas cost of emitting the event, as well as the efficiency of searching for events in the Ethereum blockchain.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events. When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.

```solidity
File: contracts/tokens/BaseTapOFT.sol

78    event Emitted(uint256 week, uint256 amount);

80    event Minted(address indexed _by, address indexed _to, uint256 _amount);

82    event Burned(address indexed _from, uint256 _amount);

84    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L78

```solidity
File:  contracts/Vesting.sol

60    event UserRegistered(address indexed user, uint256 amount);

62    event Claimed(address indexed user, uint256 amount);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L60

```solidity
File: contracts/governance/twTAP.sol

134  event Participate(
        address indexed participant,
        uint256 tapAmount,
        uint256 multiplier
    );

139 event AMLDivergence(
        uint256 cumulative,
        uint256 averageMagnitude,
        uint256 totalParticipants
    );


144     event ExitPosition(uint256 tokenId, uint256 amount);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L134

```solidity
File:  contracts/option-airdrop/AirdropBroker.sol

115    event Participate(uint256 indexed epoch, uint256 aoTAPTokenID);

123    event NewEpoch(uint256 indexed epoch, uint256 epochTAPValuation);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L115

```solidity
File:  contracts/options/TapiocaOptionBroker.sol

100  event Participate(
        uint256 indexed epoch,
        uint256 indexed sglAssetID,
        uint256 totalDeposited,
        LockPosition lock,
        uint256 discount
    );

107  event AMLDivergence(
        uint256 indexed epoch,
        uint256 cumulative,
        uint256 averageMagnitude,
        uint256 totalParticipants
    );

120   event NewEpoch(
        uint256 indexed epoch,
        uint256 extractedTAP,
        uint256 epochTAPValuation
    );

125   event ExitPosition(
        uint256 indexed epoch,
        uint256 indexed tokenId,
        uint256 amount
    );
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol#L100

```solidity
File: contracts/Penrose.sol

138    event ProtocolWithdrawal(IMarket[] markets, uint256 timestamp);

140    event RegisterSingularityMasterContract(
        address location,
        IPenrose.ContractType risk
       );

145   event RegisterBigBangMasterContract(
        address location,
        IPenrose.ContractType risk
    );

150    event RegisterSingularity(address location, address masterContract);

152    event RegisterBigBang(address location, address masterContract);

154    event FeeToUpdate(address newFeeTo);

156    event SwapperUpdate(address swapper, bool isRegistered);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol#L22

```solidity
File: contracts/markets/Market.sol

94    event LogExchangeRate(uint256 rate);

96    event LogBorrowCapUpdated(uint256 _oldVal, uint256 _newVal);

102   event Liquidated(
        address liquidator,
        address[] users,
        uint256 liquidatorReward,
        uint256 protocolReward,
        uint256 repayedAmount,
        uint256 collateralShareRemoved
    );

111    event LogBorrowingFee(uint256 _oldVal, uint256 _newVal);

113     event LiquidationMultiplierUpdated(uint256 oldVal, uint256 newVal);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol#L94

```solidity
File: contracts/markets/MarketERC20.sol

66  event ApprovalBorrow(
        address indexed owner,
        address indexed spender,
        uint256 value
    );
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L66

```solidity
File: contracts/Magnetar/MagnetarV2Storage.sol

331    event ApprovalForAll(address owner, address operator, bool approved);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol#L331

```solidity
File: contracts/Swapper/UniswapV3Swapper.sol

43    event PoolFee(uint256 _old, uint256 _new);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol#L43

```solidity
File: contracts/aave/AaveStrategy.sol

53  event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File: contracts/Balancer.sol


    event ConnectedChainUpdated(
        address indexed _srcOft,
        uint16 _dstChainId,
        address indexed _dstOft
    );
    /// @notice event emitted when a rebalance operation is performed
    /// @dev rebalancing means sending an amount of the underlying token to one of the connected chains
    event Rebalanced(
        address indexed _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bool _isNative
    );
    /// @notice event emitted when max rebalanceable amount is updated
    event RebalanceAmountUpdated(
        address _srcOft,
        uint16 _dstChainId,
        uint256 _amount,
        uint256 _totalAmount
    );
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol#L69-L89

```solidity
File:  contracts/NativeTokenFactory.sol

28    event TokenCreated(address indexed creator, string name, string symbol, uint8 decimals, uint256 tokenId);
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol#L28

```solidity
File: contracts/markets/bigBang/BigBang.sol

63    event LogAccrue(uint256 accruedAmount, uint64 rate);

65    event LogAddCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );

71  event LogRemoveCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );

77 event LogBorrow(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 feeAmount,
        uint256 part
    );

85    event LogRepay(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 part
    );

92    event MinDebtRateUpdated(uint256 oldVal, uint256 newVal);

94    event MaxDebtRateUpdated(uint256 oldVal, uint256 newVal);

96    event DebtRateAgainstEthUpdated(uint256 oldVal, uint256 newVal);
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol#L63

```solidity
File: contracts/markets/singularity/SGLStorage.sol


    event LogAccrue(
        uint256 accruedAmount,
        uint256 feeFraction,
        uint64 rate,
        uint256 utilization
    );
    /// @notice event emitted when collateral is added
    event LogAddCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );
    /// @notice event emitted when asset is added
    event LogAddAsset(
        address indexed from,
        address indexed to,
        uint256 share,
        uint256 fraction
    );
    /// @notice event emitted when collateral is removed
    event LogRemoveCollateral(
        address indexed from,
        address indexed to,
        uint256 share
    );
    /// @notice event emitted when asset is removed
    event LogRemoveAsset(
        address indexed from,
        address indexed to,
        uint256 share,
        uint256 fraction
    );
    /// @notice event emitted when asset is borrowed
    event LogBorrow(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 feeAmount,
        uint256 part
    );
    /// @notice event emitted when asset is repayed
    event LogRepay(
        address indexed from,
        address indexed to,
        uint256 amount,
        uint256 part
    );
    /// @notice event emitted when fees are extracted
    event LogWithdrawFees(address indexed feeTo, uint256 feesEarnedFraction);
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

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol#L70-L138

## [G-12] Use hardcode address instead `address(this)`

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

References:

[Foundry](https://book.getfoundry.sh/reference/forge-std/compute-create-address)

[twitter](https://twitter.com/transmissions11/status/1518507047943245824)

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
file:  contracts/Vesting.sol

157   uint256 availableToken = _token.balanceOf(address(this));

```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L157

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

## [G-13] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
 f(x) || g(y)
 f(x) && g(y)
```

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
file: /contracts/TapiocaWrapper.sol


121        if (_revertOnFailure && !success) {

144        if (_call[i].revertOnFailure && !success) {

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L121

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

## [G-14] Pre-increments and pre-decrements are cheaper than post-increments and post-decrements

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

288        epoch++;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L288

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

325            pool.totalParticipants--;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L325

```solidity
file: /contracts/governance/twTAP.sol

278            pool.totalParticipants++; // Save participation

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L278

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

423        epoch++;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L423

```solidity
file: /contracts/markets/MarketERC20.sol

248        current = _nonces[owner]++;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L248

```solidity
file: /contracts/governance/twTAP.sol

537            pool.totalParticipants--;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L537

```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

387                liquidatedCount++;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L387

## [G-15] `abi.encode()` is less efficient than `abi.encodePacked()`

To enhance gas efficiency in your smart contracts, consider utilizing abi.encodePacked() when appropriate. This function allows for tight packing of data without additional information, potentially reducing gas costs in certain scenarios.

When to Use abi.encodePacked():

Raw Data Packing: If you need to pack data without function selectors or additional ABI information, abi.encodePacked() can be more gas-efficient than abi.encode(). This is particularly useful when you solely require compact data representation.

Gas Optimization: When encoding multiple arguments for non-function call purposes, abi.encodePacked() can save gas compared to abi.encode(). It omits the function selector overhead, resulting in potential gas savings.

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
file:  master/contracts/YieldBoxPermit.sol

53    bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));

82   bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));

```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L53

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

## [G-16] Use constants instead of `type(uintx).max`

it's generally more gas-efficient to use constants instead of type(uintX).max when you need to set the maximum value of an unsigned integer type.

The reason for this is that the type(uintX).max expression involves a computation at runtime, whereas a constant is evaluated at compile-time. This means that using type(uintX).max can result in additional gas costs for each transaction that involves the expression.

By using a constant instead of type(uintX).max, you can avoid these additional gas costs and make your code more efficient.

Here's an example of how you can use a constant instead of `type(uintX).max`:

```
contract MyContract {
    uint120 constant MAX_VALUE = 2**120 - 1;

    function doSomething(uint120 value) public {
        require(value <= MAX_VALUE, "Value exceeds maximum");

        // Do something
    }
}
```

In the above example, we have a contract with a constant MAX_VALUE that represents the maximum value of a uint120. When the doSomething function is called with a value parameter, it checks whether the value is less than or equal to MAX_VALUE using the <= operator.

By using a constant instead of type(uint120).max, we can make our code more efficient and reduce the gas cost of our contract.

It's important to note that using constants can make your code more readable and maintainable, since the value is defined in one place and can be easily updated if necessary. However, constants should be used with caution and only when their value is known at compile-time.

```solidity
File:  contracts/curve/TricryptoNativeStrategy.sol


78      IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);
        IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

135    rewardToken.approve(_swapper, type(uint256).max);

145     wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L78

```solidity
File:  contracts/lido/LidoEthStrategy.sol

62   IERC20(_stEth).approve(_curvePool, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L62

```solidity
File:   contracts/stargate/StargateStrategy.sol

89      stgNative.approve(_lpStaking, type(uint256).max);
        stgNative.approve(address(router), type(uint256).max);

94      stgTokenReward.approve(_swapper, type(uint256).max);

153     stgTokenReward.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L89

```solidity
File:  contracts/balancer/BalancerStrategy.sol

77        wrappedNative.approve(_vault, type(uint256).max);

78        IERC20(address(pool)).approve(_vault, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L77

```solidity
File: contracts/compound/CompoundStrategy.sol

58  wrappedNative.approve(_cToken, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L58

```solidity
File: contracts/yearn/YearnStrategy.sol

59   wrappedNative.approve(address(vault), type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L59

```solidity
File: contracts/BoringMath.sol

6  require(a <= type(uint128).max, "BoringMath: uint128 Overflow");

11 require(a <= type(uint64).max, "BoringMath: uint64 Overflow");

16 require(a <= type(uint32).max, "BoringMath: uint32 Overflow");
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L6

```solidity
File: contracts/convex/ConvexTricryptoStrategy.sol


105     wrappedNative.approve(_lpGetter, type(uint256).max);
106        lpToken.approve(_lpGetter, type(uint256).max);
107        lpToken.approve(_booster, type(uint256).max);
108        rewardToken.approve(_multiSwapper, type(uint256).max);

174    rewardToken.approve(_swapper, type(uint256).max);

183    wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L105

```solidity
File:  contracts/curve/TricryptoLPStrategy.sol


79      lpToken.approve(_lpGauge, type(uint256).max);
        lpToken.approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

144     rewardToken.approve(_swapper, type(uint256).max);

154     wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L79

```solidity
File:  contracts/governance/twTAP.sol

313  require(expiry < type(uint56).max, "twTAP: too long");
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L313

```solidity
File: contracts/markets/MarketERC20.sol

172  if (spenderAllowance != type(uint256).max) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L172

```solidity
File: contracts/aave/AaveStrategy.sol

75    wrappedNative.approve(_lendingPool, type(uint256).max);

76    rewardToken.approve(_multiSwapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L75

## [G-17] Use assembly for loops

In the following instances, assembly is used for more gas efficient loops. The only memory slots that are manually used in the loops are scratch space (0x00-0x20), the free memory pointer (0x40), and the zero slot (0x60). This allows us to avoid using the free memory pointer to allocate new memory, which may result in memory expansion costs.

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

## [G-18] Use assembly to check for `address(0)`

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
file: /contracts/markets/singularity/Singularity.sol

581        if (address(_liquidationQueue) != address(0)) {

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L581

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
file: /contracts/usd0/BaseUSDO.sol

106         require(_conservator != address(0), "USDO: address not valid");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L106

## [G-19] Bytes `constants` are more efficient than String constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

```solidity
file: /contracts/glp/GlpStrategy.sol

26    string public constant override name = "sGLP";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L26

## [G-20] Use `selfbalance()` instead of `address(this).balance`

It's recommended to use the selfbalance() function instead of address(this).balance. The selfbalance() function is a built-in Solidity function that returns the balance of the current contract in Wei and is considered more gas-efficient and secure.

```solidity
File:  contracts/usd0/modules/USDOOptionsModule.sol

127   ISendFrom(address(this)).sendFrom{value: address(this).balance}(
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol

```solidity
File:  contracts/Magnetar/modules/MagnetarMarketModule.sol

286    address(this).balance

751     uint256 gas = msg.value > 0 ? msg.value : address(this).balance;
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol

```solidity
File:  contracts/TapiocaDeployer/TapiocaDeployer.sol

29    address(this).balance >= amount,
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol

```solidity
File:  contracts/compound/CompoundStrategy.sol

105    INative(address(wrappedNative)).deposit{value: address(this).balance}();

107    result = address(this).balance;

151    value: address(this).balance
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol

```solidity
File:  contracts/tokens/BaseTapOFT.sol

312   this.sendFrom{value: address(this).balance}(
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol

```solidity
File:  contracts/usd0/modules/USDOLeverageModule.sol

227   value: address(this).balance
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol

```solidity
File:  contracts/stargate/StargateStrategy.sol

206   result = address(this).balance;
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol

```solidity
File:  contracts/tOFT/modules/BaseTOFTOptionsModule.sol

142    ISendFrom(address(this)).sendFrom{value: address(this).balance}(
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol

```solidity
File:  contracts/tOFT/modules/BaseTOFTStrategyModule.sol

225   address(this).balance
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol

```solidity
File:  contracts/Balancer.sol

286    if (address(this).balance < _amount) revert ExceedsBalance();
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol

```solidity
File:  BaseTOFTLeverageModule.sol

230  value: address(this).balance
```

https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol

## [G-21] Use `of emit inside` a loop

When emitting an event inside a loop, the Solidity compiler performs a LOG operation N times, where N is the loop length. This can lead to unnecessary gas costs, especially if the loop iterates over a large number of elements. To achieve significant gas savings, consider refactoring the code to emit the event only once at the end of the loop. The gas savings will be multiplied by the average loop length.

```solidity
File: contracts/YieldBox.sol

350  emit TransferSingle(msg.sender, from, to, assetId, share_);
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol

## [G-22] Use assembly to hash instead of solidity

https://medium.com/@bloqarl/solidity-gas-optimization-tips-with-assembly-you-havent-heard-yet-1381c77ff078

```
function solidityHash(uint256 a, uint256 b) public view {
    //unoptimized
    keccak256(abi.encodePacked(a, b));
}
```

Gas: 313

```
function assemblyHash(uint256 a, uint256 b) public view {
    //optimized
    assembly {
        mstore(0x00, a)
        mstore(0x20, b)
        let hashedVal := keccak256(0x00, 0x40)
    }
}
```

Gas: 231

```solidity
File:  contracts/YieldBoxPermit.sol

53     bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));

82   bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L53

```solidity
File:  contracts/option-airdrop/AirdropBroker.sol


418       bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L418

## [G-23] Use ERC721A instead ERC721

However, based on the description you provided, ERC721A appears to be a custom NFT standard proposed by the Azuki team to address gas efficiency concerns while minting multiple NFTs simultaneously. The standard aims to improve the gas efficiency for minting operations, making it more cost-effective, especially in times of high Ethereum gas fees.

Key features and benefits of ERC721A, as described in your statement, include:

Gas Efficiency: ERC721A is designed to optimize gas costs for minting multiple NFTs simultaneously. By reducing the gas consumption during minting, users can mint NFTs more affordably, even during periods of high gas fees.

Simultaneous Minting: Unlike the traditional ERC721 standard, which typically requires individual transactions for each NFT minting, ERC721A allows developers to mint multiple NFTs in a single transaction. This batch minting capability further contributes to gas savings.

Azuki's NFT Collection: The ERC721A standard was used by the Azuki team for developing their NFT collection, demonstrating its real-world applicability and potential benefits.

It's important to note that while custom standards like ERC721A may offer specific benefits tailored to certain use cases, they might not be widely recognized or adopted by the broader Ethereum ecosystem. Developers and users should carefully assess the implementation and consider factors like security, community support, and compatibility with existing tools and platforms before adopting custom standards.

[Reffrence](https://nextrope.com/erc721-vs-erc721a-2/)

```solidity
File:  contracts/option-airdrop/aoTAP.sol

6  import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol#L6

```solidity
File:  contracts/Magnetar/modules/MagnetarMarketModule.sol

10  import "@openzeppelin/contracts/token/ERC721/IERC721.sol";
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol#L10

```solidity
File: contracts/YieldBox.sol

28   import "@boringcrypto/boring-solidity/contracts/interfaces/IERC721.sol";
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol#L28

```solidity
File:  contracts/options/oTAP.sol

5 import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol#L5

```solidity
File:  contracts/options/TapiocaOptionLiquidityProvision.sol

8  import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol#L8

## [G-24] With assembly, `.call` (bool success)  transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

152        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.depositToYieldbox.selector,
                assetId,
                amount,
                share,
                _erc20,
                address(this),
                onBehalfOf
161            )

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L152-L161

```solidity
file: /contracts/curve/TricryptoLPStrategy.sol

105        (bool success, bytes memory response) = address(lpGauge).staticcall(
106            abi.encodeWithSignature("claimable_tokens(address)", address(this))
107        );

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoLPStrategy.sol#L105-L107

```solidity
file: /contracts/markets/singularity/Singularity.sol

188            (bool success, bytes memory result) = address(this).delegatecall(

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L188

```solidity
file: /contracts/tOFT/modules/BaseTOFTOptionsModule.sol

189        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.exerciseInternal.selector,
                optionsData.from,
                optionsData.oTAPTokenID,
                optionsData.paymentToken,
                optionsData.tapAmount,
                optionsData.target,
                tapSendData,
                approvals
            )
200        );

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L189-L200

```solidity
file: /contracts/tOFT/BaseTOFT.sol

411        (success, returnData) = module.delegatecall(_data);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L411

```solidity
file: /contracts/convex/ConvexTricryptoStrategy.sol

356        (bool successEmtptyApproval, ) = token.call(

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol#L356

```solidity
file: /contracts/Magnetar/MagnetarV2.sol

1045        (bool success, bytes memory returnData) = target.call(actionCalldata);

1071        (success, returnData) = module.delegatecall(_data);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L1045

## [G-25] `Require() or Revert()` statement should be used sorted from cheapest to most expensive

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first,the function is able to revert before wasting a Gcoldsload (2100 gas\*) in a function that may ultimately revert in the unhappy case

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

187        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");

190        require(tapAmount >= 1e18, "tOB: Too low");

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L187

## [G-26] Use assembly to emit events

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs. Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

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
file: /contracts/balancer/BalancerStrategy.sol

110        emit DepositThreshold(depositThreshold, amount);

```

110https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol#L110

```solidity
file: /contracts/NativeTokenFactory.sol

99        emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);

```

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol#L99

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
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

328        emit UnregisterSingularity(address(singularity), sglAssetID);

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L328

```solidity
file: /contracts/compound/CompoundStrategy.sol

161        emit AmountWithdrawn(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol#L161

```solidity
file: /contracts/Swapper/UniswapV3Swapper.sol

62        emit PoolFee(poolFee, _newFee);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L62
