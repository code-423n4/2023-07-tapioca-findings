# QA Report

## Summary

|Issue ID|Submodule|Issue|Severity|
|:-:|:-|:-|:-|
|1.1|YieldBox|NFT ownership may be complicated in the event of a hard fork|Low|
|1.2|YieldBox|`TokenType` enum should have null type as the default|Noncritical|
|2.1|tapioca-periph-audit|Arbitrum has a WBTC/USD price feed|Low|
|2.2|tapioca-periph-audit|Excess ether sent to `TapiocaDeployer#deploy` can be stolen by other users|Low|
|2.3|tapioca-periph-audit|`MagnetarV2` delegatecalls `MagnetarMarketModule` but they have different storage layouts|Low|
|2.4|tapioca-periph-audit|`UniswapV2Swapper` and `UniswapV3Swapper` can only swap tokens if their pair exists|Low|
|2.5|tapioca-periph-audit|`Seer` oracle circuit arrays should not be immutable|Low|
|2.6|tapioca-periph-audit|Uniswap V3 twap should not be used on certain networks|Noncritical|
|2.7|tapioca-periph-audit|Not approving 0 first risks revert|Noncritical|
|2.8|tapioca-periph-audit|Recent version of OpenZeppelin's `Ownable` requires manually assigning the `owner` at deploy time|Noncritical|
|2.9|tapioca-periph-audit|`_checkSender()` authorisation in various `MagnetarMarketModule` functions is mostly redundant|Noncritical|
|3.1|tapioca-yieldbox-strategies-audit|Harcoded slippage tolerance leads to DoS during market volatility|Low|
|3.2|tapioca-yieldbox-strategies-audit|`AaveStrategy#compound` stakes funds even if `depositThreshold` is not met|Low|
|3.3|tapioca-yieldbox-strategies-audit|`TricryptoNativeStrategy#_currentBalance` omits `compoundAmount`|Low|
|3.4|tapioca-yieldbox-strategies-audit|`CompoundStrategy#emergencyWithdraw` return value is always 0|Noncritical|
|3.5|tapioca-yieldbox-strategies-audit|`StargateStrategy` state variable `stgEthPool` is never used|Noncritical|
|3.6|tapioca-yieldbox-strategies-audit|Remove/Resolve open TODOs in `StargateStrategy`|Noncritical|
|4.1|tapiocaz-audit|Use of `msg.value` inside a for loop causes DoS|Low|
|4.2|tapiocaz-audit|`Balancer#rebalance` requires caller to send far too much ETH for native OFTs|Low|
|4.3|tapiocaz-audit|Discrepency between comment and code in `mTapiocaOFT#unwrap`|Noncritical|

Low: 11 findings

Noncritical: 9 findings

## YieldBox

### [1.1] NFT ownership may be complicated in the event of a hard fork

