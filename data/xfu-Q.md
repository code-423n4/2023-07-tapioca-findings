|ID|Issues|Instances|
|---|:---|:---:|
| [QA-00] | Using vulnerable dependency of OpenZeppelin | 3 |
| [QA-01] | `onERC721Received` not implemented in ERC721 received `TapiocaOptionBroker` contract | 1 |
| [QA-02] | `onERC721Received` callback is never called when new tokens are minted or transferred | 1 |
| [QA-03] | Functions calling contracts/addresses with transfer hooks are missing reentrancy guards | 4 |
| [QA-04] | Unsafe to use floating pragma | 1 |
| [QA-05] | Too many digits | 1 |
| [QA-06] | Double type casts create complexity within the code | 6 |
| [QA-07] | Did not Approve to zero first | 1 |
| [QA-08] | Divide before multiply | 11 |
| [QA-09] | Lack of a double-step `transferOwnership()` pattern | 15 |
| [QA-10] | Cyclomatic complexity | 1 |
| [QA-11] | Approve `type(uint256).max` not work with some tokens | 7 |
| [QA-12] | Setters should check the input value | 9 |
| [QA-13] | `abi.encodePacked()` should not be used with dynamic types | 1 |

## [QA-00] Using vulnerable dependency of OpenZeppelin

### description:

```
    // package.json
    "@openzeppelin/contracts": "^4.8.2",
```

The `package.json` configuration file says that the project is using 4.8.2 of OZ which has a not last update version and has 4 vulnerabilities:

- [GovernorCompatibilityBravo may trim proposal calldata](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-93hq-5wgc-jc82)
- [TransparentUpgradeableProxy clashing selector calls may not be delegated](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-mx2q-35m2-x2rh)
- [Governor proposal creation may be blocked by frontrunning](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-5h3x-9wvq-w4m2)
- [MerkleProof multiproofs may allow proving arbitrary leaves for specific trees](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-wprv-93r4-jj2p)

### recommendation:

