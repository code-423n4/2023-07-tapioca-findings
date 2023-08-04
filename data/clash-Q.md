- Redundant check since msg.sender is equal to user
```solidity
            if (!extractFromSender) {
                _checkSender(user);
            }
            // transfers tokens from sender or from the user to this contract
            _extractTokens(
                extractFromSender ? msg.sender : user, // @audit same?
                collateralAddress,
                collateralAmount
            );
```
Combined with above check, `msg.sender` is equal to `user`.

2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol#L151

- No check for user in _depositRepayAndRemoveCollateralFromMarket
```solidity
            _extractTokens(
                extractFromSender ? msg.sender : user, // @audit no check?
                assetAddress,
                depositAmount
            );
```
Though no direct benefit to attacker, attacker can call this method for any user if he approved the contract.

2023-07-tapioca/tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol#L230

- _getRevertMsg can misinterpret error
2023-07-tapioca/tapioca-periph-audit/contracts/Multicall/Multicall3.sol#L96
Many instances of `_getRevertMsg`
```solidity
    function _getRevertMsg(bytes memory _returnData) private pure {
        // If the _res length is less than 68, then
        // the transaction failed with custom error or silently (without a revert message) // @audit custom error, ContractThatReverts, gitsub
        if (_returnData.length < 68) revert("Reason unknown");

        assembly {
            // Slice the sighash.
            _returnData := add(_returnData, 0x04)
        }
        revert(abi.decode(_returnData, (string))); // All that remains is the revert string // @audit only string?
    }
```
It's possible that custom errors have data longer than 68 bytes. Interpreting it as string will revert.
