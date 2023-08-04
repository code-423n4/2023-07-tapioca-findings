## [L-01] Large transfers may not work with some `ERC20` tokens

Some IERC20 implementations (e.g UNI, COMP) may fail if the valued transferred is larger than uint96. [Source](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers)

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol#L162

```solidity

162        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L359

```solidity

359        IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);

374            IERC20(erc20).safeTransfer(_toAddress, _amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L197

```solidity

197                IERC20(address(this)).safeTransfer(leverageFor, amount);

275            IERC20(erc20).safeTransfer(_toAddress, _amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol

```solidity

206                IERC20(address(this)).safeTransfer(

255            IERC20(tapSendData.tapOftAddress).safeTransfer(from, tapAmount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L165

```solidity

165                IERC20(address(this)).safeTransfer(onBehalfOf, amount);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol#L140

```solidity

140            IERC20(tokenOut).safeTransfer(to, amountOut);

```

https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol#L148

```solidity

148        IERC20(contractAddress).safeTransfer(to, amount);

```

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L423

```solidity

423            IERC20(address(singularity)).safeTransferFrom(

479            IERC721(oTapAddress).safeTransferFrom(

532                IERC721(oTapAddress).safeTransferFrom(

543                IERC721(oTapAddress).safeTransferFrom(

797        IERC20(_token).safeTransferFrom(_from, address(this), _amount);

```

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol#L148

```solidity

148            IERC20(erc20).safeTransfer(msg.sender, _amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol#L182

```solidity

182                IERC20(address(this)).safeTransfer(leverageFor, amount);

```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L180

```solidity

180                IERC20(address(this)).safeTransfer(
```

## [L-02] Unfair Fund Lockup Due to Withdrawal Pausing

The current implementation of the `withdrawAllMarketFees` function poses a concern regarding user access to their funds. When functions are paused, this function becomes inaccessible, leading to a lockup of users' funds until the contracts are unpaused. This restriction might be perceived as unfair by users who depend on timely access to their funds.

Pausing the `withdrawAllMarketFees` function locks users' funds until the contracts are unpaused, potentially causing financial inconvenience and dissatisfaction among affected users. This limitation could negatively impact user trust and satisfaction with the platform.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L232-L248

```solidity
  function withdrawAllMarketFees(
        IMarket[] calldata markets_,
        ISwapper[] calldata swappers_,
        IPenrose.SwapData[] calldata swapData_
    ) public notPaused {
        require(
            markets_.length == swappers_.length &&
                swappers_.length == swapData_.length,
            "Penrose: length mismatch"
        );
        require(address(swappers_[0]) != address(0), "Penrose: zero address");
        require(address(markets_[0]) != address(0), "Penrose: zero address");

        _withdrawAllProtocolFees(swappers_, swapData_, markets_);

        emit ProtocolWithdrawal(markets_, block.timestamp);
    }
```

## [L-03] Contract Lifespan Reduction Due to Truncating block.timestamp

They are multiple functions that truncate `block.timestamp` to `uint64` data type, which can lead to a reduction in the contract's lifespan. Truncating the timestamp in these functions might cause unintended behavior and inaccurate calculations that rely on precise time values.

By casting `block.timestamp` to `uint64`, the contract's precision is limited, leading to potential inaccuracies in interest rate calculations, epoch updates, and other time-dependent operations. This could result in unexpected contract behavior and shorten the overall lifespan of the contract.

`newEpoch` function, specifically the truncation of `block.timestamp` when updating `lastEpochUpdate`.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol#L280-L288

```solidity
    function newEpoch() external {
        require(
            block.timestamp >= lastEpochUpdate + EPOCH_DURATION,
            "adb: too soon"
        );

        // Update epoch info
        lastEpochUpdate = uint64(block.timestamp);
        epoch++;
```

`_accrue` function, truncating `block.timestamp` when updating debtRate and `lastAccrued`.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L512-L523

```solidity
function _accrue() internal override {
        IBigBang.AccrueInfo memory _accrueInfo = accrueInfo;
        // Number of seconds since accrue was called
        uint256 elapsedTime = block.timestamp - _accrueInfo.lastAccrued;
        if (elapsedTime == 0) {
            return;
        }
        //update debt rate
        uint256 annumDebtRate = getDebtRate();
        _accrueInfo.debtRate = uint64(annumDebtRate / 31536000); //per second

        _accrueInfo.lastAccrued = uint64(block.timestamp);
```

`_getInterestRate` function, truncating `block.timestamp` when updating `lastAccrued`.

```solidity
            );
        }
        _accrueInfo.lastAccrued = uint64(block.timestamp);

        if (_totalBorrow.base == 0) {
```

## [L-04] ERC20 Token Approval Zero First

The MarketERC20 contract contains a function named `_approve`, which lacks the necessary logic to handle the special behavior required by certain tokens, such as Tether (USDT). Tokens like Tether revert the approve() function when attempting to change the allowance from an existing non-zero value. This behavior is implemented to protect against potential front-running changes of approvals.

Due to the missing logic in the `_approve` function, the token contract may not correctly handle approval changes when the current allowance is non-zero. This can lead to issues when interacting with certain tokens, causing transactions to revert and preventing users from updating their approvals as intended.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L295C1-L298C6

```solidity
    function _approve(address owner, address spender, uint256 amount) internal {
        allowance[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }
```

## [L-05] `tokenURI` should throw an error if `_tokenId` is not a valid NFT

The `tokenURI` function in the `aoTAP.sol` and `oTAP.sol` contracts don't adhere to the EIP-721 standard and the metadata extension, which specifies that the function should throw an error if the `_tokenId` provided does not correspond to a valid NFT.

The current implementation returns an empty string for invalid NFTs, leading to inconsistent behavior and potential confusion for users and applications.

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/aoTAP.sol#L68-L72

```solidity
 function tokenURI(
        uint256 _tokenId
    ) public view override returns (string memory) {
        return tokenURIs[_tokenId];
    }
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L54-L58

```solidity
 function tokenURI(
        uint256 _tokenId
    ) public view override returns (string memory) {
        return tokenURIs[_tokenId];
    }
```

### Recommendation

Modify the tokenURI function to include the necessary validation to check if the \_tokenId corresponds to a valid NFT. If the token does not exist, the function should throw an error using a suitable revert message, as specified in the EIP-721 standard.
