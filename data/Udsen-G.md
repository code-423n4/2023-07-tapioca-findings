## 1. ARITHMETIC OPERATION CAN BE UNCHECKED TO SAVE GAS

In the `Market.computeTVLInfo` following depicted arithmetic operation can be `unchecked` to save gas due to previous conditional check.

        amountToSolvency = borrowPart >= collateralAmountInAsset
            ? borrowPart - collateralAmountInAsset
            : 0;

The above code snippet can be modified as follows to save gas:

        unchecked{
            amountToSolvency = borrowPart >= collateralAmountInAsset
            ? borrowPart - collateralAmountInAsset
            : 0;
        }

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L325-L327

## 2. ASSIGNING STATE VARIABLES TO DEFAULT VALUE CAN BE OMITTED TO SAVE GAS

In the `Vesting.sol` contract the `seeded` state variable is assigned to the default value of `0` as shown below:

    uint256 public seeded = 0;

This can be omitted to save gas, since by default `seeded` variable will have the value `0`. This will save an extra `SSTORE` operation.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L29

## 3. REDUNDANT DECLARATION AND ASSIGNMENT TO MEMORY VARIABLE CAN BE OMITTED TO SAVE GAS

In the `Vesting._vested` function a redundant memory variable declaration and assignment is performed as shown below:

        uint256 total = _total;

But the `_total` input variable can be used for the operations instead of the `total` variable. The modified code snippet is shown below:

    function _vested(uint256 _total) private view returns (uint256) {
        if (start == 0) return 0;
        if (block.timestamp < start + cliff) return 0;
        if (block.timestamp >= start + duration) return _total;
        return (_total * (block.timestamp - start)) / duration;
    }

This will save an extra `MLOAD` operation.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L169

## 4. `i < sglLastIndex` CHECK IS REDUNDANT IN THE `TapiocaOptionLiquidityProvision.unregisterSingularity` FUNCTION

The `TapiocaOptionLiquidityProvision.unregisterSingularity` function is used to  Un-register a singularity market. It deletes the corresponding the contract state related to the given `singularity` address. The following condition verifies that the `sglAssetID` of the `singularity` is equal to a respecitve `assetId` in the `_singularities` array.

                    _singularities[i] == sglAssetID && i < sglLastIndex

The `i < sglLastIndex` conditional check is redundant since this condition is already checkd at the begining of the `for` loop as shown below:

            for (uint256 i = 0; i < sglLength; i++) {

Hence the above conditinal check can be modified as follows to save gas.

                    _singularities[i] == sglAssetID

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L315
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L308

## 5.  `require` STATEMENT SHOULD BE EXECUTED BEFORE OTHER STATE OPERATIONS TO SAVE GAS IN THE EVENT OF A REVERT

In the `TapiocaOptionBroker.exerciseOption` function, there are multiple `require` statements implemented for input data validation. 

        require(isPositionActive, "tOB: Option expired");

The above `require` statement which is used to verify that the `position` is active, is exeucted after several `SLOAD` operations as shown below:

        uint256 cachedEpoch = epoch;

        PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
            _paymentToken
        ];


Hence if this `require` statement reverts, the gas spent on the above `state` operations will be lost. Hence it is recommended to execute the above `require` statement at the beginning of the `TapiocaOptionBroker.exerciseOption` function as shown below:

        (bool isPositionActive, LockPosition memory tOLPLockPosition) = tOLP
            .getLock(oTAPPosition.tOLP);
        require(isPositionActive, "tOB: Option expired");

This way even if this `require` statement reverts it will not waste gas since other state operations are executed after this call.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L376
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L358-L359

## 6. UNUSED `private` FUNCTIONS CAN BE REMOVED TO SAVE GAS

The `Singularity._executeViewModule` function is a private function and is not used within the scope of the `Singularity` contract. Hence it can be removed to save on the deployment gas. Since it is a `view` function it can be made `public` if the usecase of the function is to let any external user to call it.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L631-L642

## 7. REDUNDANT CONDITIONAL CHECKS CAN BE OMITTED TO SAVE GAS

The `YieldBox.batchTransfer` function performs the `_requireTransferAllowed` check by iterating through a `for` loop of length `assetIds_.length`, to verify whether the `share` amount transfer is allowed for each `assetId`. Later the `YieldBox.batchTransfer` function calls the `YieldBox._transferBatch` which also performs the same `_requireTransferAllowed` check on the same set of `assetIds_` array by iterating through a `for` loop. Hence the second `_requireTransferAllowed` is a redundant check and can be omitted to save gas.

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L310
https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L322