When there are hard forks (DAO hack in 2016, PoS merge in 2022), users often have to go through [many hoops](https://twitter.com/elerium115/status/1558471934924431363) to ensure that they control ownership of a particular NFT on every fork. Consider adding `require(n == block.chainid)` where `n` is the chainId of whichever chain the contract is deployed on. Alternatively, include the chain ID in the URI, so that there is no confusion about which chain is the owner of the NFT.

[`YieldBoxURIBuilder#uri`](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxURIBuilder.sol#L79-L135)

### [1.2] `TokenType` enum should have null type as the default

It is best practice to place the default value of a struct in the first (0th) position so that uninitialized enums will have the appropriate value. In the case of `TokenType`, the `None` value should be declared first rather than `Native`:

```solidity
File: YieldBox\contracts\enums\YieldBoxTokenType.sol

10: enum TokenType {
11:     Native,
12:     ERC20,
13:     ERC721,
14:     ERC1155,
15:     None
16: }
```

[TokenType](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/enums/YieldBoxTokenType.sol#L10-L16)

## tapioca-periph-audit

### [2.1] Arbitrum has a WBTC/USD price feed

`ARBTriCryptoOracle` is going to be deployed on Arbitrum:

```solidity
File: tapioca-periph-audit\contracts\oracle\implementations\ARBTriCryptoOracle.sol

29: /// @dev Addresses are for Arbitrum
30: contract ARBTriCryptoOracle is ITOracle {
```

The contract queries a BTC/USD feed and a WBTC/BTC feed, then performs calculations to get the WBTC/USD price. This is unecessary because unlike mainnet, Arbitrum has a [WBTC/USD price feed](https://arbiscan.io/address/0xd0C7101eACbB49F3deCcCc166d238410D6D46d57), as is shown in [Chainlink's docs](https://docs.chain.link/data-feeds/price-feeds/addresses?network=arbitrum). On top of making the function easier to understand, simply querying the WBTC/USD feed would also save gas fees on the computation for users, and LINK fees on the extra external call to the pool for the protocol.

```diff
    function _get() internal view returns (uint256 _maxPrice) {
        uint256 _vp = TRI_CRYPTO.get_virtual_price();

        // Get the prices from chainlink and add 10 decimals
-       uint256 _btcPrice = uint256(BTC_FEED.latestAnswer()) * 1e10;
-       uint256 _wbtcPrice = uint256(WBTC_FEED.latestAnswer()) * 1e10;
        uint256 _ethPrice = uint256(ETH_FEED.latestAnswer()) * 1e10;
        uint256 _usdtPrice = uint256(USDT_FEED.latestAnswer()) * 1e10;

-       uint256 _minWbtcPrice = (_wbtcPrice < 1e18)
-           ? (_wbtcPrice * _btcPrice) / 1e18
-           : _btcPrice;

+       uint256 _minWbtcPrice = uint256(WBTC_USD_FEED.latestAnswer()) * 1e10;
```
[`ARBTriCryptoOracle#_get`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L117-L128)

Note that it is suggested to use `latestRoundData` instead of `latestAnswer` as the former is deprecated and does not provide protection against price staleness. This is described in a separate medium severity finding.

### [2.2] Excess ether sent to `TapiocaDeployer#deploy` can be stolen by other users

`TapiocaDeployer#deploy` checks that the contract has at least enough ether to execute the deployment, but a user may accidentally send more ether than required. In this case, the ether remains in the deployer contract after the transaction succeeds. As there is no access control, and the check used `address(this).balance` rather than `msg.value`, the next user to deploy a contract can steal the ether and use it to deploy their own contract.

Consider changing the first require check to `require(msg.value == amount, "Create2: insufficient balance")`.

```diff
    function deploy(
        uint256 amount,
        bytes32 salt,
        bytes memory bytecode,
        string memory contractName
    ) external payable returns (address addr) {
        require(
-           address(this).balance >= amount,
+           msg.value == amount,
            "Create2: insufficient balance"
        );
        require(
            bytecode.length != 0,
            string.concat(
                "Create2: bytecode length is zero for contract ",
                contractName
            )
        );
        /// @solidity memory-safe-assembly
        assembly {
            addr := create2(amount, add(bytecode, 0x20), mload(bytecode), salt)
        }
        require(
            addr != address(0),
            string.concat(
                "Create2: Failed on deploy for contract ",
                contractName
            )
        );
    }
```
[`TapiocaDeployer#deploy`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/TapiocaDeployer/TapiocaDeployer.sol#L22-L50)

### [2.3] `MagnetarV2` delegatecalls `MagnetarMarketModule` but they have different storage layouts

When using `delegatecall` it is vital to ensure that the calling contract and the implementation contract have the same storage layout, otherwise [storage collisions](https://mixbytes.io/blog/collisions-solidity-storage-layouts) could lead to extreme consequences. However, `MagnetarV2` inherits from `Ownable` and `MagnetarStorage` (in that order), whereas `MagnetarMarketModule` inherits only from `MagnetarStorage`. In `MagnetarV2`, the state variables from the `Ownable` contract will occupy storage slots first, followed by the state variables from `MagnetarStorage`.

Although in this case none of the functions executed via the delegatecall affect the state of the calling contract, it is still important to fix this, as it could result in contracts becoming completely non-functional in future versions.

```solidity
File: tapioca-periph-audit\contracts\Magnetar\MagnetarV2.sol

30: contract MagnetarV2 is Ownable, MagnetarV2Storage { // @audit Ownable state variables come before MagnetarV2Storage state variables in storage
```
[MagnetarV2.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L30)

```solidity
File: tapioca-periph-audit\contracts\Magnetar\modules\MagnetarMarketModule.sol

20: contract MagnetarMarketModule is MagnetarV2Storage {
```
[MagnetarMarketModule.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L20)

```solidity
File: tapioca-periph-audit\contracts\Magnetar\MagnetarV2.sol

1064:     function _executeModule(
1065:         Module _module,
1066:         bytes memory _data
1067:     ) private returns (bytes memory returnData) {
1068:         bool success = true;
1069:         address module = _extractModule(_module);
1070: 
1071:         (success, returnData) = module.delegatecall(_data); // @audit here
1072:         if (!success) {
1073:             _getRevertMsg(returnData);
1074:         }
1075:     }
```
[`MagnetarV2#_executeModule`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2.sol#L1064-L1075)

### [2.4] `UniswapV2Swapper` and `UniswapV3Swapper` can only swap tokens if their pair exists

`UniswapV2Swapper` uses Uniswap V2's [`Router02#swapExactTokensForTokens`](https://docs.uniswap.org/contracts/v2/reference/smart-contracts/router-02#swapexacttokensfortokens) to swap between two input tokens. The function takes an input `path`. Uniswap docs state that "The first element of path is the input token, the last is the output token, and any intermediate elements represent intermediate pairs to trade through (if, for example, a direct pair does not exist)". However the `UniswapV2Swapper` only passes in a path of length two, assuming the pair exists, which it may not.

```solidity
File: tapioca-periph-audit\contracts\Swapper\UniswapV2Swapper.sol

107:     function swap(
108:         SwapData calldata swapData,
109:         uint256 amountOutMin,
110:         address to,
111:         bytes memory data
112:     )

124:         // Create swap path for UniswapV2Router02 operations
125:         address[] memory path = _createPath(tokenIn, tokenOut);

151:         uint256[] memory amounts = swapRouter.swapExactTokensForTokens(
152:             amountIn,
153:             amountOutMin,
154:             path,
155:             swapData.yieldBoxData.depositToYb ? address(this) : to,
156:             deadline
157:         );
```
[`UniswapV2Swapper#swap`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L125)

```solidity
File: tapioca-periph-audit\contracts\Swapper\BaseSwapper.sol

166:     function _createPath(
167:         address tokenIn,
168:         address tokenOut
169:     ) internal pure returns (address[] memory path) {
170:         path = new address[](2);
171:         path[0] = tokenIn;
172:         path[1] = tokenOut;
173:     }
```
[_createPath](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L166-L173)

Similarly, since `UniswapV3Swapper` calls []`SwapRouter#exactInputSingle`](https://github.com/Uniswap/v3-periphery/blob/main/contracts/SwapRouter.sol#L126), the swap only executes if a pool with both tokens at the specified fee exists.

```solidity
File: tapioca-periph-audit\contracts\Swapper\UniswapV3Swapper.sol

180:         ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
181:             .ExactInputSingleParams({
182:                 tokenIn: tokenIn,
183:                 tokenOut: tokenOut,
184:                 fee: poolFee,
185:                 recipient: swapData.yieldBoxData.depositToYb
186:                     ? address(this)
187:                     : to,
188:                 deadline: deadline,
189:                 amountIn: amountIn,
190:                 amountOutMinimum: amountOutMin,
191:                 sqrtPriceLimitX96: 0
192:             });
193: 
194:         // Compute outputs
195:         amountOut = swapRouter.exactInputSingle(params);
```
[`UniswapV3Swapper#swap`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L180-L195)

Consider adding functionality to allow users to define their own path if they wish, so that they are capable of swapping between tokens when the Uniswap pair/pool does not exist.

### [2.5] `Seer` oracle circuit arrays should not be immutable

The `Seer` oracle uses multiple Chainlink price feeds to get price, and multiple Uniswap V3 pools to get TWAP. These feeds/pools are assigned at deployment and currently there is no functionality to change them. This is unwise because
- Chainlink feeds may become [deprecated](https://docs.chain.link/data-feeds/deprecating-feeds#:~:text=Data%20Feeds%20without%20publicly%20known,incurred%20by%20Chainlink%20node%20operators.) over time. Should this happen to any one feed in the circuit, calls to `latestRoundData` would always revert and the entire oracle would become unusable, regardless of whether the Uniswap V3 TWAP oracle is working as intended.
- Reliability of Uniswap V3 pools as oracles is dependent on their liquidity. If any one pool in the circuit becomes illiquid (e.g. liquidity migrates to a newer version, such as [Uniswap V4](https://blog.uniswap.org/uniswap-v4)) then the returned price can become inaccurate and cheap to manipulate. Despite the use of both Uniswap and Chainlink oracles, this could still lead to negative outcomes because the higher price of the two is always used, and so a malicious user manipulating the Uniswap TWAP to the upside would not be prevented.

Consider adding functionality to allow admins to change the circuit so that the protocol can react to events such as those described above.

[Seer.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/Seer.sol)

### [2.6] Uniswap V3 twap should not be used on certain networks

Uniswap V3 [documentation](https://docs.uniswap.org/concepts/protocol/oracle#oracles-integrations-on-layer-2-rollups) states that:
    
    On Optimism, every transaction is confirmed as an individual block. The
    `block.timestamp` of these blocks, however, reflect the `block.timestamp`
    of the last L1 block ingested by the Sequencer. For this reason, Uniswap
    pools on Optimism are not suitable for providing oracle prices, as this
    high-latency `block.timestamp` update process makes the oracle much less
    costly to manipulate.

Note that this also applies to the Base L2, as it is built on the Bedrock release of the OP stack (https://docs.base.org/differences). Tapioca [documentation](https://docs.tapioca.xyz/tapioca/) states that they intend to facilitate "dozens of networks", which is likely to include one or both of these networks. Although the protocol utilises both Uniswap and Chainlink oracles, the higher price is always used, which would allow a malicious user to manipulate Uniswap oracle price to the upside with no restriction.

Consider checking `block.chain.id` when accessing price from a Uniswap oracle.

[UniswapUtils.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/utils/UniswapUtils.sol)

### [2.7] Not approving 0 first risks revert

While this finding is present in the automated report, the below instance was missed.

`BaseSwapper#_safeApprove` is used to call `approve` on a `token`. However for some tokens (such as USDT) approvals will always revert if there exists a non-zero allowance to protect against a race condition described [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729). It is recommended to first call `approve` with `amount` set to zero before approving the desired amount to account for such tokens.

```solidity
File: tapioca-periph-audit\contracts\Swapper\BaseSwapper.sol

098:     function _safeApprove(address token, address to, uint256 value) internal {
099:         (bool success, bytes memory data) = token.call(
100:             abi.encodeWithSelector(0x095ea7b3, to, value)
101:         );
102:         require(
103:             success && (data.length == 0 || abi.decode(data, (bool))),
104:             "BaseSwapper::safeApprove: approve failed"
105:         );
106:     }
```
[`BaseSwapper#_safeApprove`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L98-L106)

### [2.8] Recent version of OpenZeppelin's `Ownable` requires manually assigning the `owner` at deploy time

While old versions of OpenZeppelin's `Ownable` automatically set the `owner` to the deployer in the constructor ([Ownable <v4.9](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol#L38-L40)), as of version 4.9 it is required to manually pass the owner as a parameter ([Ownable v4.9](
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol#L38-L40)). While currently these contracts are not using the most recent version of OpenZeppelin, this is important to keep in mind because if the team decides to upgrade to v4.9 in the future, in their current form each `Ownable` contract would have an uninitialized `owner` and no way to assign it.

### [2.9] `_checkSender()` authorisation in various `MagnetarMarketModule` functions is mostly redundant

Various functions in `MagnetarMarketModule` have an address parameter `user` which allows the caller to perform actions on behalf of that user. The function `_checkSender()` is used to ensure that this is not done without permission. However the function simply checks whether `user == msg.sender`:

```solidity
File: tapioca-periph-audit\contracts\Magnetar\MagnetarV2Storage.sol

336:     function _checkSender(address _from) internal view {
337:         require(_from == msg.sender, "MagnetarV2: operator not approved");
338:     }
```

Therefore it is redundant. Users may only perform operations on behalf of themselves, and so there is no point having a `user` parameter or spending gas to call `_checkSender` for each operation. Consider redesigning these functions, or change `_checkSender` to implement some sort of approval system.

[`MagnetarV2Storage#_checkSender`](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L336-L338)

## tapioca-yieldbox-strategies-audit

### [3.1] Harcoded slippage tolerance leads to DoS during market volatility

Hardcoding slippage parameters is dangerous because it can lead to DoS in times of market volatility. In many strategy contracts within tapioca-yieldbox-strategies, `minAmount` is hardcoded (mostly as 0.5% in these contracts) when swapping or adding/removing liquidity, which can be problematic.

In each strategy contract listed below, the `_withdraw` function involves an external call with hardcoded slippage of some sort. This means that, in times of extreme volatility, users will be completely unable to withdraw their funds from the strategy. This is also true for `emergencyWithdraw`, a function seemingly implemented for precisely such conditions.

For example, if the price of ETH starts to fall dramatically and a user wants to call `YieldBox#withdraw` to get back their WETH and sell it for stablecoins, the transaction would likely fail as it would not be able to be performed without exceeding the fixed 0.5% slippage. Thus the user is unable to withdraw their funds, even if they would be happy experiencing higher slippage to do so.

LidoEthStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L108
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/lido/LidoEthStrategy.sol#L151

TricryptoNativeStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L170
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L188
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L232
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L249

TricryptoLPStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L179
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L179

StargateStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L182

AaveStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L193

BalancerStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L122
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L177
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/balancer/BalancerStrategy.sol#L250

ConvexTricryptoStrategy.sol
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L207
- https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/convex/ConvexTricryptoStrategy.sol#L313

### [3.2] `AaveStrategy#compound` stakes funds even if `depositThreshold` is not met

In `AaveStrategy#compound`, the final step is to deposit the wrapped native token to the Aave lending pool. The comment on line 196 states that it should be staked only if the amount `queued` is greater than `depositThreshold`, however no such check is made. Add a `require` condition before calling `lendingPool.deposit`, or remove the comment.

```solidity
File: tapioca-yieldbox-strategies-audit\contracts\aave\AaveStrategy.sol

138:     function compound(bytes memory) public {

196:             //stake if > depositThreshold
197:             uint256 queued = wrappedNative.balanceOf(address(this));
198:             lendingPool.deposit(
199:                 address(wrappedNative),
200:                 queued,
201:                 address(this),
202:                 0
203:             );
```
[`AaveStrategy#compound`](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/aave/AaveStrategy.sol#L196-L203)

### [3.3] `TricryptoNativeStrategy#_currentBalance` omits `compoundAmount`

As shown by a TODO comment in `_currentBalance`, it excludes `compoundAmount` in the calculation, because to calculate `compoundAmount`, `lpGauge.claimable_tokens` must be called, which is not a `view` function. This is overcome in `TricryptoLPStrategy#_currentBalance` by performing a static call:

```solidity
File: tapioca-yieldbox-strategies-audit\contracts\curve\TricryptoLPStrategy.sol

104:     function compoundAmount() public view returns (uint256 result) {
105:         (bool success, bytes memory response) = address(lpGauge).staticcall(
106:             abi.encodeWithSignature("claimable_tokens(address)", address(this))
107:         );
108:         result = 0;
109:         uint256 claimable = 0;
110:         if (success) {
111:             claimable = abi.decode(response, (uint256));
112:         }
```

Consider implementing the same logic in `TricryptoNativeStrategy` as well so that the `_currentBalance` function returns a more accurate value.

[`TricryptoNativeStrategy#compoundAmount`](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoNativeStrategy.sol#L104)
[`TricryptoLPStrategy#compoundAmount`](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/curve/TricryptoLPStrategy.sol#L105-L112)

### [3.4] `CompoundStrategy#emergencyWithdraw` return value is always 0

After performing an emergency withdraw on Compounds native ETH cToken, line 105 wraps the contracts entire balance. This means that the return value `result`, which is set to `address(this).balance` **after** this wrapping occurs, will always be zero, regardless of how much was withdrawn.

```solidity
File: tapioca-yieldbox-strategies-audit\contracts\compound\CompoundStrategy.sol

100:     function emergencyWithdraw() external onlyOwner returns (uint256 result) {
101:         compound("");
102: 
103:         uint256 toWithdraw = cToken.balanceOf(address(this));
104:         cToken.redeem(toWithdraw);
105:         INative(address(wrappedNative)).deposit{value: address(this).balance}(); // @audit entire eth balance is wrapped
106: 
107:         result = address(this).balance; // @audit eth balance is always 0
108:     }
```

[`CompoundStrategy#emergencyWithdraw`](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L105-L107)

### [3.5] `StargateStrategy` state variable `stgEthPool` is never used

The test suite indicates that this variable will point to an address that [mainnet.env](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/env/mainnet.env#L45) labels "STARGATE_UNISWAPV3_POOL". Either remove the variable to save deployment gas and improve readability, or implement it as intended.

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/stargate/StargateStrategy.sol#L43

### [3.6] Remove/Resolve open TODOs in `StargateStrategy`

TODO comments should be implemented/ignored, and then removed before code is pushed to production.

```solidity
File: tapioca-yieldbox-strategies-audit\contracts\stargate\StargateStrategy.sol

31: //TODO: decide if we need to start with ETH and wrap it into WETH; stargate allows ETH. not WETH, while others allow WETH, not ETH
32: //TODO: handle rewards deposits to yieldbox
```
https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/stargate/StargateStrategy.sol#L31-L32

## tapiocaz-audit

### [4.1] Use of `msg.value` inside a for loop causes DoS

In `TapiocaWrapper#executeCalls`, `msg.value` is used for each call that is performed inside a for loop. After the first iteration, there will not be enough ETH left to perform the second call as it has all already been spent, leading to a revert. Thus the function will always revert whenever more than one call is trying to be executed, making the function effectively useless.

Redesign the function to allow the caller to specify how much value to send with each individual call.

```solidity
File: tapiocaz-audit\contracts\TapiocaWrapper.sol

126:     /// @notice Execute the `_bytecode` against the `_toft`. Callable only by the owner.
127:     /// @dev Used to call derived OFT functions to a TOFT contract.
128:     /// @param _call The array calls to do.
129:     /// @return success If the execution was successful.
130:     /// @return results The message of the execution, could be an error message.
131:     function executeCalls(
132:         ExecutionCall[] calldata _call
133:     )
134:         external
135:         payable
136:         onlyOwner
137:         returns (bool success, bytes[] memory results)
138:     {
139:         results = new bytes[](_call.length);
140:         for (uint256 i = 0; i < _call.length; i++) {
141:             (success, results[i]) = payable(_call[i].toft).call{
142:                 value: msg.value // @audit here
143:             }(_call[i].bytecode);
144:             if (_call[i].revertOnFailure && !success) {
145:                 revert TapiocaWrapper__TOFTExecutionFailed(results[i]);
146:             }
147:         }
148:     }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L142

### [4.2] `Balancer#rebalance` requires caller to send far too much ETH for native OFTs

In `Balancer`, the `rebalance` function extracts funds from one OFT and transfers it to another OFT on a different chain. To do this, `msg.value` must be high enough to cover the fee for the relayer.

The check for whether `msg.value` is sufficient when the underlying token is the native token (L204) is excessive. Here `msg.value` only needs to be high enough to cover the fee, as the native token to cover the `_amount` has already been received from the `_srcOft` (L199). However, the check requires that `msg.value` is greater than `_amount`, which is not necessary as `_amount` is likely far larger than what the fee will be. This means that the caller will have to send far more native token with the transaction than is necessary. For example, if `_amount` is 100 ETH and the fee is expected to be roughly 0.1ETH, the caller would have to supply `msg.value` as at least 100 ETH, and then wait for Stargate to send ~99.9 ETH to the refund address later.

Although this does not lead to a loss of funds, it is highly inconvenient and implements strict and unecessary capital limitations on the owner of the `Balancer` contract. Change the check on line 204 to be the same as the one on line 207 to fix this.

```solidity
File: tapiocaz-audit\contracts\Balancer.sol

173:     function rebalance(
174:         address payable _srcOft,
175:         uint16 _dstChainId,
176:         uint256 _slippage,
177:         uint256 _amount,
178:         bytes memory _ercData
179:     )
180:         external
181:         payable
182:         onlyOwner
183:         onlyValidDestination(_srcOft, _dstChainId)
184:         onlyValidSlippage(_slippage)
185:     {
186:         if (connectedOFTs[_srcOft][_dstChainId].rebalanceable < _amount)
187:             revert RebalanceAmountNotSet();
188: 
189:         //check if OFT is still valid
190:         if (
191:             !_isValidOft(
192:                 _srcOft,
193:                 connectedOFTs[_srcOft][_dstChainId].dstOft,
194:                 _dstChainId
195:             )
196:         ) revert DestinationOftNotValid();
197: 
198:         //extract
199:         ITapiocaOFT(_srcOft).extractUnderlying(_amount); @audit funds received from _srcOft
200: 
201:         //send
202:         bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
203:         if (_isNative) {
204:             if (msg.value <= _amount) revert FeeAmountNotSet(); // @audit should be `address(this).balance <= _amount`, or `msg.value > 0`
205:             _sendNative(_srcOft, _amount, _dstChainId, _slippage);
206:         } else {
207:             if (msg.value == 0) revert FeeAmountNotSet();
208:             _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
209:         }
210: 
211:         connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
212:         emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);
213:     }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L203

### [4.3] Discrepency between comment and code in `mTapiocaOFT#unwrap`

Natspec for the `unwrap` function in `mTapiocaOFT` states that the function can be "called only on [the] host chain" (L101), however the implementation shows that it can actually be called on any connected chain (L105). Since connected chains can be added by the owner calling `updateConnectedChain`, this is indeed a discrepency that ought to be addressed.

```solidity
File: tapiocaz-audit\contracts\tOFT\mTapiocaOFT.sol

101:     /// @notice Unwrap an ERC20/Native with a 1:1 ratio. Called only on host chain.
102:     /// @param _toAddress The address to unwrap the tokens to.
103:     /// @param _amount The amount of tokens to unwrap.
104:     function unwrap(address _toAddress, uint256 _amount) external {
105:         require(connectedChains[block.chainid], "TOFT_host");
106:         require(!balancers[msg.sender], "TOFT_auth");
107:         _unwrap(_toAddress, _amount);
108:     }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L101-L108

```solidity
File: tapiocaz-audit\contracts\tOFT\mTapiocaOFT.sol

116:     function updateConnectedChain(
117:         uint256 _chain,
118:         bool _status
119:     ) external onlyOwner {
120:         emit ConnectedChainStatusUpdated(
121:             _chain,
122:             connectedChains[_chain],
123:             _status
124:         );
125:         connectedChains[_chain] = _status;
126:     }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L116-L126
