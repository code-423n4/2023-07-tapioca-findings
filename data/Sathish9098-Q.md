# LOW FINDINGS

## 

## [L-1] Lack of checks-effects-interactions (CEI) when transfer 

### Impact
The code not following the ``CEI`` pattern because the ``totalAsset.elastic`` variable is modified after the ``yieldBox.transfer`` function is called. This means that if an attacker were to call the withdraw function from within another function, they could modify the ``totalAsset.elastic`` variable before the ``yieldBox.transfer`` function has finished executing.

### POC

```solidity
FILE: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol

193:    yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
194:
195:    totalAsset.elastic += uint128(returnedShare - callerShare);


281:  yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
282:  yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
283:
284:  totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L193-L195

```solidity
FILE: Breadcrumbstap-token-audit/contracts/tokens/LTap.sol

41:   function deposit(uint256 amount) external {
42:        tapToken.transferFrom(msg.sender, address(this), amount);
43:        _mint(msg.sender, amount);
44:    }

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L41-L44


### Recommended Mitigation
Change code as per ``CEI`` Pattern

```diff
FILE: tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol

+ 195:    totalAsset.elastic += uint128(returnedShare - callerShare);
193:    yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
194:
- 195:    totalAsset.elastic += uint128(returnedShare - callerShare);


+ 284:  totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);
281:  yieldBox.transfer(address(this), penrose.feeTo(), assetId, feeShare);
282:  yieldBox.transfer(address(this), msg.sender, assetId, callerShare);
283:
- 284:  totalAsset.elastic += uint128(returnedShare - feeShare - callerShare);

```
##

## [L-2] Add to blacklist function

As stated in the project:

```
Tapioca ERC20/ERC721 contracts uses the LayerZero OFTv2 and ONFT721 contracts.

```
NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

```solidity

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }

```

Recommended Mitigation Steps
Add to Blacklist function and modifier.

##

## [L-3] There is a risk that the ``borrowOpeningFee``,``totalBorrowCap ``,``poolFee `` variables is accidentally initialized to 0 and platform loses money

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

```solidity
FILE: tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol

61: function setPoolFee(uint24 _newFee) external onlyOwner {
62:        emit PoolFee(poolFee, _newFee);
63:        poolFee = _newFee;
64:    }

```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV3Swapper.sol#L61-L64

### Recommended Mitigation
Add ``MIN`` and ``MAX``, ``>0`` value checks to setter functions to avid unexpected behavior 

##

## [L-4] ``maxLiquidatorReward``,``minLiquidatorReward`` check not allow exact value. <=,>= should be used instead of ``<,>``

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

## [L-5] There is no clear docs why ``<=`` and ``<`` when checking FEE_PRECISION `` _callerFee <= FEE_PRECISION`` and ``_liquidationBonusAmount < FEE_PRECISION`` 

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

## [L-6] ``val != paused `` check could be a problem if the market needs to be ``paused`` or ``unpaused`` for a short period of time

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

## [L-7] ``computeTVLInfo()``function does not handle the case where the ``collateralAmountInAsset`` variable is zero 

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

## [L-8] Unnecessary or unused return values ``updated, rate`` in updateExchangeRate() function

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

## [L-9] If the ``reward`` is negative values then ``uint256(reward)`` conversion may returns unexpected results 

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

##

## [L-10] Avoid shadowing variables. ``ids``variable is shadowed 

### Impact
Variable shadowing can affect the protocol in DeFi in a number of ways, especially when state and function parameters have the same name. For example, if a function parameter has the same name as a state variable, and the function modifies the parameter, then the state variable will also be modified. This can lead to unexpected behavior, such as funds being transferred to the wrong address

State variable and parameter names are same 

### POC

```solidity
FILE: YieldBox/contracts/YieldBox.sol

316: function _transferBatch(address from, address to, uint256[] calldata ids, uint256[] calldata values) internal override {

```
https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L316

```solidity
FILE: YieldBox/contracts/AssetRegister.sol

30: mapping(TokenType => mapping(address => mapping(IStrategy => mapping(uint256 => uint256)))) public ids;

```
https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/AssetRegister.sol#L30

### Recommended Mitigation
Choose different name for ``ids`` in ``_transferBatch`` function 

##

## [L-11] NFT ``_tOLPTokenID`` should be generated in a incrementing way

### Impact
Generate NFT token IDs in an incrementing way instead of allowing users to define their own values. This is because user-defined token IDs can be easily manipulated, and they can also be used to create duplicate or invalid tokens.

When ever mint new NFT tokenid should be incremented by one instead of user defined tokenId's

