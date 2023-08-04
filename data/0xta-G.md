# Gas Optimization

# Summary

| Number | Optimization Details                                                                       | Context |
| :----: | :----------------------------------------------------------------------------------------- | :-----: |
| [G-01] | Use nested if statements instead of &&                                                     |    9    |
| [G-02] | Using ERC721A instead of ERC721 for more gas-efficient                                     |    4    |
| [G-03] | Use hardcode address instead `address(this)`                                               |   13    |
| [G-04] | Can make the variable outside the loop to save gas                                         |    4    |
| [G-05] | Using calldata instead of memory for `read-only arguments` in external functions saves gas |    4    |
| [G-06] | Use assembly to check for `address(0)`                                                     |    8    |
| [G-07] | Use bitmap to save gas                                                                     |    5    |
| [G-08] | Don't initialize variables with default value                                              |    9    |
| [G-09] | With assembly, `.call (bool success)`  transfer can be done gas-optimized                  |    8    |
| [G-10] | Use `selfbalance()` instead of address(this).balance                                       |    7    |
| [G-11] | Sort Solidity operations using short-circuit mode                                          |    9    |
| [G-12] | Use assembly to emit events                                                                |   15    |
| [G-13] | Bytes constants are more efficient than String constants                                   |    1    |
| [G-14] | Before transfer of some functions, we should check some variables for possible gas save    |    5    |
| [G-15] | Use assembly to hash instead of solidity                                                   |    3    |
| [G-16] | Use constants instead of type(uintx).max                                                   |   32    |
| [G-17] | Make 3 event parameters indexed when possible                                              |   113   |
| [G-18] | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements     |    7    |

## [G-01] Use nested if statements instead of &&

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

## [G-02] Using ERC721A instead of ERC721 for more gas-efficient

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

## [G-03] Use hardcode address instead `address(this)`

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

## [G-04] Can make the variable outside the loop to save gas

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

## [G-05] Using calldata instead of memory for read-only arguments in external functions saves gas

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

## [G-06] Use assembly to check for address(0)

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

## [G-07] Use bitmap to save gas

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

## [G-08] Don't initialize variables with default value

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

## [G-09] With assembly, `.call (bool success)`  transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

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

## [G-10] Use `selfbalance()` instead of address(this).balance

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

## [G-11] Sort Solidity operations using short-circuit mode

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

## [G-12] Use assembly to emit events

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

## [G-13] Bytes constants are more efficient than String constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

```solidity
file: /contracts/glp/GlpStrategy.sol
26    string public constant override name = "sGLP";

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L26

## [G-14] Before transfer of some functions, we should check some variables for possible gas save

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

## [G-15] Use assembly to hash instead of solidity

```solidity
File:  Yieldbox/tree/master/contracts/YieldBoxPermit.sol

53     bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));

82   bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol#L53

```solidity
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol

418       bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L418

## [G-16] Use constants instead of type(uintx).max

it's generally more gas-efficient to use constants instead of type(uintX).max when you need to set the maximum value of an unsigned integer type.

```solidity
File:  tap-token-audit/tree/master/contracts/governance/twTAP.sol

313  require(expiry < type(uint56).max, "twTAP: too long");
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol#L313

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol

172  if (spenderAllowance != type(uint256).max) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L172

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol

75    wrappedNative.approve(_lendingPool, type(uint256).max);

76    rewardToken.approve(_multiSwapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L75

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol

77        wrappedNative.approve(_vault, type(uint256).max);

78        IERC20(address(pool)).approve(_vault, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol#L77

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol

58  wrappedNative.approve(_cToken, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol#L58

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol

105     wrappedNative.approve(_lpGetter, type(uint256).max);
        lpToken.approve(_lpGetter, type(uint256).max);
        lpToken.approve(_booster, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);

174    rewardToken.approve(_swapper, type(uint256).max);

183    wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol#L105

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol

79      lpToken.approve(_lpGauge, type(uint256).max);
        lpToken.approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

144     rewardToken.approve(_swapper, type(uint256).max);

154     wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol#L79

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol

78      IERC20(lpGetter.lpToken()).approve(_lpGauge, type(uint256).max);
        IERC20(lpGetter.lpToken()).approve(_lpGetter, type(uint256).max);
        rewardToken.approve(_multiSwapper, type(uint256).max);
        wrappedNative.approve(_lpGetter, type(uint256).max);

135    rewardToken.approve(_swapper, type(uint256).max);

145     wrappedNative.approve(_lpGetter, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol#L78

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol

62   IERC20(_stEth).approve(_curvePool, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol#L62

```solidity
File:   tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol

89      stgNative.approve(_lpStaking, type(uint256).max);

        stgNative.approve(address(router), type(uint256).max);

94      stgTokenReward.approve(_swapper, type(uint256).max);

153     stgTokenReward.approve(_swapper, type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol#L89

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol

59   wrappedNative.approve(address(vault), type(uint256).max);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol#L59

```solidity
File:  Yieldbox/tree/master/contracts/BoringMath.sol

6  require(a <= type(uint128).max, "BoringMath: uint128 Overflow");

11 require(a <= type(uint64).max, "BoringMath: uint64 Overflow");

16 require(a <= type(uint32).max, "BoringMath: uint32 Overflow");
```

https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol#L6

## [G-17] Make 3 event parameters indexed when possible

events are used to emit information about state changes in a contract. When defining an event, it's important to consider the gas cost of emitting the event, as well as the efficiency of searching for events in the Ethereum blockchain.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events. When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.
Exmple:

```
• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);
```

```solidity
File:  tap-token-audit/tree/master/contracts/Vesting.sol
60    event UserRegistered(address indexed user, uint256 amount);

62    event Claimed(address indexed user, uint256 amount);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L60

```solidity
File:  tap-token-audit/tree/master/contracts/governance/twTAP.sol
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
File:  tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol
115    event Participate(uint256 indexed epoch, uint256 aoTAPTokenID);

123    event NewEpoch(uint256 indexed epoch, uint256 epochTAPValuation);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol#L115

```solidity
File:  tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol
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
File:  tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol
78    event Emitted(uint256 week, uint256 amount);

80    event Minted(address indexed _by, address indexed _to, uint256 _amount);

82    event Burned(address indexed _from, uint256 _amount);

84    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
```

https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol#L78

```solidity
File: tapioca-bar-audit/tree/master/contracts/Penrose.sol
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
File: tapioca-bar-audit/tree/master/contracts/markets/Market.sol
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
File: tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol
66  event ApprovalBorrow(
        address indexed owner,
        address indexed spender,
        uint256 value
    );
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol#L66

```solidity
File:  tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol
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
File:  tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol
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

```solidity
File: tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol
331    event ApprovalForAll(address owner, address operator, bool approved);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol#L331

```solidity
File: tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol
43    event PoolFee(uint256 _old, uint256 _new);
```

https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol#L43

```solidity
File:  tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol
53  event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);
```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol#L171

```solidity
File: tapiocaz-audit/tree/master/contracts/Balancer.sol
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

## [G-18] Pre-increments and pre-decrements are cheaper than post-increments and post-decrements

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