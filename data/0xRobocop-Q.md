# L-01 SGLCommon: Incorrect Rounding in _removeAsset

Due to integer division, solidity math always have some degree of rounding errors. However, the rounding should always favor the protocol and not the user. In the `_removeAsset` function the opposite happens:

```solidity
// @audit-issue This should be false, roundings should favor the protocol.
uint256 allShare = _totalAsset.elastic +
            yieldBox.toShare(assetId, totalBorrow.elastic, true);  

share = (fraction * allShare) / _totalAsset.base;

// ....
// ....

yieldBox.transfer(address(this), to, assetId, share);
```

`allShare` is computed with the yield box flag to round up making the variable `share` to be bigger, which at the end it is sent to the user. It is recommended to compute `allShare` with `false`.


# L-02 SGLLendingCommon Incorrect accounting of yield box shares in _repay 

At the end of repayment the function calls `_addTokens`:

```solidity
_addTokens(from, to, assetId, share, uint256(totalShare), skim);
```

Which is used to transfer the funds for the repayment from `from` to the contract, but it also updates the mapping `_yieldBoxShares[to][_asset_sig] += share;` for the `to` parameter which is the account whose debt is being repaid. This is an error in the accounting since the `to` account is not transferred any shares. This error will cause the function `yieldBoxShares` in the `Singularity.sol` contract to return incorrect values which might cause vulnerabilities in third party integrations.

# L-03 Singularity: The function computeAllowedLendShare returns an incorrect value

The function is used to transform `amount` to shares for a market's permit operation, it looks like:

```solidity
function computeAllowedLendShare(
        uint256 amount,
        uint256 tokenId
) external view returns (uint256 share) {
    uint256 allShare = totalAsset.elastic +
       yieldBox.toShare(tokenId, totalBorrow.elastic, true);
    share = (amount * allShare) / totalAsset.base;
}
```

The issue is that `totalBorrow.elastic` is not updated yet, so it does not consider the fees accrued so far, which makes `computeAllowedLendShare` to return an incorrect value. 
 
# L-04 Market: There is not sanity check that `EXCHANGE_RATE_PRECISION > FEE_PRECISION`

There is no check when `EXCHANGE_RATE_PRECISION` is initialized that this value is actually greater than `FEE_PRECISION`. The default value for `EXCHANGE_RATE_PRECISION` is `1e18` which is way greater than `FEE_PRECISION`, so, initializing the value to be smaller than `FEE_PRECISION` will require a big configuration mistake. Even though it has pretty probability of happening the damage might be important since the solvency computation is done as follows:

```solidity
yieldBox.toAmount(
      collateralId,
      collateralShare *
          (EXCHANGE_RATE_PRECISION / FEE_PRECISION) *
          collateralizationRate,
      false
      ) >=
      // Moved exchangeRate here instead of dividing the other side to preserve more precision
      // @audit-info This formula returns the debt the user has to pay but in terms of collateral.
      (borrowPart * _totalBorrow.elastic * _exchangeRate) /
        _totalBorrow.base;
```

If `EXCHANGE_RATE_PRECISION < FEE_PRECISION` then the left side will be always zero, making all the accounts not solvents.

# L-05 Inconsistency in liquidations between BigBang and Singularity

The singularity markets liquidations make use of the variable `liquidationBonusAmount` which represents the max percentage increase in the available collateral that can be liquidated returned by `computeClosingFactor`:

```solidity
// @audit-issue This is not included in BigBang
if (liquidationBonusAmount > 0) {
     availableBorrowPart =
         availableBorrowPart +
         (availableBorrowPart * liquidationBonusAmount) /
         FEE_PRECISION;
}
```

The issue is that this logic is not included in the BigBang liquidations even though the `liquidationBonusAmount` exists in both. The absence of this logic in BigBang may be a security risk because a `liquidationBonusAmount` allows to liquidate more collateral which helps positions to get healthier after a liquidation happens. 

# L-06 twTAP: Inconsistency in expiration dates 

The `_releaseTap` function considers a position expired when `block.timestamp == position.expiry`:

```solidity
// @audit-info When block.timestamp == expiry, then it is considered expired.
require(position.expiry <= block.timestamp, "twTAP: Lock not expired");
```

But the `getParticipation` do not:

```solidity
if (participant.expiry < block.timestamp) {
      participant.multiplier = 0;
}
return participant;
```

In other words, when `block.timestamp == expiry` the `_releaseTap` function will consider the position expired but the `getParticipation` function do not, which may cause vulnerabilities in third party integrations.

# L-07 Vesting: There is no way to get tokens out

The contract does not have a way to transfer the vested token out of the contract in case more tokens were sent that the ones distributed or in case if someone do not claim its vested tokens. 

# NC-01 Singularity: Incorrect comment 

Incorrect comment:

```solidity
minimumInterestPerSecond = 951293760; // approx 3% APR
// @audit-issue NC This is not 1% but 3%.
accrueInfo.interestPerSecond = uint64(startingInterestPerSecond); // 1% APR, with 1e18 being 100%
```

# NC-02 Pausable contract from OZ is inherited by many contracts but is never used

For example, `TapiocaOptionBroker.sol` and `TapiocaOptionLiquidityProvision`