Update OpenZeppelin to the [latest version](https://github.com/OpenZeppelin/openzeppelin-contracts/releases).

### locations:

- tapioca-bar-audit/package.json
- tap-token-audit/package.json
- YieldBox/package.json

### severity:

Low

## [QA-01] `onERC721Received` not implemented in ERC721 received `TapiocaOptionBroker` contract

### description:

The contract `TapiocaOptionBroker` does not implement the `onERC721Received` function,
which is considered a best practice to transfer ERC721 tokens from contracts to contracts.
The absence of this function could prevent the contract from receiving ERC721 tokens
from other contracts via `safeTransferFrom/transferFrom`.

**There is `1` instance of this issue:**

- [TapiocaOptionBroker](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L53-L578) received NFT via following operations is missing `onERC721Received` function:
  - [tOLP.transferFrom(msg.sender,address(this),\_tOLPTokenID)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L230)

### recommendation:

Consider adding an implementation of the `onERC721Received` function in the `TapiocaOptionBroker` contract.

### locations:

- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L53-L578

### severity:

Low

## [QA-02] `onERC721Received` callback is never called when new tokens are minted or transferred

### description:

The ERC721 implementation used by the contract does not properly call the
corresponding callback when new tokens are minted or transferred.

The [ERC721 standard](https://eips.ethereum.org/EIPS/eip-721) states that the onERC721Received callback must be called when a
mint or transfer operation occurs.

However, the smart contracts interacting as users of the contracts will not be
notified with the `onERC721Received` callback, as expected according to the ERC721
standard.

**There is `1` instance of this issue:**

- [tOLP.transferFrom(address(this),otapOwner,oTAPPosition.tOLP)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L342) unchecked `onERC721Received` callback.

#### Exploit scenario

Alice deploys a contract to interact with the Controller contract to send and receive
ERC721 tokens. Her contract correctly implements the `onERC71Received` callback, but this
is not called when tokens are minted or transferred back to her contract. As a result, the
tokens are trapped.

### recommendation:

Short term, ensure that the ERC721 implementations execute the standard callback when
they are required.

Example see OpenZeppelin implementation: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8b72e20e326078029b92d526ff5a44add2671df1/contracts/token/ERC721/ERC721.sol#L425-L447

### locations:

- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L342

### severity:

Low

## [QA-03] Functions calling contracts/addresses with transfer hooks are missing reentrancy guards

### description:

Even if the function follows the best practice of check-effects-interaction,
not using a reentrancy guard when there may be transfer hooks will open the
users of this protocol up to
[read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/)
with no way to protect against it, except by block-listing the whole protocol.

**There are `4` instances of this issue (Excluded instances in `bot report`):**

- [rewardTokens[i].safeTransfer(\_to,amount)](/tap-token-audit/contracts/governance/twTAP.sol#L492) should use Reentrancy-Guard.

- [\_paymentToken.transferFrom(msg.sender,address(this),discountedPaymentAmount)](/tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L509-L513) should use Reentrancy-Guard.

- [rewardTokens[claimableIndex].safeTransfer(\_to,amount)](/tap-token-audit/contracts/governance/twTAP.sol#L514) should use Reentrancy-Guard.

- [\_paymentToken.transferFrom(msg.sender,address(this),discountedPaymentAmount)](/tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L530-L534) should use Reentrancy-Guard.

### recommendation:

Using [Reentrancy-Guard](https://github.com/OpenZeppelin/openzeppelin-/tap-token-audit/contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05//tap-token-audit/contracts/security/ReentrancyGuard.sol#L50C5-L62)
when calling /tap-token-audit/contracts/addresses with transfer hooks.

### locations:

- /tap-token-audit/contracts/governance/twTAP.sol#L492
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L509-L513
- /tap-token-audit/contracts/option-airdrop/AirdropBroker.sol#L514
- /tap-token-audit/contracts/options/TapiocaOptionBroker.sol#L530-L534

### severity:

Low

## [QA-04] Unsafe to use floating pragma

### description:

Contracts should be deployed with the same compiler version and flags that
they have been tested with thoroughly.
Locking the pragma helps to ensure that contracts do not accidentally get deployed using,
for example, an outdated compiler version that might introduce bugs that affect the
contract system negatively.

More detail see [SWC-103](https://swcregistry.io/docs/SWC-103).

**There is `1` instance of this issue (Excluded instances in `bot report`):**

- Should lock the pragma version instead of floating pragma: [^0.8.18](/tap-token-audit/contracts/tokens/BaseTapOFT.sol#L2).

### recommendation:

Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases)
for the compiler version that is chosen.

### locations:

- /tap-token-audit/contracts/tokens/BaseTapOFT.sol#L2

### severity:

Low

## [QA-05] Too many digits

### description:

Literals with many digits are difficult to read and review.

**There is `1` instance of this issue:**

- [TapOFT](/tap-token-audit/contracts/tokens/TapOFT.sol#L26-L256) uses literals with too many digits:
  - [decay_rate = 8800000000000000](/tap-token-audit/contracts/tokens/TapOFT.sol#L45)

#### Exploit scenario

```solidity
contract MyContract{
    uint 1_ether = 10000000000000000000;
}
```

While `1_ether` looks like `1 ether`, it is `10 ether`. As a result, it's likely to be used incorrectly.

### recommendation:

Use:

- [Ether suffix](https://solidity.readthedocs.io/en/latest/units-and-global-variables.html#ether-units),
- [Time suffix](https://solidity.readthedocs.io/en/latest/units-and-global-variables.html#time-units), or
- [The scientific notation](https://solidity.readthedocs.io/en/latest/types.html#rational-and-integer-literals)

### locations:

- /tap-token-audit/contracts/tokens/TapOFT.sol#L26-L256

### severity:

Informational

## [QA-06] Double type casts create complexity within the code

### description:

Double type casting should be avoided in Solidity contracts to prevent unintended
consequences and ensure accurate data representation.
Performing multiple type casts in succession can lead to unexpected truncation,
rounding errors, or loss of precision, potentially compromising the contract's
functionality and reliability. Furthermore, double type casting can make the code
less readable and harder to maintain, increasing the likelihood of errors and
misunderstandings during development and debugging. To ensure precise and consistent
data handling, developers should use appropriate data types and avoid unnecessary
or excessive type casting, promoting a more robust and dependable contract execution.

**There are `6` instances of this issue:**

- [\_accrueInfo.interestPerSecond = uint64((uint256(\_accrueInfo.interestPerSecond) \* interestElasticity) / scale)](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L117-L120) should use single casting instead of double casting.

- [string(abi.encodePacked(string(abi.encodePacked(ERC1155:,uint256(uint160(asset.contractAddress)).toHexString(20),/,asset.tokenId.toString())), (,asset.strategy.name(),)))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L31-L46) should use single casting instead of double casting.

- [details.name = string(abi.encodePacked(ERC1155:,uint256(uint160(asset.contractAddress)).toHexString(20),/,asset.tokenId.toString()))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L89-L91) should use single casting instead of double casting.

- [properties = string(abi.encodePacked(,"tokenAddress":",uint256(uint160(asset.contractAddress)).toHexString(20),"))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L104-L108) should use single casting instead of double casting.

- [string(abi.encodePacked(data:application/json;base64,,abi.encodePacked({"name":",details.name,","symbol":",details.symbol,",,,,"properties":{"strategy":",uint256(uint160(address(asset.strategy))).toHexString(20),","tokenType":",details.tokenType,",properties,string(abi.encodePacked(,"tokenId":,asset.tokenId.toString())),}}).encode()))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L110-L134) should use single casting instead of double casting.

- [string(abi.encodePacked(data:application/json;base64,,abi.encodePacked({"name":",details.name,","symbol":",details.symbol,",,"decimals":,details.decimals.toString(),,"properties":{"strategy":",uint256(uint160(address(asset.strategy))).toHexString(20),","tokenType":",details.tokenType,",properties,,}}).encode()))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L110-L134) should use single casting instead of double casting.

### recommendation:

Consider using single casting instead of double casting.

### locations:

- /tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L117-L120
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L31-L46
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L89-L91
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L104-L108
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L110-L134
- /YieldBox/contracts/YieldBoxURIBuilder.sol#L110-L134

### severity:

Low

## [QA-07] Did not Approve to zero first

### description:

Calling `approve()` without first calling `approve(0)` if the current approval is non-zero
will revert with some tokens, such as Tether (USDT). While Tether is known to do this,
it applies to other tokens as well, which are trying to protect against
[this attack vector](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).
`safeApprove()` itself also implements this protection.
Always reset the approval to zero before changing it to a new value,
or use `safeIncreaseAllowance()`/`safeDecreaseAllowance()`

**There is `1` instance of this issue (Excluded instances in `bot report`):**

- [IERC20(swapData.tokenOut).approve(externalData.tOft,amountOut)](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L217) should be used `safeIncreaseAllowance()` or `safeDecreaseAllowance()` instead.

#### Exploit scenario

Some ERC20 tokens like `USDT` require resetting the approval to 0 first before being
able to reset it to another value.

Unsafe ERC20 approve that do not handle non-standard erc20 behavior.

1. Some token contracts do not return any value.
2. Some token contracts revert the transaction when the allowance is not zero.

### recommendation:

As suggested by the [OpenZeppelin comment](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45),
replace `approve()/safeApprove()` with `safeIncreaseAllowance()` or `safeDecreaseAllowance()` instead.

### locations:

- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L217

### severity:

Low

## [QA-08] Divide before multiply

### description:

Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.

**There are `11` instances of this issue:**

- [SGLCommon.\_getInterestRate()](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137) performs a multiplication on the result of a division:

  - [feeAmount = (extraAmount \* protocolFee) / FEE_PRECISION](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L106)
  - [feeFraction = (feeAmount \* \_totalAsset.base) / fullAssetAmount](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L107)

- [SGLCommon.\_getInterestRate()](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137) performs a multiplication on the result of a division:

  - [underFactor = ((minimumTargetUtilization - utilization) \* FACTOR_PRECISION) / minimumTargetUtilization](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L113-L114)
  - [scale = interestElasticity + (underFactor _ underFactor _ elapsedTime)](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L115-L116)

- [SGLCommon.\_getInterestRate()](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137) performs a multiplication on the result of a division:

  - [overFactor = ((utilization - maximumTargetUtilization) \* FACTOR_PRECISION) / fullUtilizationMinusMax](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L125-L126)
  - [scale*scope_0 = interestElasticity + (overFactor * overFactor \_ elapsedTime)](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L127-L128)

- [SGLCommon.\_getInterestRate()](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137) performs a multiplication on the result of a division:

  - [extraAmount = (uint256(\_totalBorrow.elastic) _ \_accrueInfo.interestPerSecond _ elapsedTime) / 1e18](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L99-L103)
  - [feeAmount = (extraAmount \* protocolFee) / FEE_PRECISION](/tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L106)

- [SGLLiquidation.\_computeAssetAmountToSolvency(address,uint256)](/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol#L70-L95) performs a multiplication on the result of a division:

  - [collateralAmountInAsset = yieldBox.toAmount(collateralId,(collateralShare _ (EXCHANGE_RATE_PRECISION / FEE_PRECISION) _ lqCollateralizationRate),false) / \_exchangeRate](/tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol#L81-L87)

- [BigBang.getDebtRate()](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L180-L201) performs a multiplication on the result of a division:

  - [debtPercentage = ((\_currentDebt - debtStartPoint) \* DEBT_PRECISION) / (\_maxDebtPoint - debtStartPoint)](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L192-L193)
  - [debt = ((maxDebtRate - minDebtRate) \* debtPercentage) / DEBT_PRECISION + minDebtRate](/tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L194-L196)

- [Market.computeClosingFactor(uint256,uint256,uint256,uint256,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L256-L297) performs a multiplication on the result of a division:

  - [collateralPartInAssetScaled = collateralPartInAsset / (10 \*\* (collateralPartDecimals - 18))](/tapioca-bar-audit/contracts/markets/Market.sol#L273-L275)
  - [liquidationStartsAt = (collateralPartInAssetScaled \* collateralizationRate) / (10 \*\* ratesPrecision)](/tapioca-bar-audit/contracts/markets/Market.sol#L283-L284)

- [Market.computeClosingFactor(uint256,uint256,uint256,uint256,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L256-L297) performs a multiplication on the result of a division:

  - [collateralPartInAssetScaled = collateralPartInAsset / (10 \*\* (collateralPartDecimals - 18))](/tapioca-bar-audit/contracts/markets/Market.sol#L273-L275)
  - [numerator = borrowPartScaled - ((collateralizationRate \* collateralPartInAssetScaled) / (10 \*\* ratesPrecision))](/tapioca-bar-audit/contracts/markets/Market.sol#L287-L289)

- [Market.\_computeMaxBorrowableAmount(address,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L385-L398) performs a multiplication on the result of a division:

  - [collateralAmountInAsset = yieldBox.toAmount(collateralId,(userCollateralShare[user] _ (EXCHANGE_RATE_PRECISION / FEE_PRECISION) _ collateralizationRate),false) / \_exchangeRate](/tapioca-bar-audit/contracts/markets/Market.sol#L389-L397)

- [Market.\_isSolvent(address,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L402-L425) performs a multiplication on the result of a division:

  - [yieldBox.toAmount(collateralId,collateralShare _ (EXCHANGE_RATE_PRECISION / FEE_PRECISION) _ collateralizationRate,false) >= (borrowPart _ \_totalBorrow.elastic _ \_exchangeRate) / \_totalBorrow.base](/tapioca-bar-audit/contracts/markets/Market.sol#L414-L424)

- [Market.\_computeMaxAndMinLTVInAsset(uint256,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L428-L440) performs a multiplication on the result of a division:

  - [max = (collateralAmount \* EXCHANGE_RATE_PRECISION) / \_exchangeRate](/tapioca-bar-audit/contracts/markets/Market.sol#L438)
  - [min = (max \* collateralizationRate) / FEE_PRECISION](/tapioca-bar-audit/contracts/markets/Market.sol#L439)

- [YieldBoxRebase.\_toShares(uint256,uint256,uint256,bool)](/YieldBox/contracts/YieldBoxRebase.sol#L18-L38) performs a multiplication on the result of a division:

  - [share = (amount \* totalShares\_) / totalAmount](/YieldBox/contracts/YieldBoxRebase.sol#L32)
  - [roundUp && (share \* totalAmount) / totalShares\_ < amount](/YieldBox/contracts/YieldBoxRebase.sol#L35)

- [YieldBoxRebase.\_toAmount(uint256,uint256,uint256,bool)](/YieldBox/contracts/YieldBoxRebase.sol#L41-L61) performs a multiplication on the result of a division:
  - [amount = (share \* totalAmount) / totalShares\_](/YieldBox/contracts/YieldBoxRebase.sol#L55)
  - [roundUp && (amount \* totalShares\_) / totalAmount < share](/YieldBox/contracts/YieldBoxRebase.sol#L58)

#### Exploit scenario

```solidity
contract A {
  function f(uint n) public {
    coins = (oldSupply / n) * interest;
  }
}
```

If `n` is greater than `oldSupply`, `coins` will be zero. For example, with `oldSupply = 5; n = 10, interest = 2`, coins will be zero.  
If `(oldSupply * interest / n)` was used, `coins` would have been `1`.  
In general, it's usually a good idea to re-arrange arithmetic to perform multiplication before division, unless the limit of a smaller type makes this dangerous.

### recommendation:

Consider ordering multiplication before division.

### locations:

- /tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137
- /tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137
- /tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137
- /tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol#L35-L137
- /tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol#L70-L95
- /tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol#L180-L201
- /tapioca-bar-audit/contracts/markets/Market.sol#L256-L297
- /tapioca-bar-audit/contracts/markets/Market.sol#L256-L297
- /tapioca-bar-audit/contracts/markets/Market.sol#L385-L398
- /tapioca-bar-audit/contracts/markets/Market.sol#L402-L425
- /tapioca-bar-audit/contracts/markets/Market.sol#L428-L440
- /YieldBox/contracts/YieldBoxRebase.sol#L18-L38
- /YieldBox/contracts/YieldBoxRebase.sol#L41-L61

### severity:

Low

## [QA-09] Lack of a double-step `transferOwnership()` pattern

### description:

The current ownership transfer process for all the contracts inheriting
from `Ownable` or `OwnableUpgradeable` involves the current owner calling the
[transferOwnership()](https://github.com/OpenZeppelin/openzeppelin-/tapioca-bar-audit/contracts/blob/release-v4.8//tapioca-bar-audit/contracts/access/Ownable.sol#L69-L72) function:

```
    function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        _transferOwnership(newOwner);
    }
```

If the nominated EOA account is not a valid account, it is entirely possible
that the owner may accidentally transfer ownership to an uncontrolled
account, losing the access to all functions with the `onlyOwner` modifier.

**There are `15` instances of this issue (Excluded instances in `bot report`):**

- [USDOOptionsModule](/tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L16-L300) does not implement a `2-Step-Process` for transferring ownership.
- [BaseUSDOStorage](/tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol#L17-L99) does not implement a `2-Step-Process` for transferring ownership.
- [USDOLeverageModule](/tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L19-L310) does not implement a `2-Step-Process` for transferring ownership.
- [USDOMarketModule](/tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L21-L299) does not implement a `2-Step-Process` for transferring ownership.
- [USDO](/tapioca-bar-audit/contracts/usd0/USDO.sol#L24-L123) does not implement a `2-Step-Process` for transferring ownership.
- [BaseUSDO](/tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L46-L502) does not implement a `2-Step-Process` for transferring ownership.
- [mTapiocaOFT](/tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol#L9-L153) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFT](/tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L15-L574) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFTOptionsModule](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L16-L315) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFTStrategyModule](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L16-L249) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFTMarketModule](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L20-L316) does not implement a `2-Step-Process` for transferring ownership.
- [TapiocaOFT](/tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol#L21-L91) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFTLeverageModule](/tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L21-L340) does not implement a `2-Step-Process` for transferring ownership.
- [BaseTOFTStorage](/tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol#L21-L79) does not implement a `2-Step-Process` for transferring ownership.
- [TapiocaWrapper](/tapiocaz-audit/contracts/TapiocaWrapper.sol#L26-L229) does not implement a `2-Step-Process` for transferring ownership.

### recommendation:

It is recommended to implement a two-step process where the owner nominates
an account and the nominated account needs to call an `acceptOwnership()`
function for the transfer of the ownership to fully succeed. This ensures
the nominated EOA account is a valid and active account. This can be
easily achieved by using OpenZeppelin’s [Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-/tapioca-bar-audit/contracts/blob/release-v4.8//tapioca-bar-audit/contracts/access/Ownable2Step.sol) contract instead of
`Ownable`:

```
abstract contract Ownable2Step is Ownable {

    /**
     * @dev Starts the ownership transfer of the contract to a new account. Replaces the pending transfer if there is one.
     * Can only be called by the current owner.
     */
    function transferOwnership(address newOwner) public virtual override onlyOwner {
        _pendingOwner = newOwner;
        emit OwnershipTransferStarted(owner(), newOwner);
    }

    ...

    /**
     * @dev The new owner accepts the ownership transfer.
     */
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
}
```

### locations:

- /tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol#L16-L300
- /tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol#L17-L99
- /tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol#L19-L310
- /tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol#L21-L299
- /tapioca-bar-audit/contracts/usd0/USDO.sol#L24-L123
- /tapioca-bar-audit/contracts/usd0/BaseUSDO.sol#L46-L502
- /tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol#L9-L153
- /tapiocaz-audit/contracts/tOFT/BaseTOFT.sol#L15-L574
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L16-L315
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L16-L249
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L20-L316
- /tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol#L21-L91
- /tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L21-L340
- /tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol#L21-L79
- /tapiocaz-audit/contracts/TapiocaWrapper.sol#L26-L229

### severity:

Low

## [QA-10] Cyclomatic complexity

### description:

Detects functions with high (> 11) cyclomatic complexity.

**There is `1` instance of this issue:**

- [Market.setMarketConfig(uint256,IOracle,bytes,address,uint256,uint256,uint256,uint256,uint256,uint256,uint256)](/tapioca-bar-audit/contracts/markets/Market.sol#L158-L240) has a high cyclomatic complexity (12).

### recommendation:

Reduce cyclomatic complexity by splitting the function into several smaller subroutines.

### locations:

- /tapioca-bar-audit/contracts/markets/Market.sol#L158-L240

### severity:

Informational

## [QA-11] Approve `type(uint256).max` not work with some tokens

### description:

Some tokens (e.g. `UNI`, `COMP`) revert if the value passed to `approve` or `transfer`
is larger than `uint96`.

Both of the above tokens have special case logic in `approve` that sets `allowance`
to `type(uint96).max` if the `approval` amount is `uint256(-1)`, which may cause
issues with systems that expect the value passed to `approve` to be reflected in
the allowances mapping.

Approving the maximum value of `uint256` is a known practice to save gas.
However, this pattern was proven to increase the impact of an attack many times in the past,
in case the approved contract gets hacked.

**There are `7` instances of this issue:**

- [IERC20(address(pool)).approve(\_vault,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L78) should use exact amount that's needed to be transferred.

- [IERC20(lpGetter.lpToken()).approve(\_lpGauge,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L78) should use exact amount that's needed to be transferred.

- [lpToken.approve(\_lpGauge,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L79) should use exact amount that's needed to be transferred.

- [IERC20(lpGetter.lpToken()).approve(\_lpGetter,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L79) should use exact amount that's needed to be transferred.

- [lpToken.approve(\_lpGetter,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L80) should use exact amount that's needed to be transferred.

- [lpToken.approve(\_lpGetter,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L106) should use exact amount that's needed to be transferred.

- [lpToken.approve(\_booster,type()(uint256).max)](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L107) should use exact amount that's needed to be transferred.

### recommendation:

Consider approving the exact amount that’s needed to be transferred
instead of the `type(uint256).max` amount.

### locations:

- /tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L78
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L78
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L79
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L79
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L80
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L106
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L107

### severity:

Low

## [QA-12] Setters should check the input value

### description:

Setters should have initial value check to prevent assigning wrong value to the variable.
Assignment of wrong value can lead to unexpected behavior of the contract.

**There are `9` instances of this issue:**

- [CompoundStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L89) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L91)

- [YearnStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L90) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L92)

- [LidoEthStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L93) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L95)

- [BalancerStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L109) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L111)

- [AaveStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L122) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L124)

- [TricryptoNativeStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L125) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L127)

- [TricryptoLPStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L134) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L136)

- [StargateStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L142) lacks an upper limit check on :

  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L144)

- [ConvexTricryptoStrategy.setDepositThreshold(uint256).amount](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L163) lacks an upper limit check on :
  - [depositThreshold = amount](/tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L165)

### recommendation:

Add an upper limit check to the setters function.

### locations:

- /tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol#L89
- /tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol#L90
- /tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol#L93
- /tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol#L109
- /tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol#L122
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol#L125
- /tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol#L134
- /tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol#L142
- /tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol#L163

### severity:

Low

## [QA-13] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

### description:

Use `abi.encode()` instead which will pad items to 32 bytes, which will
[prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode)
(e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`,
but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason,
`abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()`
it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

There is also discussion of [removing abi.encodePacked from future versions of Solidity](https://github.com/ethereum/solidity/issues/11593),
so using `abi.encode` now will ensure compatibility in the future.

**There is `1` instance of this issue:**

- [YieldBoxURIBuilder.uri(Asset,NativeToken,uint256,address)](/YieldBox/contracts/YieldBoxURIBuilder.sol#L79-L135) calls abi.encodePacked() with multiple dynamic arguments:
  - [string(abi.encodePacked(data:application/json;base64,,abi.encodePacked({"name":",details.name,","symbol":",details.symbol,",,,,"properties":{"strategy":",uint256(uint160(address(asset.strategy))).toHexString(20),","tokenType":",details.tokenType,",properties,string(abi.encodePacked(,"tokenId":,asset.tokenId.toString())),}}).encode()))](/YieldBox/contracts/YieldBoxURIBuilder.sol#L110-L134)

#### Exploit scenario

```solidity
contract Sign {
    function get_hash_for_signature(string name, string doc) external returns(bytes32) {
        return keccak256(abi.encodePacked(name, doc));
    }
}
```

Bob calls `get_hash_for_signature` with (`bob`, `This is the content`). The hash returned is used as an ID.
Eve creates a collision with the ID using (`bo`, `bThis is the content`) and compromises the system.

### recommendation:

Do not use more than one dynamic type in `abi.encodePacked()`
(see the [Solidity documentation](https://docs.soliditylang.org/en/latest/abi-spec.html#non-standard-packed-mode)).
Use `abi.encode()`, preferably.

### locations:

- /YieldBox/contracts/YieldBoxURIBuilder.sol#L79-L135

### severity:

Low
