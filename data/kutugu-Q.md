# Findings Summary

| ID     | Title                                                            | Severity |
| ------ | ---------------------------------------------------------------- | -------- |
| [L-01] | muldiv implementation error, incompatible with mul == 0          | Low      |
| [L-02] | yearn withdraw maxLoss set to 0 may result in DOS                | Low      |
| [L-03] | CompoundStrategy emergencyWithdraw return value is always 0      | Low      |
| [L-04] | emergencyWithdraw should not use hard-coded slippage             | Low      |
| [L-05] | Malicious attackers can use flashloan to manipulate get_dy       | Low      |
| [L-06] | multicall doesn't support passing msg.value, but is payable      | Low      |
| [L-07] | CurveSwapper doesn't have deadline protection                    | Low      |
| [L-08] | paymentToken does not support tokens with more than 18 decimal   | Low      |
| [L-09] | There is a precision error in interestPersecond reduce interest  | Low      |
| [L-10] | YieldBoxURI can be corrupted                                     | Low      |

# Detailed Findings

# [L-01] muldiv implementation error, incompatible with mul == 0

## Description

```solidity
    function muldiv(
        uint256 value,
        uint256 mul,
        uint256 div,
        bool roundUp
    ) internal pure returns (uint256 result) {
        result = (value * mul) / div;
        if (roundUp && (result * div) / mul < value) {
            result++;
        }
    }
```

When `mul == 0`, `(result * div) / mul` will revert.

## Recommendations

Use `(result * div) % mul != 0` to check remainder.

# [L-02] yearn withdraw maxLoss set to 0 may result in DOS

## Description

```solidity
    function _withdraw(
        address to,
        uint256 amount
    ) internal override nonReentrant {
        uint256 available = _currentBalance();
        require(available >= amount, "YearnStrategy: amount not valid");

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            uint256 pricePerShare = vault.pricePerShare();
            uint256 toWithdraw = (((amount - queued) *
                (10 ** vault.decimals())) / pricePerShare);

            vault.withdraw(toWithdraw, address(this), 0);
        }
        wrappedNative.safeTransfer(to, amount - 1); //rounding error

        emit AmountWithdrawn(to, amount);
    }
```

The third parameter of withdraw is `maxLoss`, which means the maximum acceptable loss to sustain on withdrawal, defaults to 0.01%.   
Hardcoded 0 value means accept no loss, if the account has too big of a position in a Yearn Vault, then a withdrawal may simply not be possible as the function will always revert.    
You can monitor the limits on https://yearn.watch/, per the Yearn withdraw code, the function will go through the withdrawal queue, adding up losses (if any) to the caller.
Because the hardcoded value will not offer any flexibility to the maxLoss parameter, the withdraw function will be DOS due to loss.

## Recommendations

Allows pass maxLoss parameter.

# [L-03] CompoundStrategy emergencyWithdraw return value is always 0

## Description

```solidity
    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 toWithdraw = cToken.balanceOf(address(this));
        cToken.redeem(toWithdraw);
        INative(address(wrappedNative)).deposit{value: address(this).balance}();

        result = address(this).balance;
    }
```

result obtains the balance after deposit, which is always 0.

```solidity
	contract CounterTest is Test {
		address constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

		function setUp() public {
			vm.createSelectFork("https://rpc.ankr.com/eth");
		}

		function testBalance() public {
			deal(address(this), 1 ether);
			uint256 balanceBefore = address(this).balance;
			IERC20(WETH).deposit{ value: 1 ether }();
			uint256 balanceAfter = address(this).balance;

			assert(balanceAfter != balanceBefore);
			assert(balanceAfter == 0);
		}
	}
```

## Recommendations

Use the balance before deposit

# [L-04] emergencyWithdraw should not use hard-coded slippage

## Description