### POC

```solidity
FILE: tap-token-audit/contracts/options/TapiocaOptionBroker.sol

  function participate(
        uint256 _tOLPTokenID
    ) external returns (uint256 oTAPTokenID) {

 // Mint oTAP position
        oTAPTokenID = oTAP.mint(
            msg.sender,
            lock.lockTime + lock.lockDuration,
            uint128(target),
            _tOLPTokenID
        );

```
### Recommended Mitigation
Use state variable generate tokenId. Increment 1 for every new mint 

##

## [L-12] ``TapiocaOptionBroker.sol`` contract inherits ``Pausable`` but not implemented any pause mechanism

### Impact
The ``TapiocaOptionBroker.sol`` contract inherits from the ``Pausable`` contract, but it does not implement any pause mechanism. This means that the contract cannot be paused, and all of the functions in the contract are always available to be called

### POC
```solidity
FILE: Breadcrumbstap-token-audit/contracts/options/TapiocaOptionBroker.sol

53: contract TapiocaOptionBroker is Pausable, BoringOwnable, TWAML {

````
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L53

### Recommended Mitigation
Use pause mechanism or remove safely 

##

## [L-13] ``addCollateral()``addAsset() should be allowed even when contract ``paused`` 

### Impact
The ``addCollateral`` function should be allowed even when the contract is paused. This is because if the liquidation threshold is reached and users positions are liquidated immediately after unpaused, then it is important to allow users to add collateral to prevent their positions from being liquidated.

If the addCollateral function was not allowed when the contract was paused, then users would not be able to add collateral to their positions, and their positions would be liquidated. This would result in users losing their funds, which would be unfair and could damage the reputation of the contract

### POC

```solidity
FILE: Breadcrumbstapioca-bar-audit/contracts/markets/bigBang/BigBang.sol

