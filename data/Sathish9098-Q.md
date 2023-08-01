# LOW FINDINGS

## 

## [L-1] ``setBorrowOpeningFee()`` function to disallow Execution during market ``paused ``

### Impact
The ``setBorrowOpeningFee()`` function allows the owner of the contract to set the borrowing opening fee. This fee is charged when a user borrows an asset from the contract. If the fee is changed while the market is paused, users who are trying to buy back their assets after the crash will be forced to pay a higher fee. This could result in significant losses for users.

### POC
```solidity

    function setBorrowOpeningFee(uint256 _val) external onlyOwner {
        require(_val <= FEE_PRECISION, "Market: not valid");
        emit LogBorrowingFee(borrowOpeningFee, _val);
        borrowOpeningFee = _val;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L142-L146

### Recommended Mitigation
Use ``notPaused`` modifier to ensure market is not paused before changing the fee

##

## [L-2] Lack of ``MIN`` and ``MAX`` value checks in setter functions when assigning critical values

### Impact
The lack of MIN and MAX value checks in setter functions when assigning values to uint256 can be a security risk. This is because it allows users to set values that are outside of the valid range for uint256. This could lead to a number of problems, including

### POC

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

function setBorrowOpeningFee(uint256 _val) external onlyOwner {
        require(_val <= FEE_PRECISION, "Market: not valid");
        emit LogBorrowingFee(borrowOpeningFee, _val);
        borrowOpeningFee = _val;
    }

function setBorrowCap(uint256 _cap) external notPaused onlyOwner {
        emit LogBorrowCapUpdated(totalBorrowCap, _cap);
        totalBorrowCap = _cap;
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L142-L154

### Recommended Mitigation
Add ``MIN`` and ``MAX``, ``>0`` value checks to setter functions to avid unexpected behavior 

##

## [L-3] Unused ``onlyOnce()`` modifier in ``Market.sol`` contract

### Impact

In the ``Market.sol`` contract, there exists an unused modifier named ``onlyOnce()``. This modifier was likely implemented with the intention of ensuring that certain functions can only be executed once, preventing potential misuse or unintended behavior. However, upon closer inspection of the contract's code, it becomes apparent that this modifier is not applied to any of the contract's functions, rendering it ineffective and unnecessary.

### POC

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L130-L134

### Recommended Mitigation
The contract maintainers can either remove the ``onlyOnce()`` modifier or refactor the contract to incorporate it where appropriate.

##

## [L-4] Add parameter to event-emit to OracleDataUpdated(),OracleUpdated()

### Impact
Some event-emit description hasn’t parameter. Add to parameter for front-end website or client app , they can has that something has happened on the blockchain.

### POC

```solidity
FILE: tapioca-bar-audit/contracts/markets/Market.sol

179: emit OracleUpdated();

184: emit OracleDataUpdated();

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L184

##

## [L-5] ``maxLiquidatorReward``,``minLiquidatorReward`` check not allow exact value. <=,>= should be used instead of ``<,>``

### Impact
The ``maxLiquidatorReward`` and ``minLiquidatorReward`` variables in the ``Market.sol`` contract do not allow exact values. This is because the require() statements that check the values use the < and > operators. These operators will not allow the values to be equal to the specified value.

```
/// @notice min % a liquidator can receive in rewards
    uint256 public minLiquidatorReward = 1e3;

/// @notice max % a liquidator can receive in rewards
uint256 public maxLiquidatorReward = 1e4; 

```

So as per protocol docs should accept the values are equal to ``minLiquidatorReward ``,``maxLiquidatorReward ``. But current require check only allow less than and greater than values. 

### POC
```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

212: require(
                _minLiquidatorReward < maxLiquidatorReward,
                "Market: not valid"
            );

221: require(
                _maxLiquidatorReward > minLiquidatorReward,
                "Market: not valid"
            );

``` 
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L221

### Recommended Mitigation
``require()`` statements to use the <= and >= operators, the maxLiquidatorReward and minLiquidatorReward variables will be able to allow exact values

##

## [L-6] There is no clear docs why difference checks `` _callerFee <= FEE_PRECISION`` and ``_liquidationBonusAmount < FEE_PRECISION`` 

### Impact
The disparity in using <= and < for certain checks in the documentation is not adequately explained and may cause confusion for developers. Some checks utilize <=, while others use <, without providing a clear rationale for the distinction between the two comparison operators.Using different operators for similar checks without proper documentation can lead to misunderstandings and errors in the contract's behavior

### POC

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

193: require(_callerFee <= FEE_PRECISION, "Market: not valid");
198: require(_protocolFee <= FEE_PRECISION, "Market: not valid");
203: require(
                _liquidationBonusAmount < FEE_PRECISION,
                "Market: not valid"
            );
211: require(_minLiquidatorReward < FEE_PRECISION, "Market: not valid");

```

### Recommended Mitigation
Clearly document why < and <= in different places 

##

## [L-7] ``val != paused `` check could be a problem if the market needs to be ``paused`` or ``unpaused`` for a short period of time

### Impact
The ``updatePause()`` function in the ``Market.sol`` contract requires that the new paused state be different from the current paused state. This means that the function cannot be used to simply toggle the paused state.
This could be a problem if the market needs to be paused or unpaused for a short period of time. For example, if the market needs to be paused to perform maintenance, then the updatePause() function would not be able to be used to simply toggle the paused state. Instead, the function would have to be called twice, once to pause the market and once to unpause the market

### POC
```solidity
FILE: tapioca-bar-audit/contracts/markets/Market.sol

