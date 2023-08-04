## [01] Owner set twice and the wrong owner address can be emitted in an event

A number of contract inherit [`BoringOwnable.sol`](https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/BoringOwnable.sol#L17) which sets `owner` to msg.sender in it's constructor and emits an event.

Then a contract like [`Vesting.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L72) will set the owner again, a second time, without emitting an event.

```
    constructor(uint256 _cliff, uint256 _duration, address _owner) {
        require(_duration > 0, "Vesting: no vesting");

        cliff = _cliff;
        duration = _duration;
        owner = _owner;
    }
```

The owner will be set by Vesting.sol but the event from BoringOwnerable.sol will be the `msg.sender` and the `owner` state variable and the event will differ if the addressed passed to `Vesting.sol` is not the same as `msg.sender`.

```
emit OwnershipTransferred(address(0), msg.sender);
```

Note this pattern is repeated in other contracts as well and these should be looked at;

* [`AirdropBroker.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L109)
* [`aoTap.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/aoTAP.sol#L42)
* [`TapiocaOptionBroker.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L94)
* [`TapiocaOptionLiquidityProvision.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L75)
* [`Penrose.sol`](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L94)



## Recommendation

The simplest often is to let `BoringOwnable.sol` set the owner to `msg.sender` and then emit the event and if contracts like `Vesting.sol` want to change it they can call `transferOwnership()`. Alternatively and event could be emitted when the owner is set by `Vesting.sol`.

## [02] UserData `revoked` is always false

Revoked is only ever set to false in [`Vesting.sol`](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L139)

```
    struct UserData {
        uint256 amount;
        uint256 claimed;
        uint256 latestClaimTimestamp;
        bool revoked;
    }
```

## Recommendation
If it's not able to be set to `true` it may not be necessary