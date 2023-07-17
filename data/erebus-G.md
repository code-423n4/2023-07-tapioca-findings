# First
Double check to ensure the transfer of tokens is allowed. The lines [YieldBox.sol#L310](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L310) and [YieldBox.sol#L322](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol#L322) do exactly the same, thus it cost gas unnecessarily. Remove the one in line 322 (the second one)

# Second
Consider adding an `else` clause instead of the [return here](https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/modules/MagnetarMarketModule.sol#L698) so that it saves bytecode size by removing one return opcode, that is `if(!dest chain) {...} else {...}` instead of the current implementation (which has two implicit `return` routines, one at the end of the `if` and the other at the end of the function). 

# Third
Consider changing strings errors in the `require` statements to custom errors in order to save gas. The RE is `, "[^"]+"\)` 

# Fourth
Consider changing the increment in loops to unchecked blocks if the loop cycles are known (`for(...; ...; ++i) {...}` to `for(...; ...; ;) {...; unchecked { ++i; } }`. This is because Solidity checks for over/underflows by default since version 0.8.0, so if the maximum value of the counter, there is no need for those useless opcodes that ensures there is no over/underflows. The RE is `; [^;\)]+\) \{`