function updatePause(bool val) external {
        require(msg.sender == conservator, "Market: unauthorized");
        require(val != paused, "Market: same state");
        emit PausedUpdated(paused, val);
        paused = val;
    }

```

##

## [L-8] ``computeTVLInfo()``function does not handle the case where the ``collateralAmountInAsset`` variable is zero 

### Impact
This could happen if the user's collateral is worth less than the amount they have borrowed. In this case, the function would return incorrect values

### POC

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

318: uint256 collateralAmountInAsset = _computeMaxBorrowableAmount(
            user,
            _exchangeRate
        );

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L318

### Recommended Mitigation
Handle the case where the ``collateralAmountInAsset`` variable is zero. This would help to ensure that correct values are returned in all cases.

## 

## [L-9] Unnecessary return values ``updated,rate`` in updateExchangeRate() function

### Impact
The return values (updated, rate) from the ``updateExchangeRate()`` function are unnecessary and not being used in the calling function. Since the solvent modifier does not use the return values, there is no need to return them from the ``updateExchangeRate()`` function.

```solidity
FILE: tapioca-bar-audit/contracts/markets/Market.sol

function updateExchangeRate() public returns (bool updated, uint256 rate) {
        (updated, rate) = oracle.get("");

        if (updated) {
            require(rate > 0, "Market: invalid rate");
            exchangeRate = rate;
            emit LogExchangeRate(rate);
        } else {
            // Return the old rate if fetching wasn't successful
            rate = exchangeRate;
        }
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L340

### Recommended Mitigation

```diff

- function updateExchangeRate() public returns (bool updated, uint256 rate) {
+ function updateExchangeRate() public {
+        bool updated;
+        uint256 rate;
        (updated, rate) = oracle.get("");

        if (updated) {
            require(rate > 0, "Market: invalid rate");
            exchangeRate = rate;
            emit LogExchangeRate(rate);
        } else {
            // Return the old rate if fetching wasn't successful
            rate = exchangeRate;
        }
    }

```

##

## [L-10] If the ``reward`` is negative values then ``uint256(reward)`` conversion may returns unexpected results 

### Impact

There is a possibility that the ``diff``,``reward`` calculation can result in a negative value .
When you convert a negative signed integer to an unsigned integer (uint256), the two's complement representation can result in a very large positive number. The magnitude of the number will depend on the original negative value and the bit width of the unsigned integer.

uint256(-12) = 0xfffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff4

### POC

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/Market.sol

461: return uint256(reward);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L461

### Recommended Mitigation
 Include require check to avoid negative values 

```
require(reward >= 0, "Reward must be non-negative");

```





## initializers could be front run

## Lack of checks-effects-interactions

## Use .call instead of .transfer to send ether

## Is this possible receiver possible to receive nft, is NFT Ownership checked ? Is tokenid generated right way ? 

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

L-01 Incorrect Natspec

 Inconsistent usage of variable naming for mint and burn functions (amount vs. value)

 function mint(address account, uint256 amount) external returns (bool);

    /**
     * @notice Function to burn tokens in the Branch Chain.
     * @param value Amount of tokens to be burned.
     */
    function burn(uint256 value) external;

Mixed usage of uint24 and uint256 for chain ids. See examples below:

Unnecessary single-line comment of multiple lines of already existing multi-line comment

Add to blacklist function

As stated in the project:

- Is it an NFT?: true
NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
Recommended Mitigation Steps
Add to Blacklist function and modifier.


Consider the case where totalsupply is 0

decimals() not part of ERC20 standard

The Contract Should approve(0) First

Storage Write Removal Bug On Conditional Early Termination
See the following for more info:

https://twitter.com/solidity_lang/status/1567953562151579650?s=20&t=fXIo4hRjOiMXl2dqpD5Oyw

https://blog.soliditylang.org/2022/09/08/storage-write-removal-before-conditional-termination/

Proof Of Concept
File: GovernorBravoDelegateMaia.sol

536: function getChainIdInternal() internal view returns (uint256) {
        uint256 chainId;
        assembly {
            chainId := chainid()
        }
        return chainId;
    }
}
https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L536

Recommended Mitigation Steps
Upgrade pragma to at least version 0.8.17



There may be problems with the use of Layer2
According to the scope information of the project, it is stated that it can be used in rollup chains and popular EVM chains.

README.md:
  797  - Is it a fork of a popular project?:   true
  798: - Does it use rollups?:   true
  799: - Is it multi-chain?:  true
  800: - Does it use a side-chain?: true
Some conventions in the project are set to Pragma ^0.8.19, allowing the conventions to be compiled with any 0.8.x compiler. The problem with this is that Arbitrum is Compatible with 0.8.20 and newer. Contracts compiled with these versions will result in a non-functional or potentially damaged version that does not behave as expected. The default behavior of the compiler will be to use the latest version which means it will compile with version 0.8.20 which will produce broken code by default.



