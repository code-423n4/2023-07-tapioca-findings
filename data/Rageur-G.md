## GAS-1: Caching global variables is more expensive than using the actual variable

### Description

 It’s cheaper to use global variable as compared to caching.

### Affected file

* TapOFT.sol (Line: 132, 177)
* TapiocaOptionBroker.sol (Line: 422)
* Vesting.sol (Line: 161)
* aoTAP.sol (Line: 141)
* oTAP.sol (Line: 128)
* twTAP.sol (Line: 127)

## GAS-2: Change ```public``` state variable visibility to ```private```

### Description

If it is preferred to change the visibility of the ```owner``` state variable to private, this will save significant gas.

### Affected file

* NativeTokenFactory.sol (Line: 25, 26)

## GAS-3: Do not calculate constants

### Description

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

### Affected file

* ARBTriCryptoOracle.sol (Line: 41)
* AirdropBroker.sol (Line: 69, 85, 93)
* BaseUSDOStorage.sol (Line: 38)
* MarketERC20.sol (Line: 29, 33)
* TapOFT.sol (Line: 41)
* TapiocaOptionBroker.sol (Line: 76, 77)
* YieldBoxPermit.sol (Line: 27, 31)
* twTAP.sol (Line: 82, 83, 95)

## GAS-4: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* Balancer.sol (Line: 111, 117)
* BaseSwapper.sol (Line: 18)
* BaseTOFT.sol (Line: 45)
* Market.sol (Line: 115, 120, 130)
* MarketERC20.sol (Line: 94, 99)
* NativeTokenFactory.sol (Line: 38, 45)
* Penrose.sol (Line: 173, 181, 189)
* TapOFT.sol (Line: 88)
* TapiocaOptionLiquidityProvision.sol (Line: 101)
* USDO.sol (Line: 25)
* aoTAP.sol (Line: 45)
* oTAP.sol (Line: 39)

## GAS-5: Should use arguments instead of state variable

### Description

Using function's parameter cost less gas then a state variable.

### Affected file

* GlpStrategy.sol (Line: 77, 78, 79, 80)
* TapOFT.sol (Line: 120)

## GAS-6: Unbounded gas consumption risk due to external call recipients

### Description

In the context of Solidity, external function calls without a specified gas limit present a significant risk. The callee contract has the potential to consume all the gas allocated to the transaction, causing an undesired revert and disrupt the function's execution. To mitigate this, it's recommended to explicitly set a gas limit when performing external calls using ```addr.call{gas: }```. This limits the gas forwarded to the callee, preventing potential pitfalls and offering better control over transaction execution.

### Affected file

* BaseSwapper.sol (Line: 99)
* BaseTOFT.sol (Line: 411)
* BaseTOFTLeverageModule.sol (Line: 184)
* BaseTOFTMarketModule.sol (Line: 160)
* BaseTOFTOptionsModule.sol (Line: 189)
* BaseTOFTStrategyModule.sol (Line: 152)
* BaseUSDO.sol (Line: 369)
* BigBang.sol (Line: 216)
* ConvexTricryptoStrategy.sol (Line: 356, 369)
* MagnetarV2.sol (Line: 1045, 1071)
* Multicall3.sol (Line: 51, 79)
* Penrose.sol (Line: 444)
* Singularity.sol (Line: 188, 625, 638)
* TapiocaWrapper.sol (Line: 120, 141)
* TricryptoLPStrategy.sol (Line: 105)
* USDOLeverageModule.sol (Line: 169)
* USDOMarketModule.sol (Line: 168)
* USDOOptionsModule.sol (Line: 174)

## GAS-7: Use ```assembly``` to write address storage values

### Affected file