```solidity
    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 toWithdraw = stEth.balanceOf(address(this));
        uint256 minAmount = (toWithdraw * 50) / 10_000; //0.5%
        result = curveStEthPool.exchange(1, 0, toWithdraw, minAmount);

        INative(address(wrappedNative)).deposit{value: result}();
    }
```

Of course, other places such as the TricryptoNativeStrategy's swap function also use a lot of hard-coded slippage, but it is particularly critical for emergencyWithdraw.      
emergencyWithdraw is used in an emergency scene, such as the market occurs a major accident and users run.      
At this time, it should not use hard-coded 0.5% slippage, this may lead to the failure of the transaction, token prices continue to fluctuate, and further lead to the loss of funds.     

## Recommendations

Allow owner input slippage

# [L-05] Malicious attackers can use flashloan to manipulate get_dy

## Description

```solidity
    function _currentBalance() internal view override returns (uint256 amount) {
        uint256 stEthBalance = stEth.balanceOf(address(this));
        uint256 calcEth = stEthBalance > 0
            ? curveStEthPool.get_dy(1, 0, stEthBalance)
            : 0;
        uint256 queued = wrappedNative.balanceOf(address(this));
        return calcEth + queued;
    }
```

Malicious attackers can use flashloan to manipulate `get_dy`, further manipulate `currentBalance` and `toWithdraw`, causing the agreement redeems a large amount of ETH, reducing the amount of stETH and rebase interest:
1. Malicious users use flashloan to increase the ETH price in curve pool, resulting in a decrease in get_dy and _currentBalance.
2. User withdraws, the stETH redemption logic is triggered, and _currentBalance is undervalued, resulting in a large number of stETH is redeemed. 
3. The extra ETH stays in the contract and cannot enjoy stETH's rebase benefits.

## Recommendations

Use oracle prices instead of pool to avoid price manipulation

# [L-06] multicall doesn't support passing msg.value, but is payable

## Description

```solidity
    function multicall(
        Call[] calldata calls
    ) public payable returns (Result[] memory returnData) {
        uint256 length = calls.length;
        returnData = new Result[](length);
        Call memory calli;
        for (uint256 i = 0; i < length; ) {
            Result memory result = returnData[i];
            calli = calls[i];

            (result.success, result.returnData) = calli.target.call(
                calli.callData
            );
            if (!result.success) {
                _getRevertMsg(result.returnData);
            }
            unchecked {
                ++i;
            }
        }
    }
```

multicall doesn't support passing msg.value, instead should use multicallValue.   
multicall is marked payable, if the user passes msg.value, funds are lost.

## Recommendations

Remove payable mark

# [L-07] CurveSwapper doesn't have deadline protection 

## Description

```solidity
    function _swapTokensForTokens(
        int128 i,
        int128 j,
        uint256 amountIn,
        uint256 amountOutMin
    ) private returns (uint256) {
        address tokenIn = curvePool.coins(uint256(uint128(i)));
        address tokenOut = curvePool.coins(uint256(uint128(j)));

        uint256 outputAmount = curvePool.get_dy(i, j, amountIn);
        require(outputAmount >= amountOutMin, "insufficient-amount-out");

        uint256 balanceBefore = IERC20(tokenOut).balanceOf(address(this));

        _safeApprove(tokenIn, address(curvePool), amountIn);
        curvePool.exchange(i, j, amountIn, amountOutMin);

        uint256 balanceAfter = IERC20(tokenOut).balanceOf(address(this));
        require(balanceAfter > balanceBefore, "swap failed");

        return balanceAfter - balanceBefore;
    }
```

Compared with UniswapSwapper, CurveSwapper does not have a deadline protection, the swap transaction may stay in the mempool for a long time, after which the swap result is still within the slippage range, but the user will still suffer losses:
1. If the user wants to swap 100 A for 100 B, A price == B price
2. Transactions stay in the mempool for some time due to network congestion
3. Then the price of B drops sharply, 100 A == 300 B. At this time, the searcher will use the sandwich arbitrage. Although the user gets 100 B, the actual value is 1/3

## Recommendations

