| Number | Optimization Details                                                                                                                                                   | Instances |
| :----: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-01] | Use assembly to check for the zero address                                                                                                                             |     33     |
| [G-02] | Do not calculate constant variables                                                                                                                                    |     3     |
| [G-03] | Cache state variables with stack variables                                                                                                                             |     19     |
| [G-04] | x += y and x -= y are more expensive than x = x + y and x = x-y for state variables                                                                                    |     22     |
| [G-05] | Using ternary operator instead of if else saves gas                                                                                                                    |     10     |
| [G-06] | Use unchecked{} whenever underflow/overflow not possible saves 130 gas each time                                                                                       |     9     |
| [G-07] | The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement |     4     |
| [G-08] | ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops                         |     1     |

Total 8 issues

## [G-01] Use assembly to check for the zero address

Using assembly for address comparisons in Solidity can save gas because it allows for more direct access to the Ethereum Virtual Machine (EVM), reducing the overhead of higher-level operations. Solidity's high-level abstraction simplifies coding but can introduce additional gas costs. Using assembly for simple operations like address comparisons can be more gas-efficient.

_Total 33 instances - 19 file:_

### Instance#1:

```solidity
File: contracts/markets/singularity/SGLLiquidation.sol
40:if (address(liquidationQueue) != address(0)) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L40

### Instance#2:

```solidity
File: contracts/usd0/BaseUSDO.sol
106: require(_conservator != address(0), "USDO: address not valid");
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L106

### Instance#3:

```solidity
File: contracts/usd0/BaseUSDO.sol
354:if (module == address(0)) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L354

### Instance#4-6:

```solidity
File:contracts/Penrose.sol
242:require(address(swappers_[0]) != address(0), "Penrose: zero address");

243:require(address(markets_[0]) != address(0), "Penrose: zero address");

282:require(_conservator != address(0), "Penrose: address not valid");
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L242
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L243
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L282

### Instance#7-13:

```solidity
File:contracts/markets/singularity/Singularity.sol
101:            address(_collateral) != address(0) &&

102:                address(_asset) != address(0) &&

103:                address(_oracle) != address(0),

581:    if (address(_liquidationQueue) != address(0)) {

586:    if (_bidExecutionSwapper != address(0)) {

591:    if (_usdoSwapper != address(0)) {

611:    if (module == address(0)) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L101
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L102
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L103
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L581
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L586
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L591
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L611

### Instance#14-15:

```solidity
File:contracts/markets/Market.sol
177: if (address(_oracle) != address(0)) {

187: if (_conservator != address(0)) {
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L177
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L187C8-L187C42

### Instance#16:

```solidity
File: contracts/tOFT/TapiocaOFT.sol
74:if (erc20 == address(0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/TapiocaOFT.sol#L74

### Instance#17:

```solidity
File: contracts/tOFT/mTapiocaOFT.sol
94:if (erc20 == address(0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L94C9-L94C35

### Instance#18:

```solidity
File: contracts/TapiocaWrapper.sol
160:if (address(tapiocaOFTsByErc20[_erc20]) != address(0x0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L160

### Instance#19-23:

```solidity
File: contracts/Balancer.sol
112:if (connectedOFTs[_srcOft][_dstChainId].dstOft == address(0))

127:if (_router == address(0)) revert RouterNotValid();

128:if (_routerETH == address(0)) revert RouterNotValid();

142: if (ITapiocaOFT(_srcOft).erc20() == address(0)) {

225: bool isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L112
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L127
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L128
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L142
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L225

### Instance#24:

```solidity
File: contracts/tOFT/modules/BaseTOFTLeverageModule.sol
272:if (erc20 == address(0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272C6-L272C6

### Instance#25:

```solidity
File: contracts/tOFT/BaseTOFT.sol
371:if (erc20 == address(0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L371C9-L371C35

### Instance#26:

```solidity
File: contracts/tOFT/BaseTOFT.sol
396: if (module == address(0)) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L396

### Instance#27:

```solidity
File: contracts/options/oTAP.sol
127: require(broker == address(0), "OTAP: only once");
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L127

### Instance#28:

```solidity
File: contracts/option-airdrop/aoTAP.sol
140:require(broker == address(0), "AOTAP: only once");
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/aoTAP.sol#L140

### Instance#29:

```solidity
File: contracts/Vesting.sol
132:if (_user == address(0)) revert AddressNotValid();
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L132

### Instance#30-31:

```solidity
File: contracts/tokens/TapOFT.sol
118: require(_lzEndpoint != address(0), "LZ endpoint not valid");

161:require(_minter != address(0), "address not valid");
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L118
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L161

### Instance#32:

```solidity
File: contracts/option-airdrop/AirdropBroker.sol
369: paymentTokenBeneficiary != address(0),
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L369

### Instance#33:

```solidity
File: contracts/options/TapiocaOptionBroker.sol
483: paymentTokenBeneficiary != address(0),
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L483

## [G-02] Do not calculate constant variables

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas each time of use.

_Total 3 instances - 2 file:_

### Instance#1:

```solidity
File: contracts/usd0/BaseUSDOStorage.sol
38:    bytes32 internal constant FLASH_MINT_CALLBACK_SUCCESS =
39:        keccak256("ERC3156FlashBorrower.onFlashLoan");

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol#L38C1-L39C55

### Instance#2-3:

Findings are marked as <=FOUND

```solidity
File: contracts/markets/MarketERC20.sol
29:bytes32 private constant _PERMIT_TYPEHASH =
30:        keccak256(
31:            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
32:        );//<=FOUND
33: bytes32 private constant _PERMIT_TYPEHASH_BORROW =
34:        keccak256(
35:            "PermitBorrow(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
36:        );//<=FOUND

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L29C4-L36C11

## [G-03] Cache state variables with stack variables

Caching of a state variable replaces each Gwarmaccess (100 gas) with a cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_Total 19 instances - 6 file:_

The following instances are not covered in automated bot report

### Instance#1-4:

```solidity
File: contracts/markets/singularity/SGLLendingCommon.sol
//@audit collateralId at line24
32:  collateralId,

//@audit _yieldBoxShares[from][COLLATERAL_SIG] at line 51 or 53
50: if (share > _yieldBoxShares[from][COLLATERAL_SIG]) {

//@audit assetId at line 73
79: yieldBox.transfer(address(this), to, assetId, share);

//@audit assetId at line 93
95: _addTokens(from, to, assetId, share, uint256(totalShare), skim);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L32C12-L32C26
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L50C9-L50C61
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L79
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L95

### Instance#5-6:

```solidity
File: contracts/markets/singularity/SGLLeverage.sol
//@audit assetId at line110
129: uint256 shareOwed = yieldBox.toShare(assetId, amountOwed, true);

//@audit assetId at line158,160
167: assetId,
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L129
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L167

### Instance#7-8:

```solidity
File: contracts/markets/MarketERC20.sol
//@audit allowance[from][msg.sender] at line 77
80:allowance[from][msg.sender] -= share;

//@audit allowance[from][msg.sender] at line 86
89:allowance[from][msg.sender] -= share;

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L129
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L89C11-L89C56

### Instance#9-16:

```solidity
File: contracts/markets/singularity/SGLLiquidation.sol
//@audit assetId at line 160,179
193: yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

//@audit collateralId at line 129,169
175: yieldBox.toAmount(collateralId, allCollateralShare, true),

//@audit collateralId at line 217
256: collateralId,

//@audit userCollateralShare[user] at line 260
261:collateralShare = userCollateralShare[user];

//@audit userBorrowPart[user] at line 244,245
248:userBorrowPart[user] = userBorrowPart[user] - borrowPart;

//@audit assetId at line 275,281
282:yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

//@audit collateralId at line 333
343:collateralId,

//@audit assetId at line 324
344: assetId,
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L193
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L175
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L256
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L261
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L248
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L282
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L343
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L344

### Instance#17:

```solidity
File: contracts/tokens/TapOFT.sol
//@audit mintedInWeek[week - 1] at line 204
207: uint256 unclaimed = emissionForWeek[week - 1] - mintedInWeek[week - 1];
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L207

### Instance#18-19:

```solidity
File: contracts/governance/twTAP.sol
//@audit address(tapOFT) at line 365
366:claimRewardsOn(_tokenId, address(tapOFT), _rewardTokens);

//@audit address(tapOFT) at line 390
391:return _releaseTap(_tokenId, address(tapOFT));
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L366C1-L366C67
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L391

## [G-04] x += y and x -= y are more expensive than x = x + y and x = x-y for state variables

Using the addition/subtraction operator instead of plus-equals or minus-equals saves 113 gas.

_Total 22 instances - 7 file:_

The following instances are not covered in automated bot report

### Instance#1-6:

```solidity
File: contracts/markets/singularity/SGLLendingCommon.sol
26: userCollateralShare[to] += share;

46: userCollateralShare[from] -= share;

47:    totalCollateralShare -= share;

53: _yieldBoxShares[from][COLLATERAL_SIG] -= share;

70: userBorrowPart[from] += part;

91: userBorrowPart[to] -= part;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L26C8-L26C42
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L46
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L47
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L53
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L70
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L91

### Instance#7-8:

```solidity
File: contracts/markets/MarketERC20.sol
80:allowance[from][msg.sender] -= share;

89:allowanceBorrow[from][msg.sender] -= share;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L80C13-L80C50
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L89C13-L89C56

### Instance#9-15:

```solidity
File: contracts/markets/singularity/SGLLiquidation.sol
133: userCollateralShare[user] -= collateralShare;

157:totalCollateralShare -= allCollateralShare;

195:totalAsset.elastic += uint128(returnedShare - callerShare);

263: userCollateralShare[user] -= collateralShare;

266: totalBorrow.elastic -= uint128(borrowAmount);

267:totalBorrow.base -= uint128(borrowPart);

284:totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L133
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L157
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L195
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L263
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L266
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L267
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L284

### Instance#16-17:

```solidity
File: contracts/Balancer.sol
210:  connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;

255:connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L210
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L255

### Instance#18:

```solidity
File: contracts/Vesting.sol
116: users[msg.sender].claimed += _claimable;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L116

### Instance#19:

```solidity
File:contracts/tokens/TapOFT.sol
227:mintedInWeek[week] += _amount;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L227

### Instance#20-22:

```solidity
File:contracts/governance/twTAP.sol
343:        weekTotals[w0 + 1].netActiveVotes += int256(votes);

344:        weekTotals[w1 + 1].netActiveVotes -= int256(votes);

491:  claimed[_tokenId][i] += amount;

```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L343
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L344
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L491

## [G-05] Using ternary operator instead of if else saves gas

_Total 10 instances - 9 file:_

### Instance#1:

```solidity
File: contracts/markets/singularity/SGLLendingCommon.sol
50: if (share > _yieldBoxShares[from][COLLATERAL_SIG]) {
51:            _yieldBoxShares[from][COLLATERAL_SIG] = 0; //accrues in time
52:        } else {
53:            _yieldBoxShares[from][COLLATERAL_SIG] -= share;
54:        }
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L50C8-L54C10

### Instance#2:

```solidity
File: contracts/markets/MarketERC20.sol
279:if (asset) {
280:            _approve(owner, spender, value);
281:        } else {
282:            _approveBorrow(owner, spender, value);
283:        }
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L279C9-L283C10

### Instance#3:

```solidity
File: contracts/tOFT/TapiocaOFT.sol
74:if (erc20 == address(0)) {
75:            _wrapNative(_toAddress);
76:        } else {
77:            _wrap(_fromAddress, _toAddress, _amount);
78:        }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/TapiocaOFT.sol#L74C9-L78C10

### Instance#4:

```solidity
File:contracts/tOFT/mTapiocaOFT.sol
94: if (erc20 == address(0)) {
95:            _wrapNative(_toAddress);
96:        } else {
97:            _wrap(_fromAddress, _toAddress, _amount);
98:        }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L94C8-L98C10

### Instance#5:

```solidity
File:contracts/tOFT/mTapiocaOFT.sol
145: if (_isNative) {
146:            _safeTransferETH(msg.sender, _amount);
147:        } else {
148:            IERC20(erc20).safeTransfer(msg.sender, _amount);
149:        }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L145C9-L149C10

### Instance#6:

```solidity
File:contracts/tOFT/modules/BaseTOFTLeverageModule.sol
272:if (erc20 == address(0)) {
273:            _safeTransferETH(_toAddress, _amount);
274:        } else {
275:            IERC20(erc20).safeTransfer(_toAddress, _amount);
276:        }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L272C9-L276C10

### Instance#7:

```solidity
File:contracts/tOFT/BaseTOFT.sol
371:if (erc20 == address(0)) {
372:            _safeTransferETH(_toAddress, _amount);
373:        } else {
374:            IERC20(erc20).safeTransfer(_toAddress, _amount);
375:        }
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L371C9-L375C10

### Instance#8:

```solidity
File:contracts/governance/twTAP.sol
544: if (pool.cumulative > position.averageMagnitude) {
545:                    pool.cumulative -= position.averageMagnitude;
546:                } else {
547:                    pool.cumulative = 0;
548:                }
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L544C17-L548C18

### Instance#9-10:

Findings are marked as <=FOUND

```solidity
File:contracts/options/TapiocaOptionBroker.sol
253: if (pool.cumulative > pool.averageMagnitude) {
254:                    pool.cumulative -= pool.averageMagnitude;
255:                } else {
256:                    pool.cumulative = 0;
257:                }//<FOUND

315: if (pool.cumulative > pool.averageMagnitude) {
316:                    pool.cumulative -= pool.averageMagnitude;
317:                } else {
318:                    pool.cumulative = 0;
319:                }//<FOUND
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L253C17-L257C18
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L315C17-L319C18

## [G-06] Use unchecked{} whenever underflow/overflow not possible saves 130 gas each time

Because of above check, it can be decided that underflow/overflow not possible.

_Total 9 instances - 6 file:_

The following instances are not covered in automated bot report

### Instance#1:

```solidity
File: contracts/markets/singularity/SGLLendingCommon.sol
//@audit can be unchecked due to line 50
53: _yieldBoxShares[from][COLLATERAL_SIG] -= share;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol#L53

### Instance#2-3:

```solidity
File: contracts/markets/MarketERC20.sol
//@audit can be unchecked due to line 77
80:allowance[from][msg.sender] -= share;

//@audit can be unchecked due to line 86
89:  allowanceBorrow[from][msg.sender] -= share;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L80C13-L80C50
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L89C13-L89C56

### Instance#4-5:

```solidity
File:contracts/markets/singularity/SGLLiquidation.sol
//@audit can be unchecked due to line 260
263: userCollateralShare[user] -= collateralShare;

//@audit can be unchecked due to line 384
387:  liquidatedCount++;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L263
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L387

### Instance#6:

```solidity
File:contracts/governance/twTAP.sol
//@audit can be unchecked due to line 544
545: pool.cumulative -= position.averageMagnitude;
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol#L545C21-L545C21

### Instance#7-8:

```solidity
File:contracts/options/TapiocaOptionBroker.sol
//@audit can be unchecked due to line 253
254: pool.cumulative -= pool.averageMagnitude;

//@audit can be unchecked due to line 315
316:pool.cumulative -= pool.averageMagnitude;
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L254C20-L254C62
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L316

### Instance#9:

```solidity
File:contracts/twAML.sol
//@audit can be unchecked due to line 148
150: uint256 x = y / 2 + 1;
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/twAML.sol#L150



## [G-07] The use of a logical AND in place of double if is slightly less gas efficient in instances where there isn't a corresponding else statement for the given if statement

Using a double if statement instead of logical AND (&&) can provide similar short-circuiting behavior whereas double if is slightly more efficient.

_Total 4 instances - 4 file:_

### Instance#1:

```solidity
File: contracts/TapiocaWrapper.sol
144:if (_call[i].revertOnFailure && !success) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol#L144

### Instance#2:

```solidity
File: contracts/Balancer.sol
226:if (!isNative && _ercData.length == 0) revert PoolInfoRequired();
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L226

### Instance#3:

```solidity
File: contracts/tOFT/BaseTOFT.sol
412:if (!success && !_forwardRevert) {
```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L412C9-L412C43

### Instance#4:

```solidity
File:contracts/options/TapiocaOptionLiquidityProvision.sol
314: } else if (
315:                    _singularities[i] == sglAssetID && i < sglLastIndex
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L314C17-L315C72

## [G-08] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

_Total 1 instances - 1 file:_

The following instance is not covered in automated bot report

### Instance#1:

```solidity
File: contracts/options/TapiocaOptionLiquidityProvision.sol
308:for (uint256 i = 0; i < sglLength; i++) {
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L308
