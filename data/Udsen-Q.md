## 1. THERE IS NO CHECK FOR `address(0)` ON THE `ECDSA.recover` RETURN VALUE

The `YieldBoxPermit.permit` function is used to allow `ERC721` token approvals to be made using a signature.
Here the `YieldBoxPermit.permit` and `YieldBoxPermit.permitAll` function uses the `ECDSA.recover` function to recover the `signer` address from the `hash of the signed message` and provided `signature`. The `ECDSA.recover` function returns `address(0)` if the signature provided is `invalid`. But this check is not performed in the `permit` and `permitAll` functions. 

Hence a maliciouse user can set the `owner` address as `address(0)` and provide an invalid signature (v,r,s) to the `permit` function to bypass the following conditional check: (because `signer == owner == address(0)`)

        require(signer == owner, "YieldBoxPermit: invalid signature");

As a result following two functions can set the `owner` as `address(0)` and `spender` as `msg.sender` thus by allowing the `msg.sender` to spend any funds allocated to `address(0)`.

        _setApprovalForAsset(owner, spender, assetId, true);

        _setApprovalForAll(owner, spender, true);

It is recommended to add the following conditional check inside the `permit` and `permitAll` functions.

        address signer = ECDSA.recover(hash, v, r, s);
        require(signer != address(0), "Invalid signer");

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxPermit.sol#L57-L58
https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxPermit.sol#L86-L87
 
## 2. RECOMMENDED TO ADD INPUT VALIDATION OF `address(0)` FOR CRITICAL ADDRESS STATE VARIABLE ASSIGNMENTS

The critical address state variables are set in the `Penrose.constructor` with out input validation for `address(0)`. It is recommended to perform the `address(0)` checks for these critical address assignments.

        yieldBox = _yieldBox;
        tapToken = tapToken_;
        owner = _owner;

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L92-L94
https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L75

## 3. DISCREPENCY IN INPUT VALIDATION BETWEEN `setter` AND `setMarketConfig` FUNCTIONS

