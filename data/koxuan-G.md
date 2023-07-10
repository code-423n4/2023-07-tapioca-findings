# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 12 |
| [GAS-2](#GAS-2) | Cache array length outside of loop | 9 |
| [GAS-3](#GAS-3) | Use Custom Errors | 62 |
| [GAS-4](#GAS-4) | Don't initialize variables with default value | 22 |
| [GAS-5](#GAS-5) | Functions guaranteed to revert when called by normal users can be marked `payable` | 15 |
| [GAS-6](#GAS-6) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 9 |
| [GAS-7](#GAS-7) | Use != 0 instead of > 0 for unsigned integer comparison | 39 |
### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (12)*:
```solidity
File: Penrose.sol

39:     bool public paused;

61:     mapping(address => bool) public isSingularityMasterContractRegistered;

63:     mapping(address => bool) public isBigBangMasterContractRegistered;

65:     mapping(address => bool) public isMarketRegistered;

71:     mapping(ISwapper => bool) public swappers;

```

```solidity
File: markets/Market.sol

35:     bool public paused;

129:     bool internal initialized;

```

```solidity
File: markets/bigBang/BigBang.sol

46:     mapping(address => mapping(address => bool)) public operators;

52:     bool private _isEthMarket;

```

```solidity
File: usd0/BaseUSDOStorage.sol

25:     mapping(uint256 => mapping(address => bool)) public allowedMinter;

28:     mapping(uint256 => mapping(address => bool)) public allowedBurner;

30:     bool public paused;

```

### <a name="GAS-2"></a>[GAS-2] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (9)*:
```solidity
File: markets/bigBang/BigBang.sol

215:         for (uint256 i = 0; i < calls.length; i++) {

666:         for (uint256 i = 0; i < users.length; i++) {

```

```solidity
File: markets/singularity/SGLLiquidation.sol

45:                 for (uint256 i = 0; i < maxBorrowParts.length; i++) {

107:         for (uint256 i = 0; i < users.length; i++) {

384:         for (uint256 i = 0; i < users.length; i++) {

```

```solidity
File: markets/singularity/Singularity.sol

187:         for (uint256 i = 0; i < calls.length; i++) {

```

```solidity
File: usd0/modules/USDOLeverageModule.sol

255:         for (uint256 i = 0; i < approvals.length; ) {

```

```solidity
File: usd0/modules/USDOMarketModule.sol

244:         for (uint256 i = 0; i < approvals.length; ) {

```

```solidity
File: usd0/modules/USDOOptionsModule.sol

245:         for (uint256 i = 0; i < approvals.length; ) {

```

### <a name="GAS-3"></a>[GAS-3] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (62)*:
```solidity
File: Penrose.sol

190:         require(!paused, "Penrose: paused");

242:         require(address(swappers_[0]) != address(0), "Penrose: zero address");

243:         require(address(markets_[0]) != address(0), "Penrose: zero address");

272:         require(msg.sender == conservator, "Penrose: unauthorized");

273:         require(val != paused, "Penrose: same state");

282:         require(_conservator != address(0), "Penrose: address not valid");

505:         require(swappers[swapper], "Penrose: Invalid swapper");

506:         require(isMarketRegistered[address(market)], "Penrose: Invalid market");

```

```solidity
File: markets/Market.sol

116:         require(!paused, "Market: paused");

126:         require(_isSolvent(from, exchangeRate), "Market: insolvent");

131:         require(!initialized, "Market: initialized");

143:         require(_val <= FEE_PRECISION, "Market: not valid");

172:             require(_borrowOpeningFee <= FEE_PRECISION, "Market: not valid");

193:             require(_callerFee <= FEE_PRECISION, "Market: not valid");

198:             require(_protocolFee <= FEE_PRECISION, "Market: not valid");

211:             require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");

220:             require(_maxLiquidatorReward < FEE_PRECISION, "Market: not valid");

246:         require(msg.sender == conservator, "Market: unauthorized");

247:         require(val != paused, "Market: same state");

344:             require(rate > 0, "Market: invalid rate");

```

```solidity
File: markets/MarketERC20.sol

142:             require(srcBalance >= amount, "ERC20: balance too low");

144:                 require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas

167:             require(srcBalance >= amount, "ERC20: balance too low");

179:                 require(to != address(0), "ERC20: no zero address"); // Moved down so other failed calls safe some gas

261:         require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

277:         require(signer == owner, "ERC20Permit: invalid signature");

```

```solidity
File: markets/bigBang/BigBang.sol

344:         require(penrose.swappers(swapper), "SGL: Invalid swapper");

371:         require(amountOut >= minAmountOut, "SGL: not enough");

391:         require(penrose.swappers(swapper), "SGL: Invalid swapper");

413:         require(amountOut >= minAmountOut, "SGL: not enough");

476:                 require(_minDebtRate < maxDebtRate, "BigBang: not valid");

482:                 require(_maxDebtRate > minDebtRate, "BigBang: not valid");

593:         require(penrose.swappers(swapper), "BigBang: Invalid swapper");

680:         require(liquidatedCount > 0, "SGL: no users found");

820:         require(borrowAmount != 0, "SGL: solvent");

```

```solidity
File: markets/singularity/SGLCommon.sol

240:         require(_totalAsset.base >= 1000, "SGL: min limit");

```

```solidity
File: markets/singularity/SGLLendingCommon.sol

75:         require(_totalAsset.base >= 1000, "SGL: min limit");

```

```solidity
File: markets/singularity/SGLLeverage.sol

104:         require(penrose.swappers(swapper), "SGL: Invalid swapper");

126:         require(amountOut >= minAmountOut, "SGL: not enough");

155:         require(penrose.swappers(swapper), "SGL: Invalid swapper");

182:         require(amountOut >= minAmountOut, "SGL: not enough");

```

```solidity
File: markets/singularity/SGLLiquidation.sol

152:         require(allBorrowAmount != 0, "SGL: solvent");

264:         require(borrowAmount != 0, "SGL: solvent");

327:         require(penrose.swappers(swapper), "SGL: Invalid swapper");

397:         require(liquidatedCount > 0, "SGL: no users found");

```

```solidity
File: markets/singularity/Singularity.sol

566:             require(_liquidationMultiplier < FEE_PRECISION, "SGL: not valid");

582:             require(_liquidationQueue.onlyOnce(), "SGL: LQ not initalized");

612:             revert("SGL: module not set");

```

```solidity
File: usd0/BaseUSDO.sol

97:         require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");

106:         require(_conservator != address(0), "USDO: address not valid");

115:         require(msg.sender == conservator, "USDO: unauthorized");

116:         require(val != paused, "USDO: same state");

355:             revert("USDO: module not found");

498:                 revert("OFTCoreV2: unknown packet type");

```

```solidity
File: usd0/USDO.sol

26:         require(!paused, "USDO: paused");

68:         require(token == address(this), "USDO: token not valid");

87:         require(token == address(this), "USDO: token not valid");

88:         require(maxFlashLoan(token) >= amount, "USDO: amount too big");

89:         require(amount > 0, "USDO: amount not valid");

100:         require(_allowance >= (amount + fee), "USDO: repay not approved");

110:         require(allowedMinter[_getChainId()][msg.sender], "USDO: unauthorized");

119:         require(allowedBurner[_getChainId()][msg.sender], "USDO: unauthorized");

```

### <a name="GAS-4"></a>[GAS-4] Don't initialize variables with default value

*Instances (22)*:
```solidity
File: Penrose.sol

437:         for (uint256 i = 0; i < len; ) {

492:             for (uint256 i = 0; i < length; ) {

512:         uint256 amount = 0;

546:         uint256 marketsLength = 0;

550:             for (uint256 i = 0; i < _masterContractLength; ) {

564:             for (uint256 i = 0; i < _masterContractLength; ) {

569:                 for (uint256 j = 0; j < clonesOfLength; ) {

```

```solidity
File: markets/bigBang/BigBang.sol

215:         for (uint256 i = 0; i < calls.length; i++) {

527:         uint256 extraAmount = 0;

603:         uint256 minAssetMount = 0;

665:         uint256 liquidatedCount = 0;

666:         for (uint256 i = 0; i < users.length; i++) {

```

```solidity
File: markets/singularity/SGLLiquidation.sol

44:                 uint256 needed = 0;

45:                 for (uint256 i = 0; i < maxBorrowParts.length; i++) {

107:         for (uint256 i = 0; i < users.length; i++) {

337:         uint256 minAssetAmount = 0;

383:         uint256 liquidatedCount = 0;

384:         for (uint256 i = 0; i < users.length; i++) {

```

```solidity
File: markets/singularity/Singularity.sol

187:         for (uint256 i = 0; i < calls.length; i++) {

```

```solidity
File: usd0/modules/USDOLeverageModule.sol

255:         for (uint256 i = 0; i < approvals.length; ) {

```

```solidity
File: usd0/modules/USDOMarketModule.sol

244:         for (uint256 i = 0; i < approvals.length; ) {

```

```solidity
File: usd0/modules/USDOOptionsModule.sol

245:         for (uint256 i = 0; i < approvals.length; ) {

```

### <a name="GAS-5"></a>[GAS-5] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (15)*:
```solidity
File: Penrose.sol

256:     function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner {

263:     function setBigBangEthMarket(address _market) external onlyOwner {

281:     function setConservator(address _conservator) external onlyOwner {

291:     function setUsdoToken(address _usdoToken) external onlyOwner {

455:     function setFeeTo(address feeTo_) external onlyOwner {

464:     function setSwapper(ISwapper swapper, bool enable) external onlyOwner {

```

```solidity
File: markets/Market.sol

142:     function setBorrowOpeningFee(uint256 _val) external onlyOwner {

151:     function setBorrowCap(uint256 _cap) external notPaused onlyOwner {

```

```solidity
File: markets/bigBang/BigBang.sol

101:     function init(bytes calldata data) external onlyOnce {

```

```solidity
File: markets/singularity/Singularity.sol

62:     function init(bytes calldata data) external onlyOnce {

```

```solidity
File: usd0/BaseUSDO.sol

88:     function setMaxFlashMintable(uint256 _val) external onlyOwner {

96:     function setFlashMintFee(uint256 _val) external onlyOwner {

105:     function setConservator(address _conservator) external onlyOwner {

125:     function setMinterStatus(address _for, bool _status) external onlyOwner {

134:     function setBurnerStatus(address _for, bool _status) external onlyOwner {

```

### <a name="GAS-6"></a>[GAS-6] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (9)*:
```solidity
File: markets/MarketERC20.sol

248:         current = _nonces[owner]++;

```

```solidity
File: markets/bigBang/BigBang.sol

215:         for (uint256 i = 0; i < calls.length; i++) {

666:         for (uint256 i = 0; i < users.length; i++) {

669:                 liquidatedCount++;

```

```solidity
File: markets/singularity/SGLLiquidation.sol

45:                 for (uint256 i = 0; i < maxBorrowParts.length; i++) {

107:         for (uint256 i = 0; i < users.length; i++) {

384:         for (uint256 i = 0; i < users.length; i++) {

387:                 liquidatedCount++;

```

```solidity
File: markets/singularity/Singularity.sol

187:         for (uint256 i = 0; i < calls.length; i++) {

```

### <a name="GAS-7"></a>[GAS-7] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (39)*:
```solidity
File: markets/Market.sol

171:         if (_borrowOpeningFee > 0) {

182:         if (_oracleData.length > 0) {

192:         if (_callerFee > 0) {

197:         if (_protocolFee > 0) {

202:         if (_liquidationBonusAmount > 0) {

210:         if (_minLiquidatorReward > 0) {

219:         if (_maxLiquidatorReward > 0) {

228:         if (_totalBorrowCap > 0) {

233:         if (_collateralizationRate > 0) {

344:             require(rate > 0, "Market: invalid rate");

```

```solidity
File: markets/bigBang/BigBang.sol

152:         EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0

348:         if (supplyShare > 0) {

449:         if (totalFees > 0) {

475:             if (_minDebtRate > 0) {

481:             if (_maxDebtRate > 0) {

487:             if (_debtRateAgainstEthMarket > 0) {

495:             if (_liquidationMultiplier > 0) {

604:         if (_dexData.length > 0) {

680:         require(liquidatedCount > 0, "SGL: no users found");

734:         if (toBurn > 0) {

```

```solidity
File: markets/singularity/SGLLeverage.sol

159:         if (supplyShare > 0) {

```

```solidity
File: markets/singularity/SGLLiquidation.sol

233:         if (liquidationBonusAmount > 0) {

338:         if (dexData.length > 0) {

397:         require(liquidatedCount > 0, "SGL: no users found");

```

```solidity
File: markets/singularity/Singularity.sol

131:         EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0

480:         if (accrueInfo.feesEarnedFraction > 0) {

498:         if (_minimumTargetUtilization > 0) {

506:         if (_maximumTargetUtilization > 0) {

521:         if (_minimumInterestPerSecond > 0) {

533:         if (_maximumInterestPerSecond > 0) {

545:         if (_interestElasticity > 0) {

553:         if (_lqCollateralizationRate > 0) {

565:         if (_liquidationMultiplier > 0) {

```

```solidity
File: usd0/USDO.sol

89:         require(amount > 0, "USDO: amount not valid");

```

```solidity
File: usd0/modules/USDOLeverageModule.sol

119:         if (approvals.length > 0) {

```

```solidity
File: usd0/modules/USDOMarketModule.sol

123:         if (approvals.length > 0) {

197:         if (approvals.length > 0) {

```

```solidity
File: usd0/modules/USDOOptionsModule.sol

123:         if (approvals.length > 0) {

216:         if (approvals.length > 0) {

```

