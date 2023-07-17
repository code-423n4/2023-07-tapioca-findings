## initializers could be front run

## No min max limit for setter functions

## Lack of checks-effects-interactions

## Use .call instead of .transfer to send ether

## Tokens with very large decimals will not work as expected

Although not common, it is possible that ERC-20 tokens have decimals() > 36. The current system acknowledges it, but does not prevent anyone from creating a position with them. This will result in the token not working as expected.

## Stake function shouldn’t be accessible, when the status is paused or frozen

he function stake in StRSR.sol is used by users to stake a RSR amount on the corresponding RToken to earn yield and over-collateralize the system. If the contract is in paused or frozen status, some of the main functions payoutRewards, unstake, withdraw and seizeRSR can’t be used. The stake function will keep operating but will skip to payoutRewards, this is problematic considering if the status is paused or frozen and a user stakes without knowing that. He won’t be able to unstake or call any of the core functions, the only option he has is to wait for the status to be unpaused or unfrozen.

## Don’t use payable.transfer()/payable.send()

## require() should be used instead of assert()

## Wrong comment

## Project has NPM Dependency which uses a vulnerable version : @openzeppelin

## Insufficient coverage

## Low-level calls that are unnecessary for the system should be avoided

[L-05] Update codes to avoid Compile Errors
Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:245:2:
    |
245 |   function updateReward (
    |   ^ (Relevant source part starts here and spans across multiple lines).
Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:261:2:
    |
261 |   function updateRewardOnMint (
    |   ^ (Relevant source part starts here and spans across multiple lines).
Warning: Contract code size is 63151 bytes and exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on Mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
   --> contracts/staking/NeoTokyoStaker.sol:190:1:

## Token transfer to address(0) should be avoided

The internal function _beforeTokenTransfer ignores the use of address(0).
As how it is now the two if statements won’t be triggered on address(0) and the function will finish successfully.

## Signatures vulnerable to malleability attacks

ecrecover() accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin’s ECDSA library to perform the extra checks necessary in order to prevent this attack.

## Gas griefing/theft is possible on unsafe external call

] No Storage Gap for BaseSmartAccount and ModuleManager

## Strategy doesn’t have inCaseTokensGetStuck

## Most Yield Farming Strategies interact with other protocols and for this reason they are subject to airdrops.

Without a inCaseTokensGetStuck these extra tokens would not be claimable and would be lost forever

## MaxLoss hardcoded means the contract will revert if any loss bigger than BPS happens

    uint256 public withdrawMaxLoss = 1;
Due to the logic handling of losses, and because of the value being hardcoded, the Strategy may be unable to withdraw until the value is changed.

L - No check for collateral existence







