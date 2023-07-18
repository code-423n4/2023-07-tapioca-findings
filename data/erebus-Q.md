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

# Third
In [TapiocaOFT.sol#L75](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/TapiocaOFT.sol#L75), the function `wrap` relies on `_wrapNative` to mint `TOFT` tokens. However, it does not take into account the `_amount` parameter passed to the function as it does with `wrap`, relying on `msg.value`. Thus a naive caller can call this function with a desired `_amount` value but in return he will receive more/less depending of the `msg.value` of the transaction, thus if it is a, say, chained transaction (furocombo, for example) he can get his funds drained in this step and the next ones will incur in undefined behavior (the devs do not care about that part because it is obviously out of scope, but the example is to illustrate the issue). Consider using the `_amount` parameter and check that it is equal to `msg.value`, reverting in the other situation. That way, the user will know exactly how much he is wrapping (it happens [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L95) too)

# Fourth
(I'm being too much, I know) In [mTapiocaOFT#L43](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/mTapiocaOFT.sol#L43), in the definition of the `_erc20` parameter, there is a `true` that should not be there (if it is auto-converted to the official docs, it can be bad for rep to have typos and "forgotten words" around). Remove it

# Fifth
Duplicated comments [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L237) and [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L239). Remove one of them

# Sixth 
Commented import [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L11) when `ITapiocaOptionsBrokerCrossChain` is used in the code 8 times. Uncomment it

# Seventh
Some contracts like `Balancer.sol` in the `tapiocaz-audit` repo do use the function `swapETH` from `IStargateRouter` in functions like `_sendNative` (see [here](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/interfaces/IStargateRouter.sol#L27) for the interface and [here](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L288) for the usage). However, there is no such function in the implementation from the Stargate protocol (see [here](https://github.com/stargate-protocol/stargate/blob/main/contracts/Router.sol)). So it shall revert because the signature does not exist in the `Router` contract. I am putting the severity as QA because it may be some Solidity fact I do not know yet BUT calling a function that does not exist is definitely a higher severity (up to the judges to increase my knowledge or let it be)