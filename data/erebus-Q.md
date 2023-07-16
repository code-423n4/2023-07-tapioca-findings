# First
This [condition](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L151) in the `MagnetarMarketModule` contract can be simplified to just `msg.sender`. This is because, if `extractFromSender` is false, the `_checkSender` function ensures `user = msg.sender` otherwise it reverts:

```
function _checkSender(address _from) internal view {
        require(_from == msg.sender, "MagnetarV2: operator not approved"); // @audit rlly ?
}
```

thus the condition `extractFromSender ? msg.sender : user` goes to `msg.sender` if `extractFromSender` is true and to `user == msg.sender` from the `checkSender(user)` if it is false

# Second
Redundant code [here](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L487-L492) given the fact that those checks had been made [here](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L305-L310) and they hadn't been modified at all (I am not taking into account the previous issue, just saying what is written RN)