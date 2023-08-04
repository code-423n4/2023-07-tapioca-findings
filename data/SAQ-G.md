## Summary

### Gas Optimization

no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements | 7 | - |
| [G-02] | Can make the variable outside the loop to save gas | 4 | - |
| [G-03] | Require() or Revert() statement should be used sorted from cheapest to most expensive  | 1 | - |
| [G-04] | Using calldata instead of memory for read-only arguments in external functions saves gas | 4 | - |
| [G-05] | Should use arguments instead of state variable | 6 | - |
| [G-06] | Use assembly to check for address(0) | 8 | - |
| [G-07] | Before transfer of  some functions, we should check some variables for possible gas save | 5 | - |
| [G-08] | Use bitmap to save gas | 5 | - |
| [G-09] | Bytes constants are more efficient than String constants | 1 | - |
| [G-10] | Don't initialize variables with default value | 9 | - |
| [G-11] | With assembly, .call (bool success)  transfer can be done gas-optimized | 8 | - |
| [G-12] | Use nested if and, avoid multiple check combinations | 6 | - |
| [G-13] | Use assembly to write address storage values | 5 | - |
| [G-14] | Use selfbalance() instead of address(this).balance | 7 | - |
| [G-15] | Use assembly to emit events | 12 | - |
| [G-16] | Using ERC721A instead of ERC721 for more gas-efficient | 6 | - |
| [G-17] | Sort Solidity operations using short-circuit mode | 8 | - |
| [G-18] | Use hardcode address instead address(this) | 13 | - |
| [G-19] | Use assembly for math (add, sub, mul, div) | 19 | - |
| [G-20] | Don't apply the same value to state variables | 2 | - |
| [G-21] | Use != 0 instead of > 0 for unsigned integer comparison | 5 | - |
| [G-22] | Use assembly to calculate hashes to save gas | 3 | - |
| [G-23] | Refactor event to avoid emitting empty data | 3 | - |
| [G-24] | State variable read in a loop | 5 | - |
| [G-25] | Using Bools For Storage Incurs Overhead | 11 | - |
| [G-26] | bytes.concat() can be used in place of abi.encodePacked | 5 | - |
| [G-27] | Use of emit inside a loop | 3 | - |
| [G-28] | Unused named return variables without optimizer waste gas | 7 | - |

## Gas Optimizations  

## [G-1]  Pre-increments and pre-decrements are cheaper than post-increments and post-decrements

 Saves 5 gas per iteration


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
file: /contracts/markets/singularity/SGLLiquidation.sol

387                liquidatedCount++;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L387

## [G-2] Can make the variable outside the loop to save gas

Consider making the stack variables before the loop which gonna save gas
and the variable will re-initialized per iteration, this will consume extra gas.

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


## [G-3]  Require() or Revert() statement should be used sorted from cheapest to most expensive 
      
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, 
the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

187        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");

190        require(tapAmount >= 1e18, "tOB: Too low");

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L187


## [G-4] Using calldata instead of memory for read-only arguments in external functions saves gas 

When a function with a memory array is called externally, the abi.decode ()  step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

```solidity
file: /contracts/tOFT/modules/BaseTOFTStrategyModule.sol

96        bytes memory airdropAdapterParam

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L96

```solidity
file: /contracts/governance/twTAP.sol

363        IERC20[] memory _rewardTokens

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L363

```solidity
file: /contracts/Balancer.sol

177        bytes memory _ercData

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L177


```solidity
file: /contracts/tOFT/modules/BaseTOFTLeverageModule.sol

208        IUSDOBase.ILeverageExternalContractsData memory externalData,

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L208

## [G-5] Should use arguments instead of state variable 

state variables should not used in emit  ,  This will save near 97 gas   

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

///@audit the ' epoch ' and ' epochTAPValuation '  are state variables, should use argument instead. 

293        emit NewEpoch(epoch, epochTAPValuation);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L293

