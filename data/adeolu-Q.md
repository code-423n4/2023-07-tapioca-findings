## 1. redeem() is uncallable when lockedUntil == block.timestamp

**Lines of code**

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L46

**Impact**
`lockedUntil` variable is meant to be used as a time limit. redeem() should be sucessfull when block.timestamp is >= lockedUntil. redeem() as it is means there will be a delay when lockedUntil value is reached. 

**Proof of Concept**
```
    function redeem() external {
        require(block.timestamp > lockedUntil, "Still locked");
        uint256 amount = balanceOf(msg.sender);
        _burn(msg.sender, amount);
        tapToken.transfer(msg.sender, amount);
    }
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/LTap.sol#L46C1-L51C6

**Tools Used**
manual review 
**Recommended Mitigation Steps**
change `require(block.timestamp > lockedUntil, "Still locked");` to `require(block.timestamp >= lockedUntil, "Still locked");`


**Assessed type**

Invalid Validation


## 2. Events should be emitted last in function logic 
**impact**

it is best practice to always emit events last 


**lines of code**
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L140C1-L164C6

**Proof of concept**

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L140C1-L164C6


**Tools Used**
manual review 


**Recommended Mitigation Steps**
emit events last


**Assessed type**
events