* ARBTriCryptoOracle.sol (Line: 53, 54, 55, 56, 57, 58, 59)
* AaveStrategy.sol (Line: 66, 67, 69, 70, 71, 72, 73, 124, 131)
* AirdropBroker.sol (Line: 105, 106, 107, 108, 287, 292, 312, 313, 321, 360)
* Balancer.sol (Line: 129, 130)
* BalancerStrategy.sol (Line: 66, 67, 69, 70, 73, 75, 111, 297)
* BaseTOFT.sol (Line: 74, 75, 76, 77)
* BaseTOFTStorage.sol (Line: 61, 62, 63, 64)
* BaseTapOFT.sol (Line: 327)
* BaseUSDO.sol (Line: 75, 76, 77)
* BaseUSDOStorage.sol (Line: 77, 78, 80)
* BigBang.sol (Line: 158, 159, 160, 161, 460, 478, 484, 492, 513, 538)
* CompoundStrategy.sol (Line: 55, 56, 91)
* ConvexTricryptoStrategy.sol (Line: 93, 94, 96, 97, 98, 99, 100, 102, 103, 165, 173, 182)
* CurveSwapper.sol (Line: 40, 41)
* GLPOracle.sol (Line: 11)
* GlpStrategy.sol (Line: 59, 63, 65, 67, 68, 69, 71, 74, 77, 78, 79, 80, 82, 114, 168, 172)
* LTap.sol (Line: 36, 37, 38, 55)
* LidoEthStrategy.sol (Line: 58, 59, 60, 95)
* MagnetarV2.sol (Line: 46)
* Market.sol (Line: 133, 145, 153, 174, 178, 183, 189, 194, 199, 207, 216, 225, 230, 238, 249, 316, 345, 412)
* Penrose.sol (Line: 92, 93, 104, 113, 122, 131, 257, 264, 275, 284, 292, 302, 456)
* SGOracle.sol (Line: 36, 37, 38, 39)
* Seer.sol (Line: 42, 43, 44)
* Singularity.sol (Line: 92, 93, 94, 95)
* StargateStrategy.sol (Line: 77, 78, 79, 81, 82, 83, 85, 86, 88, 92, 144, 152)
* TapOFT.sol (Line: 119, 132, 147, 155, 163)
* TapiocaOptionBroker.sol (Line: 89, 90, 91, 92, 93, 422, 450, 451, 474)
* TapiocaOptionLiquidityProvision.sol (Line: 74, 103, 131, 194, 304)
* TricryptoLPStrategy.sol (Line: 71, 72, 73, 74, 75, 76, 77, 136, 145, 153)
* TricryptoNativeStrategy.sol (Line: 70, 71, 73, 74, 75, 76, 127, 136, 144)
* UniswapV2Swapper.sol (Line: 39, 40, 41)
* UniswapV3Swapper.sol (Line: 54, 55, 56, 63)
* Vesting.sol (Line: 70, 71, 156, 160, 161)
* YearnStrategy.sol (Line: 56, 57, 92)
* YieldBox.sol (Line: 92, 93)
* YieldBoxPermit.sol (Line: 118)
* aoTAP.sol (Line: 118, 141)
* oTAP.sol (Line: 105, 128)
* twTAP.sol (Line: 125, 127, 128, 193, 194, 300, 409, 410, 420, 437, 556)

## GAS-8: Use assembly to calculate hashes

### Description

Using assembly to calculate hashes can save 80 gas per instance

### Affected file

* AirdropBroker.sol (Line: 418)
* MarketERC20.sol (Line: 263)
* TapiocaOptionLiquidityProvision.sol (Line: 366)
* TapiocaWrapper.sol (Line: 195, 197, 213, 215)
* YieldBoxPermit.sol (Line: 53, 82)

## GAS-9: Use assembly to check for address(0)

### Description

Saves 6 gas per instance if using assembly to check for address(0).

### Remediation

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

### Affected file

