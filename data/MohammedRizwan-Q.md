## Summary

### Low Risk Issues
|Number|Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | Calls inside loops that may address DoS | 1 |
| [L&#x2011;02] | batchMint() and batchBurn() can fail due to incorrect inputs | 1 |
| [L&#x2011;03] | Avoid setting immutable address variables to address(0) | 1 |
| [L&#x2011;04] | Avoid self transfer of tokens | 1 |
| [L&#x2011;05] | In NativeTokenFactory.sol, mint() function is missing minting limit | 1 |
| [L&#x2011;06] | Any funds directly sent to YieldBox.sol will be permanently lost | 1 |
| [L&#x2011;07] | Set limit for depositThreshold | 1 |
| [L&#x2011;08] | Avoid setting glpManager to zero address | 1 |
| [L&#x2011;09] | mint() does not verify the _expiry has passed | 1 |
| [L&#x2011;10] | In mint(), _discount limit is not set per documentation | 1 |
| [L&#x2011;11] | In TapiocaOptionBroker.sol, _epochDuration must be 7 days per documentation | 1 |
| [L&#x2011;12] | draft-ERC20Permit.sol is deprecated by openzeppelin | 3 |
| [L&#x2011;13] | unsafe downcast may lead to accounting errors | contracts |
| [L&#x2011;14] | Borrow cap limit is not set in setBorrowCap() | 1 |
| [L&#x2011;15] | Misleading comment in SGLLendingCommon.sol | 1 |
| [L&#x2011;16] | Misleading comment in SGLCommon.sol | 1 |
| [L&#x2011;17] | Use msg.sender instead of ```from``` inside function | 1 |
| [L&#x2011;18] | Incorrect comment in Market.sol | 1 |
| [L&#x2011;19] | Misleading comment in MarketERC20 contract _permit() | 1 |
| [L&#x2011;20] | Use openzeppelin version 4.9.3 | contracts |
| [L&#x2011;21] | Violation of CEI pattern leads to re-entrancy in _removeCollateral() | 1 |
| [L&#x2011;22] | Performing division first leads loss of precision in _getInterestRate() | 3 |
| [L&#x2011;23] | _callApproval() can be Dos if approval is large | 3 |
| [L&#x2011;24] | Use layer zero solidity-examples latest version 0.0.13 | contracts |
| [L&#x2011;25] | For bridging only between EVM chains use OFT and for bridging between EVM and non EVM chains use OFTV2 | contracts |
| [L&#x2011;26] | Loss of precision due to division first in _computeAssetAmountToSolvency() | 1 |
| [L&#x2011;27] | Avoid setting fee recipient to zero address | 1 |




### [L&#x2011;01]  Calls inside loops that may address DoS
In Multicall3.sol contract multicall() function and multicallValue() function,
Calls to external contracts inside a loop are dangerous because it could lead to DoS if one of the calls reverts or execution runs out of gas. Such issue also introduces chance of problems with the gas limits.

**Per SWC-113:**
External calls can fail accidentally or deliberately, which can cause a DoS condition in the contract. To minimize the damage caused by such failures, it is better to isolate each external call into its own transaction that can be initiated by the recipient of the call.

Reference link- https://swcregistry.io/docs/SWC-113

There are 2 instances of this issue:
[multicall()](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L51)

[multicallValue()](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L79)

### Recommended Mitigation Steps
1) Avoid combining multiple calls in a single transaction, especially when calls are executed as part of a loop
2) Always assume that external calls can fail
3) Implement the contract logic to handle failed calls

### [L&#x2011;02]  batchMint() and batchBurn() can fail due to incorrect inputs
In NativeTokenFactory.sol, batchMint() and batchBurn() have array inputs passed as arguments. For correct working of both functions, the array length must be checked. The array length where two arrays are passed must also be equal to avoid function revert.

There is [2 instance](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L127-L145) of this issue:

### Recommended Mitigation steps

```Solidity
File: contracts/NativeTokenFactory.sol

    function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner(tokenId) {
+       require(tos.length != 0, "invalid array length");
+       require(amounts.length != 0, "invalid array length");
+       require(tos.length == amounts.length, "invalid array length");
        uint256 len = tos.length;
        for (uint256 i = 0; i < len; i++) {
            _mint(tos[i], tokenId, amounts[i]);
        }
    }


    function batchBurn(uint256 tokenId, address[] calldata froms, uint256[] calldata amounts) public {
+       require(froms.length != 0, "invalid array length");
+       require(amounts.length != 0, "invalid array length");
+       require(froms.length == amounts.length, "invalid array length");
        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");
        uint256 len = froms.length;
        for (uint256 i = 0; i < len; i++) {
            _requireTransferAllowed(froms[i], isApprovedForAsset[froms[i]][msg.sender][tokenId]);
            _burn(froms[i], tokenId, amounts[i]);
        }
    }
```

### [L&#x2011;03]  Avoid setting immutable address variables to address(0)
In YieldBox.sol, wrappedNative and uriBuilder are immutable variables. These variables are set in constructor. If mistakenly they are set to zero address then it may cause redeployment of contract. Therefore prevention is better before setting the address variables in constructor.

There are 2 instances of this issue:

```Solidity
File: contracts/YieldBox.sol

91    constructor(IWrappedNative wrappedNative_, YieldBoxURIBuilder uriBuilder_) YieldBoxPermit("YieldBox") {
92        wrappedNative = wrappedNative_;
93        uriBuilder = uriBuilder_;
94    }
```

### Recommended Mitigation steps
```Solidity

    constructor(IWrappedNative wrappedNative_, YieldBoxURIBuilder uriBuilder_) YieldBoxPermit("YieldBox") {
+       require(address(wrappedNative_) != address(0), "invalid address");
+       require(address(uriBuilder_) != address(0), "invalid address");
        wrappedNative = wrappedNative_;
        uriBuilder = uriBuilder_;
    }
```

### [L&#x2011;04]  Avoid self transfer of tokens
In YieldBox.sol, there are some deposit and withdraw function where self transfer is possible which lead to accounting errors. It means from `from` to `to` tokens can be transferred.

There are [7](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L118-L493) instances of this issue:

### Recommended Mitigation steps
Add a require check restricting self transfer.

For example:

```Solidity
   require(from != to, "self transfer not allowed");
```

### [L&#x2011;05]  In NativeTokenFactory.sol, mint() function is missing minting limit
In NativeTokenFactory.sol, mint() function does not have minting limit which means unlimited tokens can be minted. It is recommended to add mint limit.

There is 1 instance of this issue:
```Solidity
File: contracts/NativeTokenFactory.sol

109    function mint(uint256 tokenId, address to, uint256 amount) public onlyOwner(tokenId) {
110        _mint(to, tokenId, amount);
111    }
```

### [L&#x2011;06]  Any funds directly sent to YieldBox.sol will be permanently lost
In YieldBox.sol, the comment says,
```Solidity
49    /// Any funds transfered directly onto the YieldBox will be lost, use the deposit function instead.
```
There is a possibility that the ethers can be sent to this contract and the user will permanent lost it. There is also no way to revert the incoming ether via some recieve() or fallback().

There is [1](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L49) instance of this issue:

### Recommended Mitigation steps

```Solidity
File: contracts/YieldBox.sol

+    receive() external payable{
+       revert();
+     }
```

### [L&#x2011;07]  Set limit for depositThreshold
In YearnStrategy.sol, setDepositThreshold() function can be accessed by owner. The owner can either set the depositThreshold as 0 or any amount. However if it is set too high then the condition for deposit to yearn might not be feasible and setting to 0 will also be problematic. Therefore it is recommended to set limit for depositThreshold in setDepositThreshold() function.

There is 1 instance of this issue:

```Solidity
File: contracts/yearn/YearnStrategy.sol

90    function setDepositThreshold(uint256 amount) external onlyOwner {
91        emit DepositThreshold(depositThreshold, amount);
92        depositThreshold = amount;
93    }
```
### [L&#x2011;08]  Avoid setting glpManager to zero address
In GLPOracle.sol, glpManager is passed in constructor, however there is no validation in constructor. Therefore to prevent it from setting zero address, a check is required.

There is [1](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/GLPOracle.sol#L10-L12) instance of this issue:

### Recommended Mitigation Steps

```Solidity
File: contracts/oracle/implementations/GLPOracle.sol

    constructor(IGmxGlpManager glpManager_) {
+       require(address(glpManager_) != address(0), "invalid address");
        glpManager = glpManager_;
    }
```


### [L&#x2011;09]  mint() does not verify the _expiry has passed
In oTAP.sol, The issue here is mint() doesn't verify that a expiration hasn't passed when mints an OTAP. This may cause unexpected failure when the _expiry can be set in past as it does not check whether the _expiry is in future or not. 

There is 1 instance of this issue:

```Solidity
File: contracts/options/oTAP.sol

    function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        TapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.tOLP = _tOLP;

        emit Mint(_to, tokenId, option);
    }
```

### Recommended Mitigtion steps

```Solidity
File: contracts/options/oTAP.sol

    function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
+       require(block.timestamp <= _expiry, "mint passed");
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        TapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.tOLP = _tOLP;

        emit Mint(_to, tokenId, option);
    }
```

### [L&#x2011;10]  In mint(), _discount limit is not set per documentation
In oTAP.sol mint(), _discount limit is not set which is mentioned in documentation, Refer below link,
Documentation link, [here](https://docs.tapioca.xyz/tapioca/token-economy/otap-dso)

There is 1 instance of this issue:

```Solidity
File: contracts/options/oTAP.sol

    function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        TapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.tOLP = _tOLP;

        emit Mint(_to, tokenId, option);
    }
```

### Recommended Mitigation steps
Check the documentation and add the validation check for _discount.

### [L&#x2011;11]  In TapiocaOptionBroker.sol, _epochDuration must be 7 days per documentation
In TapiocaOptionBroker.sol, EPOCH_DURATION is an immutable variables which is passed in constructor. However, as per the documentation, ```Each epoch is seven days or one week. Once the epoch has ended, any unredeemed oTAP is forfeit.``` Therefore, _epochDuration must be validated.

There is [1](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L78) instance of this issue:

### Recommended Mitigation Steps

```Solidity
File: contracts/options/TapiocaOptionBroker.sol

    constructor(
        address _tOLP,
        address _oTAP,
        address payable _tapOFT,
        address _paymentTokenBeneficiary,
        uint256 _epochDuration,
        address _owner
    ) {
+       require(_epochDuration >= 7 days, "invalid epoch duration");
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tOLP = TapiocaOptionLiquidityProvision(_tOLP);
        tapOFT = TapOFT(_tapOFT);
        oTAP = OTAP(_oTAP);
        EPOCH_DURATION = _epochDuration;
        owner = _owner;
    }
```

### [L&#x2011;12]  draft-ERC20Permit.sol is deprecated by openzeppelin
In LTap.sol, TapOFT.sol and BaseTapOFT.sol draft-ERC20Permit.sol is used in contract but Openzeppelin has deprecated the use of draft-ERC20Permit.sol in v4.9.0 release. Check out the deprecations [here](https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0).

There are [instance 1](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L5), [instance 2](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L4), [instance 3](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L5) and [instance 4](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L9) of this issue:

### Recommended Mitigation steps
Use [ERC20Permit.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol).

### [L&#x2011;13]  unsafe downcast may lead to accounting errors
In contracts, while down casting the data type of data value, It is directly processed without any safety. As seen in contract, such unsafe downcasting can lead to accounting errors which can seriously hamper the overall system. 

There are various instances in overall Tapioca contracts, however putting below instances,

```Solidity
104        _totalBorrow.elastic += uint128(extraAmount);

108        _accrueInfo.feesEarnedFraction += uint128(feeFraction);
109        _totalAsset.base = _totalAsset.base + uint128(feeFraction);


117            _accrueInfo.interestPerSecond = uint64(
118                (uint256(_accrueInfo.interestPerSecond) * interestElasticity) /
119                    scale
120            );

135            _accrueInfo.interestPerSecond = uint64(newInterestPerSecond);


238        _totalAsset.elastic -= uint128(share);
239        _totalAsset.base -= uint128(fraction);
```

### Recommended Mitigation steps
Use openzeppelin safeCast.sol for safe casting.

### [L&#x2011;14]  Borrow cap limit is not set in setBorrowCap()
In Market.sol, setBorrowCap() is used to cap the borrow which is with current implementation can be set to zero. The owner can also set it to high to any amount.

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L151-L154) instance of this issue:

```Solidity
File: contracts/markets/Market.sol

    function setBorrowCap(uint256 _cap) external notPaused onlyOwner {
        emit LogBorrowCapUpdated(totalBorrowCap, _cap);
        totalBorrowCap = _cap;
    }
```

### Recommended Mitigation Steps
Add the check for _cap while setting the setBorrowCap().

### [L&#x2011;15]  Incorrect comment in SGLLendingCommon.sol
```PRIVATE FUNCTIONS``` comment must be corrected in SGLLendingCommon.sol. It is misleading while the functions are actually internal.

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L13) instance of this issue:

### Recommended Mitigation Step

```diff
-    // *** PRIVATE FUNCTIONS *** //
+    // *** INTERNAL FUNCTIONS *** //
```

### [L&#x2011;16]  Misleading comment in SGLCommon.sol
```PRIVATE FUNCTIONS``` comment must be corrected in SGLCommon.sol. It is misleading while the functions are actually internal.

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L33) instance of this issue:

### Recommended Mitigation Step

```diff
-    // *** PRIVATE FUNCTIONS *** //
+    // *** INTERNAL FUNCTIONS *** //
```

### [L&#x2011;17]  Use msg.sender instead of ```from``` inside function
In SGLCommon.sol, _addTokens() and _addAsset() _removeAsset() is using the ```from``` field as function argument. It is allowing anyone to add the tokens.asset directly from ```from``` which should be msg.sender inside the function. Whatever the accounting happens should be deducted or added to msg.sender instead of ```from```.

Check [this](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L174-L251) instance in SGLCommon.sol. This has also been used in other contracts too. This issue should also be seen in other contracts and should be corrected.

### Recommended Mitigation Steps
Use msg.sender instead of ```from``` inside the function while accounting.

### [L&#x2011;18]  Incorrect comment in Market.sol
Misleading comment should be corrected. The instance can be seen [here](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L82)

### Recommended Mitigation Steps

```diff
-    uint256 internal EXCHANGE_RATE_PRECISION; //not costant, but can only be set in the 'init' method
+    uint256 internal EXCHANGE_RATE_PRECISION; //not constant, but can only be set in the 'init' method
```

### [L&#x2011;19]  Misleading comment in MarketERC20 contract _permit()
In MarketERC20.sol, _permit() has used bool data type for asset classification like ```true``` for asset and ```false``` for collateral, however the comment is misleading as below,

```Solidity
252        bool asset, // 1 = asset, 0 = collateral
```

### Recommended Mitigation Steps

```diff
-        bool asset, // 1 = asset, 0 = collateral
+        bool asset, // true = asset, false = collateral
```

### [L&#x2011;20]  Use openzeppelin version 4.9.3
The contracts has used old version like 4.8.2 which is outdated. While importing external libraries to contracts, security is paramount important therefore using outdated external library, the risk can not be taken. It is recommended to update the openzeppelin version [4.9.3](https://github.com/OpenZeppelin/openzeppelin-contracts/releases).

### [L&#x2011;21]  Violation of CEI pattern leads to re-entrancy in _removeCollateral()
In SGLLendingCommon.sol, _removeCollateral() function has violated Checks, Effects, Interactions pattern. It is recommended to follow the CEI pattern to avoid the reentrancy opportunity to attackers.

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L41-L55) instance of this issue:

### Recommended Mitigation Steps
Perform external call at last and follow CEI pattern.

### [L&#x2011;22]  Performing division first leads loss of precision in _getInterestRate()
In SGLCommon.sol, There is a loss of precision due to division first and multiply later in _getInterestRate().
Such precision can cause losses and accounting errors. It is always recommended to perform division at last and multiply first.

There are [3](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L106-L128) instance of this issue:

Instance 1:
```Solidity
106        uint256 feeAmount = (extraAmount * protocolFee) / FEE_PRECISION; // % of interest paid goes to fee
107        feeFraction = (feeAmount * _totalAsset.base) / fullAssetAmount;
```
Instance 2:
```Solidity
113            uint256 underFactor = ((minimumTargetUtilization - utilization) *
114                FACTOR_PRECISION) / minimumTargetUtilization;
115            uint256 scale = interestElasticity +
116                (underFactor * underFactor * elapsedTime);
```
Instance 3:
```Solidity
            uint256 overFactor = ((utilization - maximumTargetUtilization) *
                FACTOR_PRECISION) / fullUtilizationMinusMax;
            uint256 scale = interestElasticity +
                (overFactor * overFactor * elapsedTime);
```

Lets take the instance 1, similar approach can be taken for other instances
```(x * y) / z * a * b / c```
As seen division is happening first leading to loss of precision, similar thing is happening in other instances.

### Recommended Mitigation Steps
Follow multiplication first and division at last.

### [L&#x2011;23]  _callApproval() can be Dos if approval is large
_callApproval() is used to fetch the approvals. It is used with dynamic array with unknown number of approvals. If the number of approvals is too large the due to block gas limit the function can be in deniel of service.

There is 1 instance of this issue:

```Solidity
File: contracts/usd0/modules/USDOMarketModule.sol

    function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {

     // Some code

    }
```

### Recommended Mitigation Steps
Set some approvals limit so that it can not be DOS.

### [L&#x2011;24]  Use layer zero solidity-examples latest version 0.0.13
layer-zero is actively updating the solidity-examples repo for its use. It is recommended to update the latest version of 0.0.13 solidity-examples. This adds new optimizations and security. Adding external contracts security is utmost important so updating is recommended.

### [L&#x2011;25]  For bridging only between EVM chains use OFT and for bridging between EVM and non EVM chains use OFTV2
For contracts using layer zero code specifically use of OFT and This is an important consideration while using the layer zero. This can be checked [here](https://layerzero.gitbook.io/docs/evm-guides/layerzero-integration-checklist)

### [L&#x2011;26]  Loss of precision due to division first in _computeAssetAmountToSolvency()
Solidity does not support float numbers so it will always truncate the values. Thats the reason it is always recommened to follow multiplication first and division last. 

In SGLLiquidation.sol, _computeAssetAmountToSolvency() function, Division is happening first which will cause precision loss which can be seen below,

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L81C1-L87C27) instance of this issue:

```Solidity

        uint256 collateralAmountInAsset = yieldBox.toAmount(
            collateralId,
            (collateralShare *
>>                (EXCHANGE_RATE_PRECISION / FEE_PRECISION) *
                lqCollateralizationRate),
            false
        ) / _exchangeRate;
```

### Recommended Mitigation Steps
Follow multiplication first and division last in function.

### [L&#x2011;27]  Avoid setting fee recipient to zero address
In Penrose.sol, Avoid Avoid setting fee recipient to zero address in setFeeTo().

There is [1](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L455-L457) instance of this issue:

### Recommended Mitigation Steps

```Solidity
    function setFeeTo(address feeTo_) external onlyOwner {
+       require(feeTo_ != address(0), "invalid address");
        feeTo = feeTo_;
        emit FeeToUpdate(feeTo_);
    }
```
