### 1. Approach taken in evaluating the codebase
- Focused on the chainlink oracles and the USDO contract
- Looked through the lending and borrowing contracts of USDO

### 2. Mechanism review
- Good to have different swappers to allow for unique token swaps (UniswapV2, UniswapV3, Curve) since it is very unlikely that a UniswapV2 router will have good liquidity sources for all tokens and will result in users experiencing forced losses to their reward token.

```
@analysis: only one router is not ideal, but the protocol have many different types of swappers.
->      _safeApprove(tokenIn, address(swapRouter), amountIn); 
        if (data.length == 0) {
            data = getDefaultDexOptions();
        }
        uint256 deadline = abi.decode(data, (uint256));
        uint256[] memory amounts = swapRouter.swapExactTokensForTokens(
            amountIn,
            amountOutMin,
            path,
            swapData.yieldBoxData.depositToYb ? address(this) : to,
            deadline
        );
```

- Transfer of ownership in BaseUSDO uses 2 step ownership if needed
- `execute()` in BigBang.sol can be quite dangerous especially if the code does not revert of failure. If intended, make sure that an event is emitted for every call to know specifically which call fails and which call succeeds. Some other contracts do not have such leeway (multicallValue() in Multicall3.sol), so if one call reverts, the whole function will revert

```
    function execute(
        bytes[] calldata calls,
->      bool revertOnFail
    ) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
            require(success || !revertOnFail, _getRevertMsg(result));
            successes[i] = success;
            results[i] = _getRevertMsg(result);
        }
    }
```


### 3. Centralization risks
- Centralization risks are kept to a limit. For example, contract makes sure that owner can set a limit to the flashMintFee so that flashloan works normally. 
```
    function setFlashMintFee(uint256 _val) external onlyOwner {
        require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");
        emit FlashMintFeeUpdated(flashMintFee, _val);
        flashMintFee = _val;
    }
```
- Liquidator fees are kept to a hardcoded value.

```
        minLiquidatorReward = 1e3;
        maxLiquidatorReward = 1e4;
        liquidationBonusAmount = 1e4;
        borrowOpeningFee = 50; // 0.05%
        liquidationMultiplier = 12000; //12%
```

- Some centralization risk such as setting borrow cap but it does not exploit the protocol if done maliciously because of a hardcoded limit.

```
    function setBorrowOpeningFee(uint256 _val) external onlyOwner {
        require(_val <= FEE_PRECISION, "Market: not valid");
        emit LogBorrowingFee(borrowOpeningFee, _val);
        borrowOpeningFee = _val;
    }


    /// @notice sets max borrowable amount
    /// @dev can only be called by the owner
    /// @param _cap the new value
    function setBorrowCap(uint256 _cap) external notPaused onlyOwner {
        emit LogBorrowCapUpdated(totalBorrowCap, _cap);
        totalBorrowCap = _cap;
    }
```

### 4. Codebase quality analysis

- Codebase is well written, and code copied from other protocols (such as Uniswap) is well integrated. 
- There is one thing to flag in UniswapV3, but I'm not sure if it's a problem. When calling consult() and getQuoteAtTick(), the tick rounds down because, in most use cases, the price being returned by an oracle is used to determine the value of an asset to be used for something like valuing collateral, where the caller is the one whose collateral is on the line, and it is crucial to ensure that user assets are not overvalued so as to give them an edge. 

```
        (int24 tick, ) = OracleLibrary.consult(pool, 60);
        amountIn = OracleLibrary.getQuoteAtTick(
            tick,
            uint128(amountOut),
            tokenOut,
            tokenIn
        );
    }
```

Because this is a swapper however, there was an issue flagged by someone regarding the need to round the tick up instead of down. Not sure if it's applicable here but will just flag.

https://github.com/sherlock-audit/2023-04-splits-judging/issues/12

### Time spent:
15 hours