* AirdropBroker.sol (Line: 368)
* Balancer.sol (Line: 112, 127, 128, 142)
* BaseSwapper.sol (Line: 19, 112, 112)
* BaseTOFT.sol (Line: 371, 396)
* BaseTOFTLeverageModule.sol (Line: 272)
* BaseUSDO.sol (Line: 106, 354)
* BigBang.sol (Line: 133, 133, 133)
* GlpStrategy.sol (Line: 62, 66)
* MagnetarMarketModule.sol (Line: 305, 308, 464, 487, 490)
* MagnetarV2.sol (Line: 1057)
* Market.sol (Line: 177, 187)
* MarketERC20.sol (Line: 144, 179)
* NativeTokenFactory.sol (Line: 59)
* Penrose.sol (Line: 242, 243, 282)
* SGLLiquidation.sol (Line: 40)
* Singularity.sol (Line: 100, 100, 100, 581, 586, 591, 611)
* TapOFT.sol (Line: 118, 161)
* TapiocaDeployer.sol (Line: 43)
* TapiocaOFT.sol (Line: 74)
* TapiocaOptionBroker.sol (Line: 482)
* Vesting.sol (Line: 132)
* YieldBox.sol (Line: 317, 340, 360, 382)
* aoTAP.sol (Line: 140)
* mTapiocaOFT.sol (Line: 94)
* oTAP.sol (Line: 127)

## GAS-10: Use assembly to emit events

### Description

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs.
Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

### Affected file

* AaveStrategy.sol (Line: 123, 130, 204, 243, 246, 274)
* AirdropBroker.sol (Line: 217, 269, 293, 315, 352)
* Balancer.sol (Line: 211, 243, 256)
* BalancerStrategy.sol (Line: 110, 142, 144, 215)
* BaseTOFTLeverageModule.sol (Line: 76, 107, 202)
* BaseTOFTMarketModule.sol (Line: 75, 123, 177)
* BaseTOFTOptionsModule.sol (Line: 65, 110, 150, 214)
* BaseTOFTStrategyModule.sol (Line: 79, 119, 170, 227, 234)
* BaseTapOFT.sol (Line: 117, 143, 146, 190, 241, 243, 283, 320, 322)
* BaseUSDO.sol (Line: 89, 98, 107, 117, 127, 136)
* BigBang.sol (Line: 477, 483, 488, 500, 540, 557, 587, 588, 629, 716, 738, 766)
* CompoundStrategy.sol (Line: 90, 129, 132, 161)
* ConvexTricryptoStrategy.sol (Line: 164, 171, 180, 214, 304, 307, 349, 351)
* LidoEthStrategy.sol (Line: 94, 134, 137, 166)
* Market.sol (Line: 144, 152, 173, 179, 184, 188, 229, 248, 346)
* MarketERC20.sol (Line: 150, 185, 292, 297)
* NativeTokenFactory.sol (Line: 62, 80, 99, 100, 101)
* Penrose.sol (Line: 247, 258, 265, 274, 283, 310, 332, 354, 375, 387, 408, 420, 457, 466, 539)
* SGLCommon.sol (Line: 151, 153, 215, 218, 237, 242)
* SGLLendingCommon.sol (Line: 37, 48, 71, 97)
* SGLLiquidation.sol (Line: 134, 139, 184, 196, 286, 321, 322, 360)
* Singularity.sol (Line: 466, 468, 499, 511, 526, 538, 546, 558, 567, 587, 592)
* StargateStrategy.sol (Line: 143, 150, 228, 237, 268)
* TapOFT.sol (Line: 143, 154, 162, 212, 228, 235)
* TapiocaOptionBroker.sol (Line: 264, 285, 328, 344, 402, 431, 453, 466)
* TapiocaOptionLiquidityProvision.sol (Line: 104, 200, 249, 269, 292, 328)
* TapiocaWrapper.sol (Line: 101, 177)
* TricryptoLPStrategy.sol (Line: 135, 142, 151, 193, 222, 225, 253)
* TricryptoNativeStrategy.sol (Line: 126, 133, 142, 176, 209, 212, 244)
* USDO.sol (Line: 112, 121)
* USDOLeverageModule.sol (Line: 59, 90, 187)
* USDOMarketModule.sol (Line: 57, 96, 188)
* USDOOptionsModule.sol (Line: 50, 95, 135, 199)
* UniswapV3Swapper.sol (Line: 62)
* Vesting.sol (Line: 120, 145)
* YearnStrategy.sol (Line: 91, 125, 128, 149)
* YieldBox.sol (Line: 157, 185, 212, 258, 293, 328, 350, 373, 397)
* aoTAP.sol (Line: 123, 135)
* mTapiocaOFT.sol (Line: 120, 135, 151)
* oTAP.sol (Line: 110, 122)
* twTAP.sol (Line: 301, 346, 557, 567)

