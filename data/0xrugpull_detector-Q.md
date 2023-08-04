## `BigBang.operator` is defined but never used.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L46
```solidity
    mapping(address => mapping(address => bool)) public operators; // @audit - unused operators
```

## No event emitted for stable period change
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/utils/ChainlinkUtils.sol#L64-L69
```solidity
    function changeStalePeriod(
        uint32 _stalePeriod
    ) external onlyRole(GUARDIAN_ROLE_CHAINLINK) {
        stalePeriod = _stalePeriod;
        // @audit - no event emitted when critcal parameter is changed.
    }
```
