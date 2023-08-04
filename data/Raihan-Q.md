## [L-01] Use of abi.encodePacked with dynamic types inside keccak256
abi.encodePacked should not be used with dynamic types when passing the result to a hash function such as keccak256. Use abi.encode instead, which will pad items to 32 bytes, to [prevent any hash collisions](https://docs.soliditylang.org/en/latest/abi-spec.html#non-standard-packed-mode).

```solidity
195                     keccak256(
                            abi.encodePacked(
                                keccak256(_bytecode),
                                address(this),
                                _erc20,
                                _salt
                            )
                        ),

213                     keccak256(
                            abi.encodePacked(
                                keccak256(_bytecode),
                                address(this),
                                _erc20,
                                _salt
                            )
                        ),


```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol

## [L-02] Claimed emits wrong user address
In the Vesting.claim function the Claimed is emitted ([Link](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L120)).
It is defined as:
[Link](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol#L62)
```solidity
  event Claimed(address indexed user, uint256 amount);
```

```solidity
135  emit Burn(msg.sender, _tokenId, options[_tokenId]);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol

```solidity
122           emit Burn(msg.sender, _tokenId, options[_tokenId]);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol

```solidity
101   emit HarvestFees(msg.sender);
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol

```solidity
151  emit Rebalancing(msg.sender, _amount, _isNative);
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/TapiocaOFT.sol

```solidity
99      emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);

100     emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);
```
https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol

## [L-03] Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from various type int/uint values

```solidity
176  votes = uint256(position.tapAmount) * uint256(position.multiplier);
```
https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol

## [G-04] Low-level calls that are unnecessary for the system should be avoided
```solidity
79  (result.success, result.returnData) = calli.target.call{value: val}(
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol

```solidity
380  (bool sent, ) = to.call{value: amount}("");
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol

## [L-05] Gas griefing/theft is possible on unsafe external call
return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, 'out' and 'outsize' values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

```solidity
380  (bool sent, ) = to.call{value: amount}("");
```   
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol

```solidity
280  (bool sent, ) = to.call{value: amount}("");
```
https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol