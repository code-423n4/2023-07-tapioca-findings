# Report


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Constants in comparisons should appear on the left side | 22 |
| [NC-2](#NC-2) | delete keyword can be used instead of setting to 0 | 1 |
| [NC-3](#NC-3) | Lines are too long | 1 |
| [NC-4](#NC-4) | Return values of `approve()` not checked | 4 |
| [NC-5](#NC-5) | Constants should be defined rather than using magic numbers | 3 |
### <a name="NC-1"></a>[NC-1] Constants in comparisons should appear on the left side
Constants should appear on the left side of comparisons, to avoid accidental assignment

*Instances (22)*:
```solidity
File: Penrose.sol

509:         if (feeShares == 0) return;

```

```solidity
File: markets/Market.sol

314:         if (borrowPart == 0) return (0, 0, 0);

408:         if (borrowPart == 0) return true;

410:         if (collateralShare == 0) return false;

447:         if (borrowed == 0) return 0;

448:         if (startTVLInAsset == 0) return 0;

485:         if (numerator == 0 || denominator == 0) {

485:         if (numerator == 0 || denominator == 0) {

```

```solidity
File: markets/bigBang/BigBang.sol

182:         if (totalBorrow.elastic == 0) return minDebtRate;

516:         if (elapsedTime == 0) {

550:         if (share == 0) {

751:             totalBorrowCap == 0 || totalBorrow.elastic <= totalBorrowCap,

```

```solidity
File: markets/singularity/SGLBorrow.sol

26:         if (amount == 0) return (0, 0);

```

```solidity
File: markets/singularity/SGLCommon.sol

61:         utilization = fullAssetAmount == 0

68:         if (elapsedTime == 0) {

81:         if (_totalBorrow.base == 0) {

207:         fraction = allShare == 0

229:         if (totalAsset.base == 0) {

```

```solidity
File: markets/singularity/SGLLendingCommon.sol

23:         if (share == 0) {

67:             totalBorrowCap == 0 || totalBorrow.base <= totalBorrowCap,

```

```solidity
File: markets/singularity/SGLLiquidation.sol

76:         if (borrowPart == 0) return 0;

115:                 if (borrowAmount == 0) {

```

### <a name="NC-2"></a>[NC-2] delete keyword can be used instead of setting to 0
It's clearer and reflects the intention of the programmer

*Instances (1)*:
```solidity
File: markets/singularity/Singularity.sol

467:         accrueInfo.feesEarnedFraction = 0;

```

### <a name="NC-3"></a>[NC-3] Lines are too long
Recommended by solidity docs to keep lines to 120 characters or lesser

*Instances (1)*:
```solidity
File: markets/singularity/SGLStorage.sol

44:     Rebase public totalAsset; // elastic = yieldBox shares held by the Singularity, base = Total fractions held by asset suppliers

```

### <a name="NC-4"></a>[NC-4] Return values of `approve()` not checked
Not all IERC20 implementations `revert()` when there's a failure in `approve()`. The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*Instances (4)*:
```solidity
File: markets/MarketERC20.sol

197:         _approve(msg.sender, spender, amount);

280:             _approve(owner, spender, value);

```

```solidity
File: markets/bigBang/BigBang.sol

450:             asset.approve(address(yieldBox), totalFees);

```

```solidity
File: usd0/USDO.sol

101:         _approve(address(receiver), address(this), _allowance - (amount + fee));

```

### <a name="NC-5"></a>[NC-5] Constants should be defined rather than using magic numbers

*Instances (3)*:
```solidity
File: markets/Market.sol

268:             borrowPartScaled = borrowPart * (10 ** (18 - borrowPartDecimals));

280:                 (10 ** (18 - collateralPartDecimals));

293:             (10 ** ratesPrecision)) * (10 ** (18 - ratesPrecision));

```


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Divide before Multiplication | 3 |
| [L-2](#L-2) | Empty Function Body - Consider commenting why | 22 |
| [L-3](#L-3) | Initializers could be front-run | 2 |
| [L-4](#L-4) | ERC20 tokens that do not implement optional decimals method cannot be used | 2 |
| [L-5](#L-5) | _safeMint() should be used rather than _mint() | 2 |
| [L-6](#L-6) | Unsafe ERC20 operation(s) | 21 |
### <a name="L-1"></a>[L-1] Divide before Multiplication
Unnecessary loss of precision caused by divide before multiplication

*Instances (3)*:
```solidity
File: markets/Market.sol

265:             borrowPartScaled = borrowPart / (10 ** (borrowPartDecimals - 18));

284:             collateralizationRate) / (10 ** ratesPrecision);

477:         return (shareRatio * userCollateralShare[user]) / (10 ** assetDecimals);

```

### <a name="L-2"></a>[L-2] Empty Function Body - Consider commenting why

*Instances (22)*:
```solidity
File: markets/MarketERC20.sol

109:     constructor(string memory name) EIP712(name, "1") {}

114:     function totalSupply() public view virtual override returns (uint256) {}

```

```solidity
File: markets/bigBang/BigBang.sol

98:     constructor() MarketERC20("Tapioca BigBang") {}

429:     ) public override returns (bool) {}

435:     ) public override returns (bool) {}

```

```solidity
File: markets/singularity/SGLStorage.sol

148:     constructor() MarketERC20("Tapioca Singularity") {}

195:     function _accrue() internal virtual override {}

```

```solidity
File: markets/singularity/Singularity.sol

644:     receive() external payable {}

```

```solidity
File: usd0/BaseUSDOStorage.sol

68:     receive() external payable {}

```

```solidity
File: usd0/USDO.sol

51:     {}

```

```solidity
File: usd0/modules/USDOLeverageModule.sol

25:     ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

267:                 {} catch Error(string memory reason) {

282:                 {} catch Error(string memory reason) {

298:                 {} catch Error(string memory reason) {

```

```solidity
File: usd0/modules/USDOMarketModule.sol

28:     ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

256:                 {} catch Error(string memory reason) {

271:                 {} catch Error(string memory reason) {

287:                 {} catch Error(string memory reason) {

```

```solidity
File: usd0/modules/USDOOptionsModule.sol

22:     ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

257:                 {} catch Error(string memory reason) {

272:                 {} catch Error(string memory reason) {

288:                 {} catch Error(string memory reason) {

```

### <a name="L-3"></a>[L-3] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (2)*:
```solidity
File: markets/bigBang/BigBang.sol

101:     function init(bytes calldata data) external onlyOnce {

```

```solidity
File: markets/singularity/Singularity.sol

62:     function init(bytes calldata data) external onlyOnce {

```

### <a name="L-4"></a>[L-4] ERC20 tokens that do not implement optional decimals method cannot be used
Underlying token that does not implement optional decimals method cannot be used 
 
 > [EIP-20](https://eips.ethereum.org/EIPS/eip-20#decimals) OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

*Instances (2)*:
```solidity
File: markets/singularity/SGLStorage.sol

184:     function decimals() external view returns (uint8) {

```

```solidity
File: usd0/BaseUSDO.sol

143:     function decimals() public pure override returns (uint8) {

```

### <a name="L-5"></a>[L-5] _safeMint() should be used rather than _mint()
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

*Instances (2)*:
```solidity
File: usd0/USDO.sol

91:         _mint(address(receiver), amount);

111:         _mint(_to, _amount);

```

### <a name="L-6"></a>[L-6] Unsafe ERC20 operation(s)

*Instances (21)*:
```solidity
File: Penrose.sol

514:             yieldBox.transfer(

536:             yieldBox.transfer(address(this), feeTo, assetId, feeShares);

```

```solidity
File: markets/bigBang/BigBang.sol

349:             yieldBox.transfer(from, address(swapper), assetId, supplyShare);

450:             asset.approve(address(yieldBox), totalFees);

596:         yieldBox.transfer(

648:         yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);

649:         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

704:             yieldBox.transfer(from, address(this), _tokenId, share);

717:         yieldBox.transfer(address(this), to, collateralId, share);

761:         asset.approve(address(yieldBox), amount);

```

```solidity
File: markets/singularity/SGLCommon.sol

192:             yieldBox.transfer(from, address(this), _assetId, share);

243:         yieldBox.transfer(address(this), to, assetId, share);

```

```solidity
File: markets/singularity/SGLLendingCommon.sol

49:         yieldBox.transfer(address(this), to, collateralId, share);

79:         yieldBox.transfer(address(this), to, assetId, share);

```

```solidity
File: markets/singularity/SGLLeverage.sol

160:             yieldBox.transfer(from, address(swapper), assetId, supplyShare);

```

```solidity
File: markets/singularity/SGLLiquidation.sol

166:         yieldBox.transfer(

193:         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

281:         yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);

282:         yieldBox.transfer(address(this), msg.sender, assetId, callerShare);

330:         yieldBox.transfer(

```

```solidity
File: usd0/modules/USDOLeverageModule.sol

217:         IERC20(swapData.tokenOut).approve(externalData.tOft, amountOut);

```


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Centralization Risk for trusted owners | 27 |
### <a name="M-1"></a>[M-1] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (27)*:
```solidity
File: Penrose.sol

32: contract Penrose is BoringOwnable, BoringFactory {

256:     function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner {

263:     function setBigBangEthMarket(address _market) external onlyOwner {

281:     function setConservator(address _conservator) external onlyOwner {

291:     function setUsdoToken(address _usdoToken) external onlyOwner {

320:     ) external onlyOwner {

342:     ) external onlyOwner {

384:     ) external onlyOwner registeredSingularityMasterContract(mc) {

417:     ) external onlyOwner registeredBigBangMasterContract(mc) {

455:     function setFeeTo(address feeTo_) external onlyOwner {

464:     function setSwapper(ISwapper swapper, bool enable) external onlyOwner {

```

```solidity
File: markets/Market.sol

14: abstract contract Market is MarketERC20, BoringOwnable {

142:     function setBorrowOpeningFee(uint256 _val) external onlyOwner {

151:     function setBorrowCap(uint256 _cap) external notPaused onlyOwner {

170:     ) external onlyOwner {

```

```solidity
File: markets/bigBang/BigBang.sol

39: contract BigBang is BoringOwnable, Market {

444:     ) external onlyOwner notPaused returns (uint256 feeShares) {

471:     ) external onlyOwner {

```

```solidity
File: markets/singularity/SGLStorage.sol

34: contract SGLStorage is BoringOwnable, Market {

```

```solidity
File: markets/singularity/Singularity.sol

479:     ) external onlyOwner notPaused returns (uint256 feeShares) {

497:     ) external onlyOwner {

580:     ) external onlyOwner {

```

```solidity
File: usd0/BaseUSDO.sol

88:     function setMaxFlashMintable(uint256 _val) external onlyOwner {

96:     function setFlashMintFee(uint256 _val) external onlyOwner {

105:     function setConservator(address _conservator) external onlyOwner {

125:     function setMinterStatus(address _for, bool _status) external onlyOwner {

134:     function setBurnerStatus(address _for, bool _status) external onlyOwner {

```