## GAS-11: Use assembly when getting a contract’s balance of ETH

### Description

You can use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas. Additionally, you can use balance(address) instead of address.balance() when getting an external contract’s balance of ETH.

### Affected file

* Balancer.sol (Line: 286)
* BaseTOFTLeverageModule.sol (Line: 230)
* BaseTOFTOptionsModule.sol (Line: 142)
* BaseTOFTStrategyModule.sol (Line: 225)
* BaseTapOFT.sol (Line: 312)
* CompoundStrategy.sol (Line: 105, 107, 151)
* MagnetarMarketModule.sol (Line: 286, 751)
* StargateStrategy.sol (Line: 206)
* USDOLeverageModule.sol (Line: 227)
* USDOOptionsModule.sol (Line: 127)

## GAS-12: Use calldata instead of memory for function parameters

### Description

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

### Affected file

* ARBTriCryptoOracle.sol (Line: 44, 44)
* AirdropBroker.sol (Line: 487)
* Balancer.sol (Line: 172, 219, 297)
* BaseTOFT.sol (Line: 50, 50, 156, 256, 403, 417, 417, 417, 442, 442)
* BaseTOFTLeverageModule.sol (Line: 25, 25, 111, 148, 148, 205, 205, 205, 284)
* BaseTOFTMarketModule.sol (Line: 24, 24, 126, 126, 180, 180, 180, 204, 260)
* BaseTOFTOptionsModule.sol (Line: 19, 19, 118, 153, 153, 221, 221, 259)
* BaseTOFTStorage.sol (Line: 45, 45, 67)
* BaseTOFTStrategyModule.sol (Line: 20, 20, 89, 122, 122, 188)
* BaseTapOFT.sol (Line: 44, 44, 52, 52, 125, 163, 198, 291, 332)
* BaseUSDO.sol (Line: 215, 361, 375, 375, 375, 399, 399)
* BaseUSDOStorage.sol (Line: 87)
* ConvexTricryptoStrategy.sol (Line: 189, 217)
* CurveSwapper.sol (Line: 94)
* MagnetarMarketModule.sol (Line: 24, 677, 734)
* MagnetarV2.sol (Line: 732, 965, 996, 1064, 1077)
* Market.sol (Line: 372)
* MarketERC20.sol (Line: 109)
* Multicall3.sol (Line: 93)
* Penrose.sol (Line: 424, 472, 485, 542)
* SGLLiquidation.sol (Line: 97)
* SGOracle.sol (Line: 30, 30)
* Seer.sol (Line: 12, 12, 12, 12, 12, 12, 12, 12)
* Singularity.sol (Line: 618, 631)
* TapiocaDeployer.sol (Line: 22, 22)
* TapiocaOFT.sol (Line: 33, 33)
* TapiocaOptionBroker.sol (Line: 508)
* USDOLeverageModule.sol (Line: 93, 133, 133, 190, 190, 190, 254)
* USDOMarketModule.sol (Line: 104, 134, 134, 191, 191, 191, 243)
* USDOOptionsModule.sol (Line: 103, 138, 138, 206, 206, 244)
* YieldBoxPermit.sol (Line: 40)
* mTapiocaOFT.sol (Line: 49, 49)
* twTAP.sol (Line: 361, 499)

