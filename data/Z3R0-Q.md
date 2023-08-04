# Wrong use of equality operator

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140-L149:

```solidity
// If `amount` is 0, or `msg.sender` is `to` nothing happens

if (amount != 0 || msg.sender == to) {
```
The previous comment says the `if` code block is not executed if amount is 0 or `msg.sender` is equal to the `to` parameter. This assertion is not correct as to fulfill both conditions the code should be written as:
```solidity
// If `amount` is 0, or `msg.sender` is `to` nothing happens

if (amount != 0 || msg.sender != to) {
//if !(amount == 0 || msg.sender == to) {   // Alternatively
```
