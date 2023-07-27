# First
Double check to ensure the transfer of tokens is allowed. The lines [YieldBox.sol#L310](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L310) and [YieldBox.sol#L322](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L322) do exactly the same, thus it cost gas unnecessarily. Remove the one in line 322 (the second one)

# Second
Consider adding an `else` clause instead of the [return here](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L698) so that it saves bytecode size by removing one return opcode, that is `if(!dest chain) {...} else {...}` instead of the current implementation (which has two implicit `return` routines, one at the end of the `if` and the other at the end of the function). 

# Third 
It is more efficient, for `++X` and `X++` to do `X += 1`. See the next test in foundry to show that it saves 8 gas and 2 gas respectively:

```
pragma solidity ^0.8.13;

import "forge-std/Test.sol";

contract POC is Test {
    uint256 private a;

    function setUp() public {
         a = 0;
    }

    function testA() public {
        a++;
    }

    function testB() public {
        a += 1;
    }

    function testC() public {
        ++a;
    }
}
```

Results:

- testA -> 22397
- testB -> 22389
- testC -> 22391

Consider using the `X += 1` version everywhere. The RE can be `[^ \+]*\+\+` for the occurrences of `X++` and `\+\+[^ \)]*` for the occurrences of `++X`. The same applies to other arithmetic operations.

# Third
Consider changing the increment in loops to unchecked blocks if the loop cycles are known (`for(...; ...; ++i) {...}` to `for(...; ...; ;) {...; unchecked { ++i; } }`. This is because Solidity checks for over/underflows by default since version 0.8.0, so if the maximum value of the counter, there is no need for those useless opcodes that ensures there is no over/underflows. The RE is `; [^;\)]+\) \{`

# Fourth
In [TapiocaWrapper.sol#L86-L90](https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L86-L90) it is doing a check to avoid OOB indexing. However, starting from version 0.8.0, Solidity does check for underflows, thus it makes impossible to OOB in this situation. Consider removing the `if` clause because it is doing unnecessary opcodes (although it is more explicit to let it there)