```solidity
file: /contracts/tokens/TapOFT.sol

///@audit the ' paused ' is state variable. 

154        emit PausedUpdated(paused, val);

///@audit the ' minter ' is state variable

162        emit MinterUpdated(minter, _minter);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L154

```solidity
file: /contracts/tOFT/mTapiocaOFT.sol

///@audit the ' connectedChains ' is state variable

120      emit ConnectedChainStatusUpdated(
            _chain,
            connectedChains[_chain],
            _status
124        );

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L120


```solidity
file: /contracts/Swapper/UniswapV3Swapper.sol

///@audit The ' poolFee ' is state variable

62        emit PoolFee(poolFee, _newFee);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol#L62


```solidity
file: /contracts/yearn/YearnStrategy.sol

///@audit The ' depositThreshold ' is state variable

91        emit DepositThreshold(depositThreshold, amount);

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/yearn/YearnStrategy.sol#L91


## [G-6] Use assembly to check for address(0)

  Save 6 gas per instance.

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


## [G-7] Before transfer of  some functions, we should check some variables for possible gas save

Before transfer, we should check for amount being 0 so the function doesn't run when its not gonna do anything:

```solidity
file: /contracts/tokens/LTap.sol

42        tapToken.transferFrom(msg.sender, address(this), amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/LTap.sol#L42

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

530       _paymentToken.transferFrom(
            msg.sender,
            address(this),
533         discountedPaymentAmount

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L530-L533

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

514        tapOFT.transfer(msg.sender, tapAmount);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L514

```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

281        yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);

282        yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L281-L282


## [G-8] Use bitmap to save gas

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



## [G-9] Bytes constants are more efficient than String constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

```solidity
file: /contracts/glp/GlpStrategy.sol
///@audit bytes4 public constant override name = "sGLP";  ==> the total string 4 bytes.

26    string public constant override name = "sGLP";

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L26


## [G-10] Don't initialize variables with default value

that initializing a variable with its default value can consume unnecessary gas and increase the size of the compiled contract.


```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

383        uint256 liquidatedCount = 0;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L383

```solidity
file: /contracts/Magnetar/modules/MagnetarMarketModule.sol

414        uint256 tOLPTokenId = 0;

508        uint256 tOLPId = 0;

571        uint256 _removeAmount = 0;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L414


```solidity
file: /contracts/balancer/BalancerStrategy.sol

154        int256 index = -1;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol#L154

```solidity
file: /contracts/Magnetar/MagnetarV2.sol

1068        bool success = true;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L1068

```solidity
file: /contracts/curve/TricryptoLPStrategy.sol

109        uint256 claimable = 0;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoLPStrategy.sol#L109

```solidity
file: /contracts/governance/twTAP.sol

160            participant.multiplier = 0;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L160

```solidity
file: /contracts/curve/TricryptoNativeStrategy.sol

105        result = 0;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoNativeStrategy.sol#L105

## [G-11] With assembly, .call (bool success)  transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), 
this storage disappears and gas optimization is provided.


```solidity
file: /contracts/Magnetar/MagnetarV2.sol

1045        (bool success, bytes memory returnData) = target.call(actionCalldata);

1071        (success, returnData) = module.delegatecall(_data);

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L1045

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


## [G-12] Use nested if and, avoid multiple check combinations

Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

```solidity
file: /contracts/usd0/BaseUSDO.sol

