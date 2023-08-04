## L-01 Description
It is advisable to limit the length of the call to avoid wasting gas and getting reverted due to gas spent</description>

## POC
``` solidity
  /// @notice Allows batched call to BingBang.
  /// @param calls An array encoded call data.
  /// @param revertOnFail If True then reverts after a failed call and stops doing further calls.
  function execute(
    bytes[] calldata calls,
    bool revertOnFail
  ) external returns (bool[] memory successes, string[] memory results) {
    successes = new bool[](calls.length);
    results = new string[](calls.length);
    // @audit-issue L-01 It is advisable to limit the length of the call to avoid wasting gas and getting reverted due to gas spent
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
## Description
L-02 Remove unused function

## POC

``` solidity
<description>remove function if not in use to save deployment gas</description>
<snippet>
    if (shareOwed <= shareOut) {
      _repay(from, from, partOwed);
    } else {
      //repay as much as we can
      uint256 partOut = totalBorrow.toBase(amountOut, false);
      _repay(from, from, partOut);
    }
  }

  // @audit-issue L-02 remove function if not in use to save deployment gas
  function transfer(
    address to,
    uint256 amount
  ) public override returns (bool) {}

  function transferFrom(
    address from,
    address to,
    uint256 amount
  ) public override returns (bool) {}
```

