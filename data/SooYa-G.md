## Modifier That Used Only One Time Can Be Moved Inside Function

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol#L39C1-L42C6

Modifier make code more elegant, but cost more than normal function. Since `onlyBroker()` modifier in oTAP.sol only called once, so its better to replace it to normal function to save gas. 

I recommend to remove the modifier and add line code below in `mint()` function:
```require(msg.sender == broker, "OTAP: only onlyBroker");```


## Do not declare variable with default value (0 for uint)

https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L29

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. 

Change line code below
```
uint256 public seeded = 0;
```
Into
```
uint256 public seeded;
```