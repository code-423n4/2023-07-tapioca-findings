# [G-01] Redundant checks

Double check to ensure the transfer of tokens is allowed. The lines [YieldBox.sol#L310](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L310) and [YieldBox.sol#L322](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L322) do exactly the same, thus it cost gas unnecessarily. Remove the one in line 322 (the second one)

```
function batchTransfer(address from, address to, uint256[] calldata assetIds_, uint256[] calldata shares_) public {
        uint256 len = assetIds_.length;
        for (uint256 i = 0; i < len; i++) {
            _requireTransferAllowed(from, isApprovedForAsset[from][msg.sender][assetIds_[i]]); <================= HERE
        }

        _transferBatch(from, to, assetIds_, shares_);
    }

    function _transferBatch(address from, address to, uint256[] calldata ids, uint256[] calldata values) internal override {
        require(to != address(0), "No 0 address");

        uint256 len = ids.length;
        for (uint256 i = 0; i < len; i++) {
            uint256 id = ids[i];
            _requireTransferAllowed(from, isApprovedForAsset[from][msg.sender][id]); <============== HERE
            uint256 value = values[i];
            balanceOf[from][id] -= value;
            balanceOf[to][id] += value;
        }

        emit TransferBatch(msg.sender, from, to, ids, values);
    }
```

It's safe to delete because `_transferBatch` is 1) internal and 2) only called by `transferBatch`

# [G-02] Logic optimization
Consider adding an `else` clause instead of the [return routine in MagnetarMarketModule.sol#L698](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L698) so that it saves bytecode size by removing one return opcode, that is `if(!dest chain) {...} else {...}` instead of the current implementation (which has three implicit `return` routines, one at the end of the `if`, on the `catch` block and at the end of the function). 

```
        ...

        if (dstChainId == 0) {
            yieldBox.withdraw(
                assetId,
                from,
                LzLib.bytes32ToAddress(receiver),
                amount,
                share
            );
            return; <=============================================== HERE +1
        }
        // perform a cross chain withdrawal
        (, address asset, , ) = yieldBox.assets(assetId);
        // make sure the asset supports a cross chain operation
        try
            IERC165(address(asset)).supportsInterface(
                type(ISendFrom).interfaceId
            )
        {} catch {
            return; <=================================================== HERE +1
        }

        // withdraw from YieldBox
        yieldBox.withdraw(assetId, from, address(this), amount, 0);

        // build LZ params
        bytes memory _adapterParams;
        ISendFrom.LzCallParams memory callParams = ISendFrom.LzCallParams({
            refundAddress: msg.value > 0 ? refundAddress : payable(this),
            zroPaymentAddress: address(0),
            adapterParams: ISendFrom(address(asset)).useCustomAdapterParams()
                ? adapterParams
                : _adapterParams
        });

        // sends the asset to another layer
        ISendFrom(address(asset)).sendFrom{value: gas}(
            address(this),
            dstChainId,
            receiver,
            amount,
            callParams
        ); <=============================================== HERE +1
```

Consider making the body of the function an `if`/`else` block so that there is a `return` routine on the `cath` block and another one at the end of the function instead of returning at the end of the `if` clause (2 vs the current three)

# [G-03] Redundant checks
In [TapiocaWrapper.sol#L86-L90](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L86-L90) it is doing a check to avoid OOB indexing. However, starting from version 0.8.0, Solidity does check for underflows, thus it makes impossible to OOB in this situation. Consider removing the `if` clause because it is doing unnecessary opcodes (although it is more explicit to let it there)

```
function lastTOFT() external view returns (ITapiocaOFT) {
        if (tapiocaOFTs.length == 0) {
            revert TapiocaWrapper__NoTOFTDeployed();
        }
        return tapiocaOFTs[tapiocaOFTs.length - 1]; <====================== HERE reverts by default if underflow
    }
```