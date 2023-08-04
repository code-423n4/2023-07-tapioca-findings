# QA Report
## MaxFlashLoan is not compliance with `ERC-3156`
> The maxFlashLoan function MUST return the maximum loan possible for token. If a token is not currently supported maxFlashLoan MUST return 0, instead of reverting.

but `maxFlashLoan` only returns `maxFlashMint`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L57
```solidity
function maxFlashLoan(address) public view override returns (uint256) {
        return maxFlashMint;
    }
```
These would revert if the user tries flashloaning with the amount from any arbtritary token.
These could be problematic with third party integrations.
# Recommendation
could be refactored to:
```solidity
+ function maxFlashLoan(address token) public view override returns (uint256) {
+ 	if (token != address(this)) return 0;
        return maxFlashMint;
    }
```

## `addRewardToken` should check that token does not already exist
https://github.com/Tapioca-DAO/tap-token/blob/main/contracts/governance/twTAP.sol#L466
To avoid duplicate in `RewardsToken[]` a sanity check could be added to ensure token reward has not been added already. 
This could lead to insolvency as rewards may not be enough to distribute to some users, since `claimRewards` loops through this array.

# Recommendation
```diff
function addRewardToken(IERC20 token) external onlyOwner returns (uint256) {
+	require(rewardTokenIndex[token] == 0 && rewardTokens[0] != token, "token already added");
        uint256 i = rewardTokens.length;
        rewardTokens.push(token);
        rewardTokenIndex[token] = i;
        return i;
    }
```