In the `Market.setMarketConfig` function, when `totalBorrowCap` and `borrowOpeningFee` is set the following conditional checks are performed respectively to verify that the values are `> 0`.

        if (_totalBorrowCap > 0) {

        if (_borrowOpeningFee > 0) {

But in the setter functions for the above two state variables, the above two checks are not performed as shown below:

    function setBorrowCap(uint256 _cap) external notPaused onlyOwner {
        emit LogBorrowCapUpdated(totalBorrowCap, _cap);
        totalBorrowCap = _cap;
    }

    function setBorrowOpeningFee(uint256 _val) external onlyOwner {
        require(_val <= FEE_PRECISION, "Market: not valid");
        emit LogBorrowingFee(borrowOpeningFee, _val);
        borrowOpeningFee = _val;
    }

Hence it is recommended to add the `> 0` checks in the `setter` functions of the above two state variables.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L228-L231
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L171-L175
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L151-L154
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol#L142-L146

## 4. FLASH MINT FEE CAN BE SET TO ZERO THUS LETTING BORROWERS BORROW FREE OF CHARGE

The `BaseUSDO.setFlashMintFee` function is used to set the flashloan fee. The following check is performed to verify the validity of the passed in `_val` value:

        require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");

But there is no check to verify that `_val > 0`. Hence by mistake the `onlyOwner` can set the `flashMintFee` state variable to the default value of `0`. If `flashMintFee` is set to zero it will allow flash borrowers to flash mint the maximum amount (`maxFlashMint`) of `USDO` tokens with out any `fee`. This will be loss of funds to the protocol.

Hence it is recommended to add the following check to the `BaseUSDO.setFlashMintFee` function to verify that `_val > 0`.

    function setFlashMintFee(uint256 _val) external onlyOwner {
        require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");
        require(_val > 0, "Fee can not be zero");
        emit FlashMintFeeUpdated(flashMintFee, _val);
        flashMintFee = _val;
    }

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol#L96-L100

## 5. REENTRANCY VULNEARBILITY IN THE `TapiocaOptionLiquidityProvision.lock` FUNCTION

The `TapiocaOptionLiquidityProvision.lock` function is vulnerable to reentrancy via the `_safeMint` function as shown below:

        _safeMint(_to, tokenId);

There are state changes happening after this function call as well as shown below:

        LockPosition storage lockPosition = lockPositions[tokenId];
        lockPosition.lockTime = uint128(block.timestamp);
        lockPosition.sglAssetID = uint128(sglAssetID);
        lockPosition.lockDuration = _lockDuration;
        lockPosition.amount = _amount;

Hence it is recommended to follow the `CEI` standard or implemement `nonReentrancy guard` in the `TapiocaOptionLiquidityProvision.lock` function to mitigate this vulnerablity.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L191-L198

## 6. LACK OF INPUT VALIDATION IN THE `TapiocaOptionLiquidityProvision.setSGLPoolWEight` FUNCTION

The `TapiocaOptionLiquidityProvision.setSGLPoolWEight` function is used to set the pool weight of a given singularity market. The given `weight` is set as shown below:

        activeSingularities[singularity].poolWeight = weight;

But there is no input validation to verify that `weight > 0`. But when the singularity market weight is set in the `TapiocaOptionLiquidityProvision.registerSingularity` there is an explicit check to verify that `weight > 0` as shown below:

        activeSingularities[singularity].poolWeight = weight > 0 ? weight : 1;

Hence there is a discrepncy in the logic implementation of the two functions. Hence it is recommended to add the following check in the `setSGLPoolWEight` function to verfiy that the `weight > 0`.

    require(weight > 0, "weight can not be zero");

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L267

## 7. TYPO ERROR IN THE FUNCTION NAMING OF THE `setSGLPoolWEight` FUNCTION

It is best practice make the first letter of each word `uppercase` when naming a function in solidity smart contracts. This is the accepted standard used by the developers in the industry. But there is a typo error in the `setSGLPoolWEight` function name.

Hence this function name should be corrected as `setSGLPoolWeight` for better readabilitiy.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol#L259

## 8. RECOMMENDED TO USE THE `safeTransferFrom` INPLACE OF `transferFrom` FOR ERC721 TOKEN TRANSFER

The `TapiocaOptionBroker.exitPosition` function is used to exit a twAML participation and delete the voting power if existing. At the end of the function execution position is transfered back to the `oTAP owner` as shown below:

        tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);

It is recommended to use the `safeTransferFrom` function of the `ERC721` instead of using the `transferFrom`. Since this function uses the `CEI` reentrancly will not be an issue. In the `TapiocaOptionBroker.participate` function, the `oTAP.mint` function uses the `_safeMint(_to, tokenId)` function to mint the ERC721 token to the `recipient`. Hence it can be assumed that the recipient implements the `onERC1155Received` function. 

Hence it is recommended to use the `safeTransferFrom` function in the `TapiocaOptionBroker.exitPosition` function as shown below:

        tOLP.safeTransferFrom(address(this), otapOwner, oTAPPosition.tOLP);

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol#L342

## 9. MAGIC NUMBERS CAN BE REPRESENTED IN SHORTER FORMAT FOR EASE OF READABILITY

In the `TapOFT` function the `decay_rate` state variable is denoted as shown below:

    uint256 constant decay_rate = 8800000000000000;

But for ease of readability it can be represented in a shorter format as follows:

    uint256 constant decay_rate = 88e14;

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol#L45    

## 10. WRONG NATSPEC COMMENTS

    /// @notice Level up cross-chain: Borrow more and `buy` collateral with it.
The above comment should be corrected as follows:
    /// @notice Level up cross-chain: Borrow more and `sell` collateral with it.

Singularity.sol#L403

        // Calculte the shares using `te` current amount to share ratio
The above comment should be corrected as follows:
        // Calculte the shares using `the` current amount to share ratio        

https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol#L31
