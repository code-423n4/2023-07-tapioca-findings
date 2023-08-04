# QA Report
## Summary
Total 11 instances
- [01] Lack of check `signer != address(0)` in `YieldBoxPermit.sol`
- [02] USE OF DEPRECATED CHAINLINK FUNCTION: `LATESTANSWER`
- [03] Missing checks for `address(0x0)` when assigning values to `address` state variables (missed by bot)
- [04] Implement SafeERC20 to avoid non-zero to non-zero approvals
- [05] Erroneous comments in `Balancer.sol`
- [06] Lack of check data.length != 0 in `Balancer.sol#_sendToken()`
- [07] Project Upgrade and Stop Scenario should be
- [08] SOLIDITY VERSION 0.8.19 CAN BE USED
- [09] Use SMTChecker
- [10] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
- [11] Consider implementing the latest openzeppelin to avoid unexpected bugs
## [01] Lack of check `signer != address(0)` in `YieldBoxPermit.sol`
Should checking `_signer != address(0)`, where `address(0)` means an invalid signature.
File: YieldBox/contracts/YieldBoxPermit.sol
```
57:        address signer = ECDSA.recover(hash, v, r, s); // @audit check signer != address(0)
58:        require(signer == owner, "YieldBoxPermit: invalid signature");
          ...
86:        address signer = ECDSA.recover(hash, v, r, s); // @audit check signer != address(0)
87:        require(signer == owner, "YieldBoxPermit: invalid signature");
```
[YieldBoxPermit.sol#L57-L58](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L57-L58)
[YieldBoxPermit.sol#L86-L87](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L86-L87)

## [02] USE OF DEPRECATED CHAINLINK FUNCTION: `LATESTANSWER`
### Line of code
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L121-L124
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L51
### Impact
According to Chainlink’s documentation [API Reference](https://docs.chain.link/data-feeds/api-reference), the `latestAnswer` function is deprecated. This function does not throw an error if no answer has been reached, but instead returns 0, possibly causing an incorrect price to be fed to the different price feeds or even a `Denial of Service` by a division by zero.
### POC
```
File: tapioca-periph-audit/contracts/oracle/implementations/ARBTriCryptoOracle.sol
121:        uint256 _btcPrice = uint256(BTC_FEED.latestAnswer()) * 1e10;
122:        uint256 _wbtcPrice = uint256(WBTC_FEED.latestAnswer()) * 1e10;
123:        uint256 _ethPrice = uint256(ETH_FEED.latestAnswer()) * 1e10;
124:        uint256 _usdtPrice = uint256(USDT_FEED.latestAnswer()) * 1e10;
```
[ARBTriCryptoOracle.sol#L121-L124](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L121-L124)
```
File: tapioca-periph-audit/contracts/oracle/implementations/SGOracle.sol
51:            uint256(UNDERLYING.latestAnswer())) / SG_POOL.totalSupply();
```
[SGOracle.sol#L51](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/SGOracle.sol#L51)
### Tools
Manual Review
### RCM
- Use the `latestRoundData` function to get the price instead. Add checks on the return data with proper revert messages if the price is stale or the round is uncomplete.
- Referenced documentation:
[Chainlink - API Reference](https://docs.chain.link/data-feeds/api-reference)

## [03] Missing checks for `address(0x0)` when assigning values to `address` state variables (missed by bot)
I) File: YieldBox/contracts/NativeTokenFactory.sol
```
67:            pendingOwner[tokenId] = newOwner; // @audit check != address(0)
74:        address _pendingOwner = pendingOwner[tokenId]; // @audit check != address(0)
```
[NativeTokenFactory.sol#L67](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L67)
[NativeTokenFactory.sol#L74](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L74)
II) File: tapioca-bar-audit/contracts/markets/MarketERC20.sol
```
276:        address signer = ECDSA.recover(hash, v, r, s); // @audit check signer != address(0)
277:        require(signer == owner, "ERC20Permit: invalid signature");
```
[MarketERC20.sol#L276-L277](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L276-L277)

## [04] Implement SafeERC20 to avoid non-zero to non-zero approvals
### Description:
```
File: BaseSwapper.sol
    function _safeApprove(address token, address to, uint256 value) internal {
        (bool success, bytes memory data) = token.call(
            abi.encodeWithSelector(0x095ea7b3, to, value)
        );
        require(
            success && (data.length == 0 || abi.decode(data, (bool))),
            "BaseSwapper::safeApprove: approve failed"
        );
    }
```
[LINK TO CODE](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L98-L106)
Current function allows for non-zero to non-zero approvals, however, some ERC20 tokens would revert in such cases. Reference: [approval race protec-
tions](https://github.com/d-xo/weird-erc20#approval-race-protections)
### Recommendations:
1. Implement the SafeERC20 from OpenZeppelin, in particular safeApprove,
if it is used.
2. Replace the `hex codes` with `.selector`, i.e.
`abi.encodeWithSelector(token.approve.selector, to, value);`

## [05] Erroneous comments in `Balancer.sol`
[LINK TO CODE](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L169)
```
169:    /// @param _slippage the destination LayerZero id
```
It should be the slippage index, not the target LayerZero id

## [06] Lack of check data.length != 0 in `Balancer.sol#_sendToken()`
- `Balancer.sol#_sendToken()` is activated in `Balancer.sol#rebalance()` when `!_isNative` [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L207)
```
File: Balancer.sol
207:            _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
```
- [Balancer.sol#_sendToken()](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L297-L333)
```
File: Balancer.sol
297    function _sendToken(
298        address payable _oft,
299        uint256 _amount,
300        uint16 _dstChainId,
301        uint256 _slippage,
302        bytes memory _data
303    ) private {
304        IERC20Metadata erc20 = IERC20Metadata(ITapiocaOFT(_oft).erc20());
305        if (erc20.balanceOf(address(this)) < _amount) revert ExceedsBalance();
306:
307:        (uint256 _srcPoolId, uint256 _dstPoolId) = abi.decode(
308:            _data, // @audit lack of check data.length != 0
309:            (uint256, uint256)
310:        );
---SKIP---
333    }
```
- Should revert if `data.length == 0` as in [Balancer.sol#initConnectedOFT()](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L226)
### Recommendation:
Add this check before decode
```
        if (_data.length == 0) revert PoolInfoRequired();
```

## [07] Project Upgrade and Stop Scenario should be
At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

This can be done by adding the 'pause' architecture, which is included in many projects, to critical functions, and by authorizing the existing onlyOwner.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

## [08] SOLIDITY VERSION 0.8.19 CAN BE USED
Using the more updated version of Solidity can enhance security. As described in https://github.com/ethereum/solidity/releases, Version `0.8.19` is the latest version of Solidity, which "contains a fix for a long-standing bug that can result in code that is only used in creation code to also be included in runtime bytecode". To be more secured and more future-proofed, please consider using Version `0.8.19` for the following contracts.
Contexts: ALL CONTRACTS

## [09] Use SMTChecker
The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## [10] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
### Description:
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.
#### constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

## [11] Consider implementing the latest openzeppelin to avoid unexpected bugs
[@openzeppelin/contracts vulnerabilities](https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts)
```
File: YieldBox/package.json
26:        "@openzeppelin/contracts": "^4.8.2",
```
https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/package.json#L26
```
File: tap-token-audit/package.json
25:    "@openzeppelin/contracts": "^4.8.2",
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/package.json#L25
```
File: tapioca-bar-audit/package.json
46:        "@openzeppelin/contracts": "^4.8.2",
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/package.json#L46
```
File: tapioca-periph-audit/package.json
27        "@openzeppelin/contracts": "^4.5.0",
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/package.json#L27
```
File: tapioca-yieldbox-strategies-audit/package.json
49:    "@openzeppelin/contracts": "^4.5.0",
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/package.json#L49
```
File: tapiocaz-audit/package.json
32:        "@openzeppelin/contracts": "^4.5.0",
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/package.json#L32