370        if (!success && !_forwardRevert) {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L370

```solidity
file: /contracts/TapiocaWrapper.sol

121        if (_revertOnFailure && !success) {

144            if (_call[i].revertOnFailure && !success) {   

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L121

```solidity
file: /contracts/Balancer.sol

226        if (!isNative && _ercData.length == 0) revert PoolInfoRequired();

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L226

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

315        _singularities[i] == sglAssetID && i < sglLastIndex

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L315

```solidity
file: /contracts/Magnetar/modules/MagnetarMarketModule.sol

611        if (
            !removeAndRepayData.assetWithdrawData.withdraw &&
            removeAndRepayData.repayAssetOnBB
614        ) {

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L611-L614
 
## [G-13] Use assembly to write address storage values

By using assembly to write to address storage values, you can bypass some of these operations and lower the gas cost of writing to storage. Assembly code allows you to directly access the Ethereum Virtual Machine (EVM) and perform low-level operations that are not possible in Solidity.

example of using assembly to write to address storage values:
```solidity
contract MyContract {
    address private myAddress;
    
    function setAddressUsingAssembly(address newAddress) public {
        assembly {
            sstore(0, newAddress)
        }
    }
}
```
Instances:
```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

/// @audit the ' paymentTokenBeneficiary ' is address storage variable.

360        paymentTokenBeneficiary = _paymentTokenBeneficiary;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L360

```solidity
file: /contracts/glp/GlpStrategy.sol

114        feeRecipient = recipient;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L114

```solidity
file: /contracts/Penrose.sol

284        conservator = _conservator;

264        bigBangEthMarket = _market;

456        feeTo = feeTo_;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L284


## [G-14] Use selfbalance() instead of address(this).balance

Using selfbalance() instead of address(this).balance can save gas in Solidity because selfbalance() is a built-in function that returns the balance of the current contract directly as a uint256 value, without the need to create a new address object.
On the other hand, using address(this).balance to get the balance of the current contract creates a new address object, which consumes more gas and can make the code less efficient.
By using selfbalance(), you can reduce the amount of gas consumed by your Solidity code and make your contracts more efficient.

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


## [G-15] Use assembly to emit events

We can use assembly to emit events efficiently by utilizing scratch space and the free memory pointer. This will allow us to potentially avoid memory expansion costs. Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

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


## [G-16] Using ERC721A instead of ERC721 for more gas-efficient 

ERC721A is a proposed extension to the ERC721 standard that aims to improve gas efficiency and reduce the cost of deploying and using non-fungible tokens (NFTs) on the Ethereum blockchain. 

Reference 1: https://nextrope.com/erc721-vs-erc721a-2/

Reference 2 :https://github.com/chiru-labs/ERC721A#about-the-project


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


## [G-17] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows
 f(x) || g(y) 
 f(x) && g(y)
```


```solidity
file: /contracts/TapiocaWrapper.sol

///@audit the ' !success ' should be write first to avoid more consume of gas.

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


## [G-18] Use hardcode address instead address(this)
 
 Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.
References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

Here's an example :
```solidity
contract MyContract {
    address constant public CONTRACT_ADDRESS = 0x1234567890123456789012345678901234567890;
    
    function getContractAddress() public view returns (address) {
        return CONTRACT_ADDRESS;
    }
}
```

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

## [G-19] Use assembly for math (add, sub, mul, div)

Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

306            uint256 sglLastIndex = sglLength - 1;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L306

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

535        paymentAmount = paymentAmount / (10 ** (18 - _paymentTokenDecimals));

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L535


```solidity
file: /contracts/tokens/TapOFT.sol

207        uint256 unclaimed = emissionForWeek[week - 1] - mintedInWeek[week - 1];

242        return ((timestamp - emissionsStartTime) / WEEK) + 1; // Starts at week 1

254        result = (dso_supply * decay_rate) / DECAY_RATE_DECIMAL;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L207

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

192        uint256 otcAmountInUSD = tapAmount * epochTAPValuation; // Divided by TAP decimals

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L192

```solidity
file: /contracts/oracle/implementations/ARBTriCryptoOracle.sol

135        uint256 _g = (TRI_CRYPTO.gamma() * 1 ether) / GAMMA0;

136        uint256 _a = (TRI_CRYPTO.A() * 1 ether) / A0;

137        uint256 _discount = Math.max((_g ** 2 / 1 ether) * _a, 1e34); // handle qbrt nonconvergence

141        _maxPrice -= (_maxPrice * _discount) / 1 ether;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L135
```solidity
file: /contracts/lido/LidoEthStrategy.sol

108         uint256 minAmount = (toWithdraw * 50) / 10_000; //0.5%

151         uint256 minAmount = toWithdraw - (toWithdraw * 250) / 10_000; //2.5%

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/lido/LidoEthStrategy.sol#L108

```solidity
file: /contracts/curve/TricryptoNativeStrategy.sol

116            result = result - (result * 50) / 10_000; //0.5%

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoNativeStrategy.sol#L116

```solidity
file: /contracts/stargate/StargateStrategy.sol

251            uint256 toWithdraw = amount - queued;

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/stargate/StargateStrategy.sol#L251

## [G-20] Don't apply the same value to state variables

Assigning the same value to a state variable repeatedly can cause unnecessary gas costs, because each assignment operation incurs a gas cost. Additionally, if the value being assigned is the same as the current value of the state variable, the assignment operation does not actually change anything, which can waste gas.

```solidity
file: /contracts/tokens/LTap.sol

///@audit '   lockedUntil and  maxLockedUntil ' are state variables which have same value.

37        lockedUntil = _maxLockedUntil;

38        maxLockedUntil = _maxLockedUntil;
 
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/LTap.sol#L37

## [G-21] Use != 0 instead of > 0 for unsigned integer comparison

it's generally more gas-efficient to use != 0 instead of > 0 when
comparing unsigned integer types.

Here's an example of how you can use != 0 instead of > 0:
``` solidity
contract MyContract {
    uint256 public myUnsignedInteger;
    
    function myFunction() public view returns (bool) {
        // Use != 0 instead of > 0
        return myUnsignedInteger != 0;
    }
}
```

```solidity
file: /contracts/tOFT/modules/BaseTOFTMarketModule.sol

186        if (approvals.length > 0) {   

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L186

```solidity
file: /contracts/tOFT/modules/BaseTOFTOptionsModule.sol

138        if (approvals.length > 0) {

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L138

```solidity
file: /contracts/Vesting.sol

68        require(_duration > 0, "Vesting: no vesting");

152       if (start > 0) revert Initialized();

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L68

```solidity
file: /contracts/governance/twTAP.sol

489                if (amount > 0) {

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L489


## [G-22] Use assembly to calculate hashes to save gas

Using assembly to calculate hashes can save 80 gas per instance.

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

364        return
            bytes4(
                keccak256(
                    "onERC1155Received(address,address,uint256,uint256,bytes)"
                )
369            );

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L364-L369

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

418        bytes32 leaf = keccak256(abi.encodePacked(msg.sender));

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L418

```solidity
file: /contracts/markets/MarketERC20.sol

263        bytes32 structHash = keccak256(
            abi.encode(
                asset ? _PERMIT_TYPEHASH : _PERMIT_TYPEHASH_BORROW,
                owner,
                spender,
                value,
                _useNonce(owner),
                deadline
            )
272        );
 
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L263-L272


## [G-23] Refactor event to avoid emitting empty data

```solidity
file: /contracts/curve/TricryptoNativeStrategy.sol

115            result = swapper.getOutputAmount(swapData, "");

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoNativeStrategy.sol#L115

```solidity
file: /contracts/YieldBoxURIBuilder.sol

121          asset.tokenType == TokenType.ERC1155 ? "" : ',"decimals":',

122          asset.tokenType == TokenType.ERC1155 ? "" : details.decimals.toString(),

```
https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxURIBuilder.sol#L121-L122

## [G-24] State variable read in a loop

State variable reads and writes are more expensive than local variable reads and writes. Therefore, it is recommended to replace state variable reads and writes within loops with local variable reads and writes.

```solidity
file: /contracts/TapiocaWrapper.sol

99            harvestableTapiocaOFTs[i].harvestFees();

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L99

```solidity
file: /contracts/options/TapiocaOptionLiquidityProvision.sol

///@audit activeSingularities and sglAssetIDToAddress are state variables

137                pools[i] = activeSingularities[
138                    sglAssetIDToAddress[_singularities[i]]
139                ];

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L137-L139

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

333                phase1Users[_users[i]] = _amounts[i];

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L333

```solidity
file: /contracts/governance/twTAP.sol

492               rewardTokens[i].safeTransfer(_to, amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L492


## [G-25] Using Bools For Storage Incurs Overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
file: /contracts/tokens/TapOFT.sol

70    bool public paused;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L70

```solidity
file: /contracts/Multicall/Multicall3.sol

25        bool allowFailure;

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol#L25


```solidity
file: /contracts/usd0/BaseUSDOStorage.sol

30    bool public paused;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol#L30

```solidity 
file: /contracts/Vesting.sol

36        bool revoked;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L36

```solidity
file: /contracts/governance/twTAP.sol

35    bool hasVotingPower;

36    bool divergenceForce; // 0 negative, 1 positive

37    bool tapReleased; // allow restaking while rewards may still accumulate

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L35-L37

```solidity
file: /contracts/options/TapiocaOptionBroker.sol

27    bool hasVotingPower;

28    bool divergenceForce; // 0 negative, 1 positive

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L27-L28

```solidity
file: /contracts/Penrose.sol#L39

39    bool public paused;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L39

```solidity
file: /contracts/markets/bigBang/BigBang.sol

52     bool private _isEthMarket;

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L52


## [G-26] bytes.concat() can be used in place of abi.encodePacked

Given concatenation is not going to be used for hashing bytes.concat is the preferred method to use as its more gas efficient

```solidity
file: /contracts/Balancer.sol

272        bytes memory trustedRemotePath = abi.encodePacked(_dstOft, _srcOft);

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L272

```solidity
file: /contracts/option-airdrop/AirdropBroker.sol

418        bytes32 leaf = keccak256(abi.encodePacked(msg.sender));

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L418

```solidity
file: /contracts/tOFT/BaseTOFTStorage.sol

55            string(abi.encodePacked("TapiocaOFT-", _name)),

56            string(abi.encodePacked("t", _symbol)),

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFTStorage.sol#L55-L56

```solidity
file: /contracts/YieldBoxURIBuilder.sol

129      asset.tokenType == TokenType.ERC1155 ? string(abi.encodePacked(',"tokenId":', asset.tokenId.toString())) : "",

```
https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxURIBuilder.sol#L129


## [G-27] Use of emit inside a loop

Emitting an event inside a loop performs a Loop N times, where N is the loop length. Consider refactoring the code to emit the event only once at the end of loop. Gas savings should be multiplied by the average loop length.

```solidity
file: /contracts/YieldBox.sol

350            emit TransferSingle(msg.sender, from, to, assetId, share_);

```
https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L350


```solidity
file: /contracts/markets/singularity/SGLLiquidation.sol

134                emit LogRemoveCollateral(

139                emit LogRepay(    

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L134

## [G-28] Unused named return variables without optimizer waste gas

Consider changing the variable to be an unnamed one, since the variable is never assigned, nor is it returned by name. If the optimizer is not turned on, leaving the code as it is will also waste gas for the stack variable

```solidity
file: /contracts/oracle/implementations/GLPOracle.sol

26    ) public view override returns (bool success, uint256 rate) {

34    ) public view override returns (bool success, uint256 rate) {    

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/GLPOracle.sol#L26

```solidity
file: /contracts/oracle/implementations/SGOracle.sol

74    ) external view virtual returns (bool success, uint256 rate) {

84    ) external view virtual returns (uint256 rate) {    

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/SGOracle.sol#L74

```solidity
file: /contracts/oracle/Seer.sol

66    ) external view virtual returns (bool success, uint256 rate) {

77    ) external view virtual returns (uint256 rate) {    

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/Seer.sol#L66

```solidity
file: /contracts/yearn/YearnStrategy.sol

111    function _currentBalance() internal view override returns (uint256 amount) {

```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/yearn/YearnStrategy.sol#L111