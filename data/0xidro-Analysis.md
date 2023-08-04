## Low Risk and Non-Critical Issues

### [01] FUNCTION VISIBILITY
 The following view functions are not called internally by the children or the parent contract, better to switch them to external to save gas.
 - [func-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L199)
 - [func-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L209)
 - [func-3](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L214)
 - [func-4](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L219)
 - [func-5](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L201)
 - [func-6](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L232)

### [02] REDUNDANT EVENT DATA
 The following event emits unnecessary data `block.timestamp`. The block timestamp is by default added to the event and can be accessed by the indexer. I recommend removing it.
 - [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L247)

### [03] COMPLICATE CODE FOR SIMPLE PAUSE LOGIC
The logic related to updating the contract pauseability is complex and gas-consuming. The transaction can fail if the `status` is equal to the current contract `status`.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L247)
I recommend to update the code to a toggle logic. This modification, will reduce the gas usages caused by the input parameter and the check done in this
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L273).
```solidity
    /// @notice updates the pause state of the contract
    /// @dev can only be called by the conservator
    /// @param val the new value
    function updatePause() external {
        require(msg.sender == conservator, "Penrose: unauthorized");
        bool pauseMem = paused;
        emit PausedUpdated(!pauseMem);
        paused = !pauseMem;
    }
```
### [04] MISLEADING FUNCTION NAME
The functions `registerBigBang` and `registerSingularity` naming doesn't describ the logic. I recommand to rename them to `deployAndRegisterBigBang` and `deployAndRegisterSingularity`.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L362).
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L395).

### [05] EXECUTE MARKET FUNCTION CAN OVERFLOW
If the `mc` array length is not equal to the `data` array length, the transaction will revert with an `overflow` error.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L437-L449).

I recommend updating the code to:
```solidity
    function executeMarketFn(
        address[] calldata mc,
        bytes[] memory data,
        bool forceSuccess
    )
        external
        onlyOwner
        notPaused
        returns (bool[] memory success, bytes[] memory result)
    {
    require(mc.length == data.length, "Penrose: mc and data length mismatch");
    ...
    }
```
### [06] READ SAME VAR FROM STORAGE MULTIPLE TIME
The var `feeTo` is read from the storage multiple times inside the function `_depositFeesToYieldBox`.
[code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L499-L540)

I recommend to store the value on memory then use it in the code
```solidity
function _depositFeesToYieldBox(
        IMarket market,
        ISwapper swapper,
        IPenrose.SwapData calldata dexData
    ) private {
        require(swappers[swapper], "Penrose: Invalid swapper");
        require(isMarketRegistered[address(market)], "Penrose: Invalid market");
        
        // Add this line of code
        address feeToMem = feeTo;
        ...
        
        // Update this line of code
        (amount, ) = swapper.swap(
            swapData,
            dexData.minAssetAmount,
            feeToMem,
            ""
        );
        ...
        
        // Update this line of code
        yieldBox.transfer(address(this), feeToMem, assetId, feeShares);
    }
```

### [07] RENAME PARAM NAME
I recommand to rename the param `array` to `_masterContracts`.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L543)

### [08] READ MULTIPLE TIME FROM STORAGE THROUGH CROSS CONTRACT CALL
The function `clonesOfCount` is called inside 2 for loops to read the same data. 
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L551)
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L566)

I recommand to sotre the data on memory during the first cross contract call then reuse it on the second for loop. This will save gas cost.

```solidity
    function _getMasterContractLength(
        IPenrose.MasterContract[] memory array
    ) public view returns (address[] memory markets) {
        uint256 _masterContractLength = array.length;
        uint256 marketsLength = 0;
        
        // Add this array to store the clonesOfCount responses.
        uint256[] memory clonesOfCount = uint256[](_masterContractLength);

        unchecked {
            // We first compute the length of the markets array
            for (uint256 i = 0; i < _masterContractLength; ) {
                // Store the clonesOfCount response
                clonesOfCount[i] = clonesOfCount(array[i].location);
                // Update the nex line.
                marketsLength += clonesOfCount[i];

                ++i;
            }
        }

        markets = new address[](marketsLength);

        uint256 marketIndex;
        uint256 clonesOfLength;

        unchecked {
            // We populate the array
            for (uint256 i = 0; i < _masterContractLength; ) {
                address mcLocation = array[i].location;
                // Update the nex line to use in memory clonesOfCount.
                clonesOfLength = clonesOfCount[i];

                // Loop through clones of the current MC.
                for (uint256 j = 0; j < clonesOfLength; ) {
                    markets[marketIndex] = clonesOf[mcLocation][j];
                    ++marketIndex;
                    ++j;
                }
                ++i;
            }
        }
    }
```

### [09] GENERIC ERRORS
In the `setMarketConfig` all errors return the same string. I recommend creating custom errors for each case, this will help when debugging.

- [error-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L172)
- [error-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L198)
- [error-3](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L204)
- [error-4](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L214)
- [error-5](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L223)
- [error-6](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L236)

### [10] REFACTOR CODE TO REDUCE CONTRACT CODE SIZE
I recommand to refactore the function `computeClosingFactor` to improve the reduce the contract code size.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L256C14-L297)
```solidity
// Add internal function to scale the values.
function internal scale(uint256 _value, uint256 _decimal) returns (uint256 output){
    if (_decimal > 18) {
        return _value / (10 ** (_decimal - 18));
    } else if (_decimal < 18) {
        return _value * (10 ** (18 - _decimal));
    }
    return _value;
}

// Update the computeClosingFactor function.
function computeClosingFactor(
    uint256 borrowPart,
    uint256 collateralPartInAsset,
    uint256 borrowPartDecimals,
    uint256 collateralPartDecimals,
    uint256 ratesPrecision
) public view returns (uint256) {
    uint256 borrowPartScaled = scale(borrowPart, borrowPartDecimals);
    uint256 collateralPartInAssetScaled = scale(collateralPartInAsset, collateralPartDecimals);
    ...
}
```
### [11] WRONG FUNCTION VISIBILITY
The following modifiers are `virtual` which means they can be overwritten by children's contracts. I recommend switch the visibility to `internal`
- [modifier-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L94)
- [modifier-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L99)