## GAS-13: Use constants instead of type(uintx).max

### Description

It uses more gas in the distribution process and also for each transaction than constant usage.

### Affected file

* AaveStrategy.sol (Line: 75, 76, 150)
* BalancerStrategy.sol (Line: 77, 78)
* BoringMath.sol (Line: 6, 11, 16)
* CompoundStrategy.sol (Line: 58)
* ConvexTricryptoStrategy.sol (Line: 105, 106, 107, 108, 174, 183)
* LidoEthStrategy.sol (Line: 62)
* MarketERC20.sol (Line: 172)
* StargateStrategy.sol (Line: 89, 90, 94, 153)
* TricryptoLPStrategy.sol (Line: 79, 80, 81, 82, 144, 154)
* TricryptoNativeStrategy.sol (Line: 78, 79, 80, 81, 135, 145)
* YearnStrategy.sol (Line: 59)
* twTAP.sol (Line: 313)

## GAS-14: Use elementary types or a user-defined type instead of a struct that has only one member

### Affected file

* MagnetarV2Storage.sol (Line: 116)

## GAS-15: Use hardcoded address instead address(this)

### Description

Instead of using ```address(this)```, it is more gas-efficient to pre-calculate and use the hardcoded ```address```

### Affected file

* AaveStrategy.sol (Line: 100, 139, 142, 151, 156, 159, 164, 167, 179, 194, 197, 201, 212, 217, 226, 227, 235, 240, 257, 266)
* AirdropBroker.sol (Line: 379, 511)
* Balancer.sol (Line: 286, 305)
* BalancerStrategy.sol (Line: 131, 139, 150, 173, 174, 181, 182, 198, 220, 241, 246, 251, 257, 260, 272, 292, 293)
* BaseSwapper.sol (Line: 155, 156, 162)
* BaseTOFT.sol (Line: 359, 460)
* BaseTOFTLeverageModule.sol (Line: 176, 179, 182, 197, 212, 221, 230, 232)
* BaseTOFTMarketModule.sol (Line: 152, 155, 158, 172)
* BaseTOFTOptionsModule.sol (Line: 142, 177, 182, 187, 206, 242)
* BaseTOFTStrategyModule.sol (Line: 143, 146, 149, 165, 206, 209, 211, 225)
* BaseTapOFT.sol (Line: 134, 144, 147, 232, 235, 312, 313)
* BigBang.sol (Line: 216, 445, 454, 597, 608, 618, 619, 648, 649, 704, 717, 732, 735, 758, 762)
* CompoundStrategy.sol (Line: 103, 105, 107, 114, 117, 124, 143, 151)
* ConvexTricryptoStrategy.sol (Line: 131, 151, 208, 212, 257, 275, 292, 294, 301, 330, 333, 346)
* CurveSwapper.sol (Line: 134, 158, 163)
* GlpStrategy.sol (Line: 120, 131, 141, 167, 190, 191, 192, 205, 209, 254, 256, 263, 281, 288, 302, 305, 317, 325)
* LTap.sol (Line: 42)
* LidoEthStrategy.sol (Line: 107, 119, 123, 129, 148, 161)
* MagnetarMarketModule.sol (Line: 163, 164, 174, 186, 237, 238, 248, 261, 286, 345, 346, 358, 392, 425, 431, 432, 438, 478, 480, 534, 544, 582, 599, 617, 625, 643, 664, 712, 726, 751, 771, 784, 797)
* Penrose.sol (Line: 515, 536)
* SGLCommon.sol (Line: 192, 243)
* SGLLendingCommon.sol (Line: 49, 79)
* SGLLeverage.sol (Line: 47, 71, 74, 75)
* SGLLiquidation.sol (Line: 167, 179, 193, 275, 281, 282, 331, 350)
* Singularity.sol (Line: 188)
* StargateStrategy.sol (Line: 119, 162, 166, 168, 184, 186, 198, 204, 206, 215, 216, 224, 235, 248, 256)
* TapiocaDeployer.sol (Line: 60)
* TapiocaOptionBroker.sol (Line: 230, 342, 493, 532)
* TapiocaOptionLiquidityProvision.sol (Line: 186, 242)
* TapiocaWrapper.sol (Line: 198, 216)
* TricryptoLPStrategy.sol (Line: 161, 163, 165, 180, 183, 191, 202, 211, 212, 219, 221, 236, 239, 248, 250)
* TricryptoNativeStrategy.sol (Line: 104, 152, 154, 156, 171, 173, 185, 197, 199, 206, 226, 229, 240, 251)
* USDO.sol (Line: 68, 87, 99, 101)
* USDOLeverageModule.sol (Line: 161, 164, 167, 182, 198, 201, 212, 219, 220, 227, 229)
* USDOMarketModule.sol (Line: 160, 163, 166, 180)
* USDOOptionsModule.sol (Line: 127, 162, 167, 172, 191, 227)
* UniswapV2Swapper.sol (Line: 155, 165)
* UniswapV3Swapper.sol (Line: 186, 200)
* Vesting.sol (Line: 157)
* YearnStrategy.sol (Line: 104, 105, 112, 115, 122, 124, 139, 145)
* YieldBox.sol (Line: 148, 361, 383, 489)
* twTAP.sol (Line: 260, 448)