Add deadline protection

# [L-08] paymentToken does not support tokens with more than 18 decimal

## Description

```solidity
    function setPaymentToken(
        ERC20 _paymentToken,
        IOracle _oracle,
        bytes calldata _oracleData
    ) external onlyOwner {
        paymentTokens[_paymentToken].oracle = _oracle;
        paymentTokens[_paymentToken].oracleData = _oracleData;

        emit SetPaymentToken(_paymentToken, _oracle, _oracleData);
    }

    function _getDiscountedPaymentAmount(
        uint256 _otcAmountInUSD,
        uint256 _paymentTokenValuation,
        uint256 _discount,
        uint256 _paymentTokenDecimals
    ) internal pure returns (uint256 paymentAmount) {
        // Calculate payment amount
        uint256 rawPaymentAmount = _otcAmountInUSD / _paymentTokenValuation;
        paymentAmount =
            rawPaymentAmount -
            muldiv(rawPaymentAmount, _discount, 100e4); // 1e4 is discount decimals, 100 is discount percentage
        paymentAmount = paymentAmount / (10 ** (18 - _paymentTokenDecimals));
    }
```

For NEAR such as tokens, decimals is more than 18 decimal, when calculating _getDiscountedPaymentAmount overflow happens, but in setting paymentToken didn't check.
Therefore, when setting a paymentToken larger than 18 decimal, it can be executed, but when the user wants to make payment, it will revert, affecting the user experience.

## Recommendations

Use `paymentAmount = paymentAmount * 10 ** _paymentTokenDecimals / 1e18;`

# [L-09] There is a precision error in interestPersecond reduce interest

## Description

```solidity
    // BigBang
    _accrueInfo.debtRate = uint64(annumDebtRate / 31536000); //per second
    extraAmount =
        (uint256(_totalBorrow.elastic) *
            _accrueInfo.debtRate *
            elapsedTime) /
        1e18;
```
In Bigbang, the interest is set according to the year, rather than seconds.     
In the calculation of interest, the interest is first divided by the number of seconds of the year, and then multiplied by the number of borrow.    
The first division and then multiplication lead to the division error is amplified, affecting the interest income.    
The maximum error can reach `(31536000 - 1) * borrowAmt`

## Recommendations

Multiply before divide

# [L-10] YieldBoxURI can be corrupted

## Description

```solidity
    string(
        abi.encodePacked(
            "data:application/json;base64,",
            abi
                .encodePacked(
                    '{"name":"',
                    details.name,
                    '","symbol":"',
                    details.symbol,
                    '"',
                    asset.tokenType == TokenType.ERC1155 ? "" : ',"decimals":',
                    asset.tokenType == TokenType.ERC1155 ? "" : details.decimals.toString(),
                    ',"properties":{"strategy":"',
                    uint256(uint160(address(asset.strategy))).toHexString(20),
                    '","tokenType":"',
                    details.tokenType,
                    '"',
                    properties,
                    asset.tokenType == TokenType.ERC1155 ? string(abi.encodePacked(',"tokenId":', asset.tokenId.toString())) : "",
                    "}}"
                )
                .encode()
        )
    );
```

Malicious users can use double quotes to corrupt uri structures and pass in malicious data, such as links to phishing websites.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";

contract TestUri is Test {
    function testAddField() public {
        assertEq(
            uri("NFT", 'N", "image":"https://phishing'),
            '{"name":"NFT","symbol":"N", "image":"https://phishing"}'
        );
    }

    function testCorrupt() public {
        assertEq(
            uri("NFT", 'N"'),
            '{"name":"NFT","symbol":"N""}'
        );
    }

    function uri(string memory name, string memory symbol) internal view returns (string memory) {
        return
            string(
                abi.encodePacked(
                    '{"name":"',
                    name,
                    '","symbol":"',
                    symbol,
                    '"',
                    "}"
                )
            );
    }
}
```

## Recommendations

Filter blacklist character