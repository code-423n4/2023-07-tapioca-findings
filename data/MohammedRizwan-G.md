  ## Summary

### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G&#x2011;01] | Use nested-if and avoid "&&" to save gas | 1 |
| [G&#x2011;02] | Catch newOwner argument in local variable to save gas | 1 |
| [G&#x2011;03] | Refactor state variables to save gas | 1 |
| [G&#x2011;04] | Save gas with abi.encode and avoid possibility of hash collisions | 1 |
| [G&#x2011;05] | catch share argument as local variable to save gas in _addCollateral() and _removeCollateral() | 1 |
| [G&#x2011;06] | Avoid calling function arguments repeatedly in computeTVLInfo() | 1 |
| [G&#x2011;07] | In MarketERC20.sol, Avoid copying functions from imports instead directly use them | 2 |
| [G&#x2011;08] | Do not copy contracts from LayerZero repositories directly in project | contracts |








### [G&#x2011;01]  Use nested if and, avoid multiple check combinations
Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
It save 6 gas per instance, total of 6 gas can be saved.

There is [1 instance in BoringMath.sol](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/BoringMath.sol#L20-L29) of this issue:

```diff
File: contracts/BoringMath.sol

    function muldiv(
        uint256 value,
        uint256 mul,
        uint256 div,
        bool roundUp
    ) internal pure returns (uint256 result) {
        result = (value * mul) / div;
-       if (roundUp && (result * div) / mul < value) {
-           result++;
-       }
+        if (roundUp) {
+            if ((result * div) / mul < value) {
+                result++;
+        }
+        }
         }
```      
### [G&#x2011;02]  Catch newOwner argument in local variable to save gas
In NativeTokenFactory.sol, a significant amount of gas can be save by catching function arguments to local variables. This approach avoid the repetative reading from function arguments.

There is 1 instance of this issue:

```diff
File: contracts/NativeTokenFactory.sol

    function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {
+       address _newOwner = newOwner;
        if (direct) {
            // Checks
-           require(newOwner != address(0) || renounce, "NTF: zero address");
+           require(_newOwner != address(0) || renounce, "NTF: zero address");

            // Effects
            emit OwnershipTransferred(tokenId, owner[tokenId], newOwner);
-           owner[tokenId] = newOwner;
+           owner[tokenId] = _newOwner;
            pendingOwner[tokenId] = address(0);
        } else {
            // Effects
-           pendingOwner[tokenId] = newOwner;
+           pendingOwner[tokenId] = _newOwner;
        }
    }
```
### [G&#x2011;03]  Refactor state variables to save gas
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.

In SGLStorage.sol, the state variables can be re-factored to save gas. It will save 1 slot.

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L57-L64) instance of this issue:

### Recommended Mitigation Steps

```diff
File : contracts/markets/singularity/SGLStorage.sol

    uint256 public minimumTargetUtilization;
    uint256 public maximumTargetUtilization;
    uint256 public fullUtilizationMinusMax;

+   uint256 public interestElasticity;
    uint64 public minimumInterestPerSecond;
    uint64 public maximumInterestPerSecond;
-   uint256 public interestElasticity;
    uint64 public startingInterestPerSecond;
```

### [G&#x2011;04]  Save gas with abi.encode and avoid possibility of hash collisions
The use of abi.encodePacked may cause the function to consume a significant amount of gas, especially if the concatenated strings are long or there are many function calls within abi.encodePacked. If the resulting data exceeds the block gas limit, the function will fail.

In SGLStorage.sol, There are [2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L154-L181) instances of this issue:

### Recommended Mitigation Steps

```diff
File: contracts/markets/singularity/SGLStorage.sol

    function symbol() public view returns (string memory) {
        return
            string(
-                abi.encodePacked(
+                abi.encode(
                    "tm",
                    collateral.safeSymbol(),
                    "/",
                    asset.safeSymbol(),
                    "-",
                    oracle.symbol(oracleData)
                )
            );
    }

    /// @notice returns market's ERC20 name
    function name() external view returns (string memory) {
        return
            string(
-                abi.encodePacked(
+                abi.encode(
                    "Tapioca Singularity ",
                    collateral.safeName(),
                    "/",
                    asset.safeName(),
                    "-",
                    oracle.name(oracleData)
                )
            );
    }
```

### [G&#x2011;05]  catch share argument as local variable to save gas in _addCollateral() and _removeCollateral()
Catch share value. This will save good amount of gas per recommendation.

### Recommended Mitigation Steps

```diff

    function _addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) internal {
+       uint256 _share = share;
-        if (share == 0) {
+        if (_share == 0) {
-           share = yieldBox.toShare(collateralId, amount, false);
+           _share = yieldBox.toShare(collateralId, amount, false);
        }
-       userCollateralShare[to] += share;
+       userCollateralShare[to] += _share;
        uint256 oldTotalCollateralShare = totalCollateralShare;
-       totalCollateralShare = oldTotalCollateralShare + share;
+       totalCollateralShare = oldTotalCollateralShare + _share;
        _addTokens(
            from,
            to,
            collateralId,
-           share,
+           _share,
            oldTotalCollateralShare,
            skim
        );
        emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);
    }

    /// @dev Concrete implementation of `removeCollateral`.
    function _removeCollateral(
        address from,
        address to,
        uint256 share
    ) internal {
+       uint256 = _share;
-       userCollateralShare[from] -= share;
+       userCollateralShare[from] -= _share;
-       totalCollateralShare -= share;
+       totalCollateralShare -= _share;
        emit LogRemoveCollateral(from, to, share);              // emit event directly from argument to save gas
-       yieldBox.transfer(address(this), to, collateralId, share);
+       yieldBox.transfer(address(this), to, collateralId, _share);
-       if (share > _yieldBoxShares[from][COLLATERAL_SIG]) {
+       if (_share > _yieldBoxShares[from][COLLATERAL_SIG]) {
            _yieldBoxShares[from][COLLATERAL_SIG] = 0; //accrues in time
        } else {
-           _yieldBoxShares[from][COLLATERAL_SIG] -= share;
+           _yieldBoxShares[from][COLLATERAL_SIG] -= _share;
        }
    }
```

### [G&#x2011;06]  Avoid calling function arguments repeatedly in computeTVLInfo()
In computeTVLInfo(), catch user and _exchangeRate to save gas and repeatedly calling these arguments from function.

### Recommended Mitigation Steps

```diff

    function computeTVLInfo(
        address user,
        uint256 _exchangeRate
    )
        public
        view
        returns (uint256 amountToSolvency, uint256 minTVL, uint256 maxTVL)
    {
+       address _user = user;
+       uint256 __exchangeRate = _exchangeRate;
-       uint256 borrowPart = userBorrowPart[user];
+       uint256 borrowPart = userBorrowPart[_user];

        if (borrowPart == 0) return (0, 0, 0);

        Rebase memory _totalBorrow = totalBorrow;

        uint256 collateralAmountInAsset = _computeMaxBorrowableAmount(
-           user,
+           _user,
-           _exchangeRate
+           __exchangeRate
        );

        borrowPart = (borrowPart * _totalBorrow.elastic) / _totalBorrow.base;

        amountToSolvency = borrowPart >= collateralAmountInAsset
            ? borrowPart - collateralAmountInAsset
            : 0;

        (minTVL, maxTVL) = _computeMaxAndMinLTVInAsset(
-            userCollateralShare[user],
+            userCollateralShare[_user],
-            _exchangeRate
+            __exchangeRate
        );
    }
```

### [G&#x2011;07]  In MarketERC20.sol, Avoid copying functions from imports instead directly use them
In MarketERC20.sol, transfer() and transferFrom() is exactly copied from imports without any change. This increase extra bytes which ultimatly increase the deployment cost. These functions can be directly used like used in other functions.

There are [2](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135-L187) instances of this issue:

### Recommended Mitigation steps
Use functions directly from imports instead of copying again.

### [G&#x2011;08]  Do not copy contracts from LayerZero repositories directly in project
Use npm package for [solidity-examples](https://www.npmjs.com/package/@layerzerolabs/solidity-examples) and avoid copying the code as it unnecessarily adds extra bytes in contracts leading to more deployment cost. This issue is applicable to all Tapioca repositories.