## GAS-16: Use nested if and avoid multiple check combinations

### Description

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Affected file

* AaveStrategy.sol (Line: 171)
* Balancer.sol (Line: 226)
* BaseTOFT.sol (Line: 412)
* BaseUSDO.sol (Line: 370)
* BoringMath.sol (Line: 27)
* MagnetarMarketModule.sol (Line: 402, 611)
* MagnetarV2.sol (Line: 1046)
* TapiocaOptionLiquidityProvision.sol (Line: 314)
* TapiocaWrapper.sol (Line: 121, 144)
* YieldBoxRebase.sol (Line: 35, 58)

## GAS-17: Use selfbalance()

### Description

Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.

### Affected file

* Balancer.sol (Line: 286)
* BaseTOFTLeverageModule.sol (Line: 230)
* BaseTOFTOptionsModule.sol (Line: 142)
* BaseTOFTStrategyModule.sol (Line: 225)
* BaseTapOFT.sol (Line: 312)
* CompoundStrategy.sol (Line: 105, 107, 151)
* MagnetarMarketModule.sol (Line: 286, 751)
* StargateStrategy.sol (Line: 206)
* USDOLeverageModule.sol (Line: 227)
* USDOOptionsModule.sol (Line: 127)

## GAS-18: Using > 0 costs more gas than != 0 when used on a uint in a require() statement

### Description

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance.

### Affected file

