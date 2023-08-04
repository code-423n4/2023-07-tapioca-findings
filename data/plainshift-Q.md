## BigBang toBurn redundancy
### Impact
Calculating `toBurn` as `amount - toWithdraw` (https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L731) is redundant, as `toBurn` can just be set to `part` which would result in the same value.

### Proof of Concept

```solidity
uint256 toWithdraw = (amount - part); //acrrued
uint256 toBurn = amount - toWithdraw;
```

`toBurn` is just `part`.

### Recommended Mitigation Steps
Change `uint256 toBurn = amount - toWithdraw;`  to `uint256 toBurn = part;`

## USDO doesn't conform to ERC-3156
### Impact
USDO utilizes to ERC-3156 for flash lending. Unfortunately, USDO does not completely conform to ERC-3156 standards when it comes to the `maxFlashLoan` function. According to the ERC [documentation](https://eips.ethereum.org/EIPS/eip-3156):

```
The maxFlashLoan function MUST return the maximum loan possible for token. If a token is not currently supported maxFlashLoan MUST return 0, instead of reverting.
```

Unfortunately, if the USDO token is paused then maxFlashLoan will still return the maxFlashMint value. Instead it should return 0.

USDO should ideally conform to these standards since it's advertising itself as supporting ERC-3156. 
 

### Proof of Concept

```solidity
function maxFlashLoan(address) public view override returns (uint256) {
    return maxFlashMint;
}
```

In this function, there are no checks that the USDO is not paused. This goes against the documentation standards:

> The maxFlashLoan function MUST return the maximum loan possible for token. If a token is not currently supported maxFlashLoan MUST return 0, instead of reverting.

### Recommended Mitigation Steps

The maxFlashLoan function should check if the token is paused. If it is, then the function should return 0.

## Options Epoch Duration unspecified
### Impact
`participate()` in twTAP.sol specifies the minimum epoch duration in case the user inputs a value for `duration` that is less than `EPOCH_DURATION`. 
`require(_duration >= EPOCH_DURATION, "twTAP: Lock not a week");`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L257

However, `participate()` in TapiocaOptionBroker.sol doesn't specify the minimum epoch duration for the user, instead calling the user inputted duration as "too short" if less than `EPOCH_DURATION`.
`require(lock.lockDuration >= EPOCH_DURATION, "tOB: Duration too short");`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L220C10-L220C80

### Recommended Mitigation Steps
Specify minimum epoch duration for user under TapiocaOptionBroker.sol

## Penrose relies on BoringFactory which can result in view functions returning incorrect market counts
### Impact
Penrose utilizes BoringFactory in order to deploy BigBang and Singularity contracts. Penrose utilizes the `clonesOf ` mapping defined in the BoringFactory contract. 

Unfortunately, BoringFactory allows anyone to call the `deploy()` function. This allows a malicious user to deploy contracts and thus impact the `clonesOf` mapping and therefore the clonesOfCount().

This can in turn cause the `Penrose.singularityMarkets()` and `Penrose.bigBangMarkets()` to misquote the number of Singularity or BigBang markets deployed.

See BoringFactory contract for more info: https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/BoringFactory.sol
 
### Proof of Concept

Below is the functions used to calculate the # of BigBang and Singularity markets. As can be seen in _getMasterContractLength() the BoringFactory.clonesOfCount() function is called to determine the number of markets for a given Master contract.


```solidity
// From Penrose.sol
function singularityMarkets()
    public
    view
    returns (address[] memory markets)
{
    markets = _getMasterContractLength(singularityMasterContracts);
}

// From Penrose.sol
function bigBangMarkets() public view returns (address[] memory markets) {
    markets = _getMasterContractLength(bigbangMasterContracts);
}

// From Penrose.sol
function _getMasterContractLength(
    IPenrose.MasterContract[] memory array
) public view returns (address[] memory markets) {
    uint256 _masterContractLength = array.length;
    uint256 marketsLength = 0;

    unchecked {
        // We first compute the length of the markets array
        for (uint256 i = 0; i < _masterContractLength; ) {
            marketsLength += clonesOfCount(array[i].location);

            ++i;
        }
    }

    markets = new address[](marketsLength);

    uint256 marketIndex;
    uint256 clonesOfLength;

    unchecked {
        // We populate the array
        for (uint256 i = 0; i < _masterContractLength; ) {
            address mcLocation = array[i].location;
            clonesOfLength = clonesOfCount(mcLocation); // relying on clonesOfCount assumes that only the Penrose admins can call Penrose.deploy(). Since anyone can call Penrose.deploy() the clonesOfCount return value can be manipulated.

            // Loop through clones of the current MC.
            for (uint256 j = 0; j < clonesOfLength; ) {
                markets[marketIndex] = clonesOf[mcLocation][j]; 
                ++marketIndex;
                ++j;
            }
            ++i;
        }
    }
}

// From BoringFactory.sol
function clonesOfCount(address masterContract) public view returns (uint256 cloneCount) {
    cloneCount = clonesOf[masterContract].length;
}

// From BoringFactory.sol
function deploy(
    address masterContract,
    bytes calldata data,
    bool useCreate2
) public payable returns (address cloneAddress) {
    require(masterContract != address(0), "BoringFactory: No masterContract");
    bytes20 targetBytes = bytes20(masterContract); // Takes the first 20 bytes of the masterContract's address

    if (useCreate2) {
        // each masterContract has different code already. So clones are distinguished by their data only.
        bytes32 salt = keccak256(data);

        // Creates clone, more info here: https://blog.openzeppelin.com/deep-dive-into-the-minimal-proxy-contract/
        assembly {
            let clone := mload(0x40)
            mstore(clone, 0x3d602d80600a3d3981f3363d3d373d3d3d363d73000000000000000000000000)
            mstore(add(clone, 0x14), targetBytes)
            mstore(add(clone, 0x28), 0x5af43d82803e903d91602b57fd5bf30000000000000000000000000000000000)
            cloneAddress := create2(0, clone, 0x37, salt)
        }
    } else {
        assembly {
            let clone := mload(0x40)
            mstore(clone, 0x3d602d80600a3d3981f3363d3d373d3d3d363d73000000000000000000000000)
            mstore(add(clone, 0x14), targetBytes)
            mstore(add(clone, 0x28), 0x5af43d82803e903d91602b57fd5bf30000000000000000000000000000000000)
            cloneAddress := create(0, clone, 0x37)
        }
    }
    masterContractOf[cloneAddress] = masterContract;
    clonesOf[masterContract].push(cloneAddress); // AUDIT: anyone is eligible to call deploy()and thus push a clone address to a master contract, even if it's not a legitimate master contract.

    IMasterContract(cloneAddress).init{value: msg.value}(data);

    emit LogDeploy(masterContract, data, cloneAddress);
}
```

As can be seen in the codebase, anyone is eligible to call Penrose.deploy(). This will create a clone contract based off a master contract, causing the `clonesOf ` to contain an additional item. This in turn influences the return value of `clonesOfCount` which is used in `_getMasterContractLength()`, thus impacting the return values of `Penrose.singularityMarkets()` and `Penrose.bigBangMarkets()`.

### Recommended Mitigation Steps

Tapioca should restrict who can call deploy() such that only the owner can call the contract. 

## Yieldbox.transfer doesn't validate to != from
### Impact
Yieldbox.transfer does not validate that the from address is the not the same as the to address. This can lead to quirky behavior as transfers should never occur intrapersonally.

### Proof of Concept

### Steps to Reproduce
1. Call `Yieldbox.transfer` where the from address is the same as to address. The transfer will succeed.

### Relevant code

```solidity
// From Yieldbox.sol
//
function transfer(
    address from,
    address to,
    uint256 assetId,
    uint256 share
) public allowed(from) {

    _transferSingle(from, to, assetId, share);
}

// From ERC1155.sol
//
function _transferSingle(
    address from,
    address to,
    uint256 id,
    uint256 value
) internal {
    require(to != address(0), "No 0 address");

    balanceOf[from][id] -= value;
    balanceOf[to][id] += value;

    emit TransferSingle(msg.sender, from, to, id, value);
}
```
### Recommended Mitigation Steps

Add a check that to != from.

## Penrose.executeMarketFn doesn't validate mc and data length
### Impact
When calling Penrose.executeMarketFn, two arguments are passed, mc and data. These two arguments are both arrays. These arrays are looped over where each mc address is called with each data item. Unfortunately, there is no check that the two arrays equal the same. A mistake made by the admin can result in an extra unnecessary call to be made.

### Proof of Concept

```solidity
/// @notice Execute an only owner function inside of a Singularity or a BigBang market
function executeMarketFn(
    address[] calldata mc,
    bytes[] memory data,
    bool forceSuccess
)
    external
    onlyOwner
    notPaused
    returns (bool[] memory success, bytes[] memory result)
{
    uint256 len = mc.length; // AUDIT: we need to validate that mc length is the same as data.length
    success = new bool[](len);
    result = new bytes[](len);
    for (uint256 i = 0; i < len; ) {
        require(
            isSingularityMasterContractRegistered[
                masterContractOf[mc[I]]
            ] || isBigBangMasterContractRegistered[masterContractOf[mc[i]]],
            "Penrose: MC not registered"
        );
        (success[i], result[i]) = mc[i].call(data[I]);
        if (forceSuccess) {
            require(success[i], _getRevertMsg(result[i]));
        }
        ++I;
    }
}
```

### Recommended Mitigation Steps

Add a require check that mc and data length are the same.

## Phase 2 specification comment flawed
Comment is incorrect. Cassava is not including in the Phase 2 Airdrop, as shown by the code and [documentation](https://docs.tapioca.xyz/tapioca/launch/option-airdrop#phase-two-core-tapioca-guild)
```solidity
    // [OG Pearls, Sushi Frens, Tapiocans, Oysters, Cassava]
    bytes32[4] public phase2MerkleRoots; // merkle root of phase 2 airdrop
    uint8[4] public PHASE_2_AMOUNT_PER_USER = [200, 190, 200, 190];
    uint8[4] public PHASE_2_DISCOUNT_PER_USER = [50, 40, 40, 33];
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L75

## Consistent Spelling Mistake
tOLP is consistently referenced as tOLR leading to confusion under [TapiocaOptionLiquidityProvision.sol](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L29)
```solidity
    uint128 amount; // amount of tOLR tokens locked.
```
```solidity
    uint256 totalDeposited; // total amount of tOLR tokens deposited, used for pool share calculation
```
```solidity
    /// @notice Locks tOLR tokens for a given duration
```
```solidity
    /// @param _amount Amount of tOLR tokens to lock
```
```solidity
        // Transfer the tOLR tokens back to the owner
```