function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public allowedBorrow(from, share) notPaused {

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L282-L288

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/Singularity.sol#L204-L209

### Recommended Mitigation
Allow addCollateral() even contract ``paused ``

##

## [L-14] Project has NPM Dependency which uses a vulnerable version : @openzeppelin

### Impact
Protocol uses the vulnerable versions of openzeppelin,

Known vulnerabilities 

#### openzeppelin 4.8.2 known vulnerabilities 

 - Improper Input Validation
 - Missing Authorization
 - Denial of Service (DoS)

#### openzeppelin 4.5.0 known vulnerabilities 

 - Missing Authorization
 - Denial of Service (DoS)
 - Improper Input Validation
 - Improper Verification of Cryptographic Signature
 - Incorrect Calculation
 - Information Exposure

### POC

```solidity

Package.json

- YieldBox -  ``@openzeppelin/contracts": "^4.8.2"`` versions

- tapioca-yieldbox-strategies-audit - ``"@openzeppelin/contracts": "^4.5.0"``

- tapioca-periph-audit - `` "@openzeppelin/contracts": "^4.5.0",``

- tapioca-bar-audit - `` "@openzeppelin/contracts": "^4.8.2",``

```

### Recommended Mitigation
Use latest ``openzeppelin`` version ``V4.9.3 ``

##

## [L-15] Token transfer to ``address(0)`` should be avoided in ``removeCollateral()`` function

### Impact
Token transfer to ``address(0)`` should be avoided. This is because ``address(0)`` is the "null address", which is a special address that does not belong to any user. If tokens are transferred to address(0), they are effectively lost.

There are a few reasons why token transfer to address(0) should be avoided

 - Tokens transferred to address(0) are effectively lost
 - Token transfer to address(0) can be used to exploit re-entrancy vulnerabilities
 - Token transfer to address(0) can be used to spam the blockchain

### POC

```solidity
FILE: tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol

function removeCollateral(
        address from,
        address to,
        uint256 share
    ) public notPaused solvent(from) allowedBorrow(from, share) {
        _removeCollateral(from, to, share);
    }

 function _removeCollateral(
        address from,
        address to,
        uint256 share
    ) internal {
        userCollateralShare[from] -= share;
        totalCollateralShare -= share;
        emit LogRemoveCollateral(from, to, share);
        yieldBox.transfer(address(this), to, collateralId, share);
    }


```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L709-L718

### Recommended Mitigation
Add address(0) check before calling transfer function 

```solidity

require (to != address(0), "null address");

```

##

## [L-16] ``signer`` should be checked with address(0) before ``signer == owner`` check in ``permit()``,``permitAll()`` functions

### Impact
It is always a good practice to be as safe as possible. By checking if the signer is not address(0) before you compare it to the owner of the contract, you can add an extra layer of security to your contract. This will help to prevent even the most sophisticated malicious actors from exploiting your contract

### POC

```solidity
FILE: YieldBox/contracts/YieldBoxPermit.sol

86: address signer = ECDSA.recover(hash, v, r, s);
87: require(signer == owner, "YieldBoxPermit: invalid signature");

```
https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L86-L87

### Recommended Mitigation
Add address(0) check

```diff
FILE: YieldBox/contracts/YieldBoxPermit.sol

86: address signer = ECDSA.recover(hash, v, r, s);
+  require(signer != address(0), "Invalid signer ");
87: require(signer == owner, "YieldBoxPermit: invalid signature");

```

##

## [L-17] Recommended to include the ``chain ID`` in the ``_PERMIT_TYPEHASH``,``_PERMIT_ALL_TYPEHASH `` , to prevent malicious actors from ``forging signatures`` on ``other networks``

### Impact
The chain ID is a number that identifies the Ethereum network that the transaction was submitted to. This number is used to prevent malicious actors from forging signatures on other networks.

If you do not include the chain ID in the type hash, then your contract will still be able to verify signatures. However, it will be more vulnerable to signature forgery.

It is recommended to include the chain ID in the type hash, even if it is not strictly necessary. This will help to improve the security of your contract and prevent malicious actors from forging signatures

### POC

```solidity
FILE: YieldBox/contracts/YieldBoxPermit.sol

27: bytes32 private constant _PERMIT_TYPEHASH =
        keccak256("Permit(address owner,address spender,uint256 assetId,uint256 nonce,uint256 deadline)");
    

31: bytes32 private constant _PERMIT_ALL_TYPEHASH =
        keccak256("PermitAll(address owner,address spender,uint256 nonce,uint256 deadline)");
```
https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol#L27-L32

### Recommended Mitigation
Include the chain ID in the ``_PERMIT_TYPEHASH``,``_PERMIT_ALL_TYPEHASH `` 

##

## [L-18] ``Constants`` with ``default visibility`` should be avoided. Declare constant with appropriate visibility to avoid unexpected behavior

### Impact
This is because constants with default visibility are visible to contracts in the same scope, which can lead to unexpected behavior. If this is already planned please clearly explain this with contract docs. 

### POC

```solidity
FILE: tap-token-audit/contracts/governance/twTAP.sol

81:    uint256 constant MIN_WEIGHT_FACTOR = 10; // In BPS, 0.1%
82:    uint256 constant dMAX = 100 * 1e4; // 10% - 100% voting power multiplier
83:    uint256 constant dMIN = 10 * 1e4;
95: uint256 constant DIST_PRECISION = 2 ** 128;

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L81-L83

### Recommended Mitigation
Constants should be declared with appropriate visibility.

##

## [L-19] Incorrect title 

The title suggests its public function but external function implemented 

### POC

```solidity
FILE: tapiocaz-audit/contracts/TapiocaWrapper.sol


// ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    /// @notice Harvest fees from all the deployed TOFT contracts. Fees are transferred to the owner.
    function harvestFees() external {
        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {
            harvestableTapiocaOFTs[i].harvestFees();
        }
        emit HarvestFees(msg.sender);
    }

```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L92-L102





When ever we using ERC4494.sol we need to consider this 
Security Considerations

Extra care should be taken when creating transfer functions in which permit and a transfer function can be used in one function to make sure that invalid permits cannot be used in any way. This is especially relevant for automated NFT platforms, in which a careless implementation can result in the compromise of a number of user assets.

The remaining considerations have been copied from ERC-2612 with minor adaptation, and are equally relevant here:

Though the signer of a Permit may have a certain party in mind to submit their transaction, another party can always front run this transaction and call permit before the intended party. The end result is the same for the Permit signer, however.

Since the ecrecover precompile fails silently and just returns the zero address as signer when given malformed messages, it is important to ensure ownerOf(tokenId) != address(0) to avoid permit from creating an approval to any tokenId which does not have an approval set.

Signed Permit messages are censorable. The relaying party can always choose to not submit the Permit after having received it, withholding the option to submit it. The deadline parameter is one mitigation to this. If the signing party holds ETH they can also just submit the Permit themselves, which can render previously signed Permits invalid.

The standard ERC-20 race condition for approvals applies to permit as well.

If the DOMAIN_SEPARATOR contains the chainId and is defined at contract deployment instead of reconstructed for every signature, there is a risk of possible replay attacks between chains in the event of a future chain split.



Implement reentrancy protection in critical functions like addCollateral, removeCollateral, borrow, repay, and others using the nonReentrant modifier bigbang.sol