* AaveStrategy.sol (Line: 103, 103, 145, 145, 158, 158, 168, 168, 171, 171, 174, 174)
* AirdropBroker.sol (Line: 203, 392, 467)
* BaseSwapper.sol (Line: 127, 127, 127, 131, 131, 136, 136)
* BaseTOFT.sol (Line: 364)
* BaseTOFTLeverageModule.sol (Line: 135, 135)
* BaseTOFTMarketModule.sol (Line: 186, 186, 226, 226)
* BaseTOFTOptionsModule.sol (Line: 138, 138, 231, 231)
* BaseTOFTStrategyModule.sol (Line: 56, 98)
* BaseTapOFT.sol (Line: 104)
* BigBang.sol (Line: 348, 348, 449, 449, 475, 475, 481, 481, 487, 487, 495, 495, 604, 604, 680, 734, 734)
* ConvexTricryptoStrategy.sol (Line: 133, 133, 196, 196, 249)
* GlpStrategy.sol (Line: 119, 119)
* MagnetarMarketModule.sol (Line: 171, 171, 184, 184, 228, 228, 245, 245, 258, 258, 353, 353, 405, 405, 416, 416, 510, 743)
* MagnetarV2.sol (Line: 205, 235, 235)
* Market.sol (Line: 171, 171, 182, 182, 192, 192, 197, 197, 202, 202, 210, 210, 219, 219, 228, 228, 233, 233, 344)
* SGLLeverage.sol (Line: 159, 159)
* SGLLiquidation.sol (Line: 233, 233, 338, 338, 397)
* Singularity.sol (Line: 480, 480, 498, 498, 506, 506, 521, 521, 533, 533, 545, 545, 553, 553, 565, 565)
* StargateStrategy.sol (Line: 122, 122, 165, 165)
* TapOFT.sol (Line: 201, 201, 222)
* TapiocaOptionBroker.sol (Line: 419)
* TapiocaOptionLiquidityProvision.sol (Line: 177, 178, 181, 263, 281, 301)
* TricryptoLPStrategy.sol (Line: 113, 113, 162, 162, 249, 249)
* TricryptoNativeStrategy.sol (Line: 106, 106, 153, 153, 241, 241)
* USDO.sol (Line: 89)
* USDOLeverageModule.sol (Line: 119, 119)
* USDOMarketModule.sol (Line: 123, 123, 197, 197)
* USDOOptionsModule.sol (Line: 123, 123, 216, 216)
* Vesting.sol (Line: 68, 131, 131, 134, 134, 152, 152)
* twAML.sol (Line: 12)
* twTAP.sol (Line: 489, 489, 511, 511)

## GAS-19: With assembly, ```.call (bool success)``` transfer can be done gas-optimized

### Description

```return``` data ```(bool success,)``` has to be stored due to EVM architecture, but in a usage in assembly, 'out' and 'outsize' values are given (0,0), this storage disappears and gas optimization is provided.

### Affected file

* BaseSwapper.sol (Line: 99)
* BaseTOFT.sol (Line: 380, 411)
* BaseTOFTLeverageModule.sol (Line: 184, 280)
* BaseTOFTMarketModule.sol (Line: 160)
* BaseTOFTOptionsModule.sol (Line: 189)
* BaseTOFTStrategyModule.sol (Line: 152)
* BaseUSDO.sol (Line: 369)
* BigBang.sol (Line: 216)
* ConvexTricryptoStrategy.sol (Line: 356, 369)
* MagnetarV2.sol (Line: 1045, 1071)
* Multicall3.sol (Line: 51, 79)
* Penrose.sol (Line: 444)
* Singularity.sol (Line: 188, 625, 638)
* TapiocaWrapper.sol (Line: 120, 141)
* TricryptoLPStrategy.sol (Line: 105)
* USDOLeverageModule.sol (Line: 169)
* USDOMarketModule.sol (Line: 168)
* USDOOptionsModule.sol (Line: 174)

## GAS-20: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* Balancer.sol (Line: 143, 150, 316)
* BalancerStrategy.sol (Line: 169, 179, 238, 255, 288)
* BaseTOFTLeverageModule.sol (Line: 56, 89)
* BaseTOFTMarketModule.sol (Line: 56, 105)
* BaseTOFTOptionsModule.sol (Line: 47, 90)
* BaseTOFTStrategyModule.sol (Line: 60, 102)
* BaseTapOFT.sol (Line: 96, 172, 266)
* GlpStrategy.sol (Line: 329)
* MagnetarMarketModule.sol (Line: 191, 592, 653)
* MagnetarV2.sol (Line: 293, 323, 382, 399)
* MarketERC20.sol (Line: 264)
* USDOLeverageModule.sol (Line: 39, 72)
* USDOMarketModule.sol (Line: 40, 78)
* USDOOptionsModule.sol (Line: 32, 75)
* UniswapV2Swapper.sol (Line: 53)
* UniswapV3Swapper.sol (Line: 75)
* YieldBoxPermit.sol (Line: 53, 82)