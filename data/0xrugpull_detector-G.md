## Use unchecked.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L245-L249
```solidity
    function _useNonce(
        address owner
    ) internal virtual returns (uint256 current) {
+       unchecked {
            current = _nonces[owner]++; // @audit - unchecked.
+        }
    }
```

## Use constant or `onERC1155Received.selector`
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L356-L371
```solidity
    function onERC1155Received(
        address,
        address,
        uint256,
        uint256,
        bytes calldata
    ) external pure returns (bytes4) {
-        return
-            bytes4(
-                keccak256(
-                    "onERC1155Received(address,address,uint256,uint256,bytes)"
-                )
-            ); // @audit - define it as constant or onERC1155Received.selector
+        return this.onERC1155Received.selector;
    }
```