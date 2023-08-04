# Low severity and QA report
### Low Risk Issues
| |Issue|.|
|-|:-|:-:|
| [L&#x2011;01] Unbounded loop will run out of gas and revert | 1 | 
| [L&#x2011;02] twTAP:: The first voter can mint oTAP position for free | 2 | 
| [L&#x2011;03] The attacker can set the TAP price to zero for 7 days | 3 | 
| [L&#x2011;04] `_getDiscountedPaymentAmount` reverts if calculated with higher decimals than 18 | 4 | 
| [L&#x2011;05] The owner should not be able to set the pool weight to zero | 5 | 
| [L&#x2011;06] The first person to mint shares with an amount lower than 1000 will lose allowance | 6 | 


### Non-critical Issues
| |Issue|.|
|-|:-|:-:|
| [N&#x2011;01] Follow checks effects pattern in `lock()` | 1 | 


## Issues


# [L&#x2011;01] Unbounded loop will run out of gas and revert
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L397

### Vulnerability Details

In `twTAP.sol` when someone calls `advanceWeek` function for the first time after one week (in case that no one called it in first week or first epoch ) to set `lastProcessedWeek` amount, the function then enters in `while (week < goal)` where `week` is `lastProcessedWeek` (which is by the way zero because the value is never set). `week` will be always smaller than `goal` because its value is never updated in the loop. This loop will run until all the gas is consumed then it reverts.

```solidity
    function advanceWeek(uint256 _limit) public {
        // TODO: Make whole function unchecked
        uint256 cur = currentWeek();
        uint256 week = lastProcessedWeek;
        uint256 goal = cur;
        unchecked {
            if (goal - week > _limit) {
                goal = week + _limit;
            }
        }
        uint256 len = rewardTokens.length;
        //@audit week is always smaller than goal because it is never incremented in the loop
        // therefore the function will never exit the loop
        while (week < goal) {
            WeekTotals storage prev = weekTotals[week];
            WeekTotals storage next = weekTotals[++week];
            // TODO: Prove that math is safe
            next.netActiveVotes += prev.netActiveVotes;
            for (uint256 i = 0; i < len; ) {
                next.totalDistPerVote[i] += prev.totalDistPerVote[i];
                unchecked {
                    ++i;
                }
            }
        }
        lastProcessedWeek = goal;
    }
```

```solidity
    function currentWeek() public view returns (uint256) {
        return (block.timestamp - creation) / EPOCH_DURATION;
    }
```

## Impact
advanceWeek funtion will always revert and Users cannot claim any rewards related to twTAP token.

For example:
```solidity
//@audit this function will always return an empty array bc of lastProcessedWeek
Line 166:
    function claimable(
        uint256 _tokenId
    ) public view returns (uint256[] memory) {
        uint256 len = rewardTokens.length;
        uint256[] memory result = new uint256[](len);

        Participation memory position = participants[_tokenId];
        uint256 votes;
        unchecked {
            // Math is safe: Types fit
            votes = uint256(position.tapAmount) * uint256(position.multiplier);
        }

        if (votes == 0) {
            return result;
        }

        // If the "last processed week" is behind the actual week, rewards
        // get processed as if it were earlier.
        uint256 week = lastProcessedWeek;
        if (week <= position.lastInactive) {
            return result;
        }

        .
        .
        .
```
Example 2:
```solidity
//@audit this will always revert bc lastProcessedWeek is zero
    function distributeReward(
        uint256 _rewardTokenId,
        uint256 _amount
    ) external {
        require(
            lastProcessedWeek == currentWeek(),
            "twTAP: Advance week first"
        );

        .
        .
        .
```
## Proof of Concept
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L150
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L397

## Tools Used
Manual Review

## Recommended Mitigation Steps
Make sure the `lastProcessedWeek` value is set successfully.


# [L&#x2011;02] twTAP:: The first voter can mint oTAP position for free
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L252

## Impact

When pool.totalDeposited is still zero, the first person to call `participate` can transfer zero amount and mint an oTAP position. This seems harmless as the oTAP rewards function does indeed check for the amount of tap provided to mint oTAP, also the voting weight will be zero in `participate` . But this is not the intended behaviour either and has unknown consequences. User should not be able to mint oTAP unless they provide some amount.

```solidity
/// @notice Participate in twAMl voting and mint an oTAP position
    function participate(
        address _participant,
        uint256 _amount,
        uint256 _duration
    ) external returns (uint256 tokenId) {
```

```solidity
//@audit in `participate` function
    bool hasVotingPower = _amount >=
        computeMinWeight(pool.totalDeposited, MIN_WEIGHT_FACTOR);
```

```solidity
//@audit will return zero for the first time
    function computeMinWeight(
        uint256 _totalWeight,
        uint256 _minWeightFactor
    ) internal pure returns (uint256) {
        uint256 mul = (_totalWeight * _minWeightFactor);
        return mul >= 1e4 ? mul / 1e4 : _totalWeight;
    }
```

```solidity
//@audit in `participate` function
    tokenId = ++mintedTWTap;
        _safeMint(_participant, tokenId);
```

## Tools Used
Manual Review

## Recommended Mitigation Steps
Check if the `_amount` is bigger than zero.


# [L&#x2011;03] The attacker can set the TAP price to zero for 7 days
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L280
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L413


## Impact
If the owner has not set the oracle during the first epoch, anyone could call `newEpoch()` which starts the first phase of the airdrop and also sets the `epochTAPValuation` to zero. This function cannot be called before the `EPOCH_DURATION` is passed (7 days).

```solidity
    function newEpoch() external {
        require(
            block.timestamp >= lastEpochUpdate + EPOCH_DURATION,
            "adb: too soon"
        );

        // Update epoch info
        lastEpochUpdate = uint64(block.timestamp);
        epoch++;

        // Get epoch TAP valuation
        (, uint256 _epochTAPValuation) = tapOracle.get(tapOracleData);
        epochTAPValuation = uint128(_epochTAPValuation);
        emit NewEpoch(epoch, epochTAPValuation);
    }
```

People who want to exercise their aoTAP position will get their TAP for free.

## Tools Used
Manual Review

## Recommended Mitigation Steps
Check if the oracle exists in `newEpoch()` before getting the price.



# [L&#x2011;04] `_getDiscountedPaymentAmount` reverts if calculated with higher decimals than 18
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L544
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L523

## Impact

`_getDiscountedPaymentAmount` would revert if the owner sets a payment token that has a higher decimal than 18. `ExerciseOption` will not work with the mentioned token temporarily.

```solidity
//@audit reverts if the paymentToken's decimal is bigger than 18
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

## Tools Used
Manual Review

## Recommended Mitigation Steps
Check for the conditions that the added token's decimal is bigger than 18



# [L&#x2011;05] The owner should not be able to set the pool weight to zero
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L259

### Vulnerability Details

```solidity
    function setSGLPoolWEight(
        IERC20 singularity,
        uint256 weight
    ) external onlyOwner updateTotalSGLPoolWeights {
        require(
            activeSingularities[singularity].sglAssetID > 0,
            "tOLP: not registered"
        );
        //@audit should not be zero
        activeSingularities[singularity].poolWeight = weight;

        emit SetSGLPoolWeight(address(singularity), weight);
    }
```

## Tools Used
Manual Review

## Recommended Mitigation Steps
Add a check for zero value.



# [L&#x2011;06] The first person to mint shares with an amount lower than 1000 will lose allowance
### Lines of code
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L210

### Vulnerability Details

```solidity
    function addAsset(
        address from,
        address to,
        bool skim,
        uint256 share
    ) public notPaused allowedLend(from, share) returns (uint256 fraction) {
        _accrue();
        fraction = _addAsset(from, to, skim, share);
    }
```

```solidity
    modifier allowedLend(address from, uint share) virtual {
        _allowedLend(from, share);
        _;
    }
```

```solidity
    function _allowedLend(address from, uint share) internal {
        if (from != msg.sender) {
            if (allowance[from][msg.sender] < share) {
                revert NotApproved(from, msg.sender);
            }
            allowance[from][msg.sender] -= share;
        }
    }
```

```solidity
    function _addAsset(
        address from,
        address to,
        bool skim,
        uint256 share
    ) internal returns (uint256 fraction) {
        Rebase memory _totalAsset = totalAsset;
        uint256 totalAssetShare = _totalAsset.elastic;
        uint256 allShare = _totalAsset.elastic +
            yieldBox.toShare(assetId, totalBorrow.elastic, true);
        fraction = allShare == 0
            ? share
            : (share * _totalAsset.base) / allShare;
            //@audit the first person to mint shares (500) will lose allowance
        if (_totalAsset.base + uint128(fraction) < 1000) {
            return 0;
        }
```

## Tools Used
Manual Review

## Recommended Mitigation Steps
remove the check.



# [N&#x2011;01]Follow checks effects pattern in `lock()`
### Lines of code
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L171

### Vulnerability Details

```solidity
in lock function:
    yieldBox.transfer(msg.sender, address(this), sglAssetID, sharesIn);
    activeSingularities[_singularity].totalDeposited += _amount;
```

```solidity
in unlock function:
    yieldBox.transfer(
            address(this),
            _to,
            lockPosition.sglAssetID,
            sharesOut
        );
        activeSingularities[_singularity].totalDeposited -= lockPosition.amount;
```

## Tools Used
Manual Review

## Recommended Mitigation Steps
It is always better to first do the checks and then effects.