Change the  visibility of the following functions to `external`
- [func-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L116)

### [12] MARKET ERC20 TRANSFER INVALID VALIDATIONS LEAD TO CONSUME MORE GAS AND ACTION MISLEADING
The `transfer` function implementation contains an error is call-data validation, this will cause the transfer function to not behave as expected. The execution will not revert if the `msg.sender == to`. This logic can confuse the user and impact the UX.
[line](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L143C36-L143C36) 
I recommend to fix the logic an revert the transfer if the calldata doesn't meet the ERC20 transfer conditions, please check the OZ [ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol#L118) contract.
The to and from addresses are also not checked if they are not equal to ZERO. The reason why we have to check this because ZERO address in transfer event means either a mint when `from` address is ZERO and burn when `to` is ZERO, this can impact offchain indexers.
I recommend changing the logic and throwing an error if the condition is not valid.

```solidity
    ...
    require(to != address(0), "to zero address");
    require(from != address(0), "from zero address");
    require(msg.sender != to, "From address can not be to");
    if (amount != 0) {...}
    ...
}
```

### [13] UNDERFLOW CHECK NOT REQUIRED
The following code doesn't need to check overflow
- [code-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L146C55-L146C55)
- [code-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L181)

```solidity
unchecked {
    balanceOf[msg.sender] -= amount;
}
}
```
### [14] ADD NATSPEC
Add solidity natspec to the following functions
- [function-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L224)

### [15] READ VAR FROM STORAGE MULTIPLE TIME
The `penrose` var is loaded from storage multiple times. To save gas use the in memory var `tapiocaBar_`
Update the following by in memory variable
- [code-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L129)
- [code-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L131)
- [code-3](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L141)

The same can be applied in the function `getDebtRate`. Store the `penrose` value on memory and use it to do cross contract calls.
- [code-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L181)
- [code-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L184)

The same can be applied in the function `buyCollateral`. Store the `yieldBox` value on memory and use it to do cross contract calls.
- [code-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L347)
- [code-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L347)

The same can be applied in the function `_liquidateUser`. Store the `yieldBox` value on memory and use it to do cross contract calls.
- [code-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L590)
- [code-2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L596)
- [code-3](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L608)
- [code-4](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L619)

The same can be applied in the function `_extractLiquidationFees`. Store the `yieldBox` value on memory and use it to do cross contract calls. 
- [code-5](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L648-L649)



### [15] REMOVE EXECUTE FUNCTION
Remove the `execute` function as this is a behaviour of a multicall contract. I recommand to use the [uniswap multicall v2 contract](https://etherscan.io/address/0x5ba1e12693dc8f9c48aad8770482f4739beed696#code).
This will make the contract bytes size smaller and reduce deployment cost.

### [16] DELETE DATA INSTEAD OF UPDATE
Updating the operator status to `false` in the `updateOperator` function means the data is deleted because the default value of a mapping `address -> bool` is false. Updating the value cost more than if the data is deleted. I recommend updating the code and using the delete key word when the status is `false`.
```solidity
function updateOperator(address operator, bool status) external {
    if (status) {
        operators[msg.sender][operator] = true;
    } else {
        delete operators[msg.sender][operator];
    }
}
```
### [17] EVENTS NOT EMITTED
The following functions doesn't emit any event.
- [func-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L227)

### [18] ALLOW OWER TO CALL FUNCTION WHEN CONTRACT IS ON PAUSE
The admin functions should not be impacted by the contract pause, the following functions should allow the admin to call them incase an emergency task is required.
- [func-1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L442)


## Medium Risk and Critical Issues

### [01] BLOCK THE ADMIN TO SET VALUE IF CONTRACT IS IN PAUSE MODE
When the contract is in pause mode, the admin will not be able to update the `totalBorrowCap` value. I recommand to remove the `notPaused` modifier.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L151)

## High Risk and Critical Issues

### [01] WRONG VALIDATION ORDER WHEN CONFIG MARKET CAN LEAD TO PROTOCOL LOOSE FUNDS
THe function `setMarketConfig` updates first `minLiquidatorReward` then `maxLiquidatorReward`. This can lead to wrong values.
```solidity
Example
// The current state of the contract is
    minLiquidatorReward = 1000;
    maxLiquidatorReward = 10000;

// Call setMarketConfig with following calldata with wrong data.
    _minLiquidatorReward = 10000
    _maxLiquidatorReward = 1000

// The new contract state is updated with the wrong data.
    minLiquidatorReward = 10000
    maxLiquidatorReward = 1000
```
This can impact the amount of rewards the liquidator will takes from the prtocol which is much more bigger than the `maxLiquidatorReward`.
The function `_getCallerReward` use the `maxLiquidatorReward` and `minLiquidatorReward` to calculate the amount the liquidator will take after a liquidation.
- [code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L456-L461)

The impacted contracts:
- [SGLLiquidation.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L310-L314)
- [BigBang.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L576-L580)

### [02] REPAY CHARGE USERS MORE THAN EXPECTED AMOUNT 
When call `yieldBox.withdraw` the amount passed as argument should be the `toWithdraw` but in this case the value is set the `amount`. This will lead to token looses.
[code](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L732)


### Time spent:
40 hours