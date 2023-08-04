0.https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L9
M/QA/LOW: need to replace this with the latest release: https://forum.openzeppelin.com/t/draft-erc20permit-sol-vs-erc20permit-sol/36338

1. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/a4793e75a79060f8332927f97c6451362ae30201/contracts/usd0/USDO.sol#L68
QA: why is this necessary if its not possible to flashloan anything else other than the stablecoin? Why is the token even given as a parameter?

3. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L115
QA: always ensure that setConservator() is called first to set the conservator address, otherwise this will revert because default value for conservator is address(0) 

4. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDOStorage.sol#L22
QA: Explicit initialization of storage variables with default values is implemented to ensure predictable behavior, code clarity, and reduce potential vulnerabilities related to uninitialized storage variables.
Many instances throughout the codebase.

Risk:
Uninitialized storage variables can lead to various types of exploits and vulnerabilities, including but not limited to arbitrary state manipulation, unauthorized access, data leakage, and bypassing access controls. These vulnerabilities arise when uninitialized storage variables are improperly accessed or manipulated, allowing an attacker to gain control over critical contract state or exploit unintended behaviors.

5. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L366
QA/LOW: why is this `success = true;` necessary, if at all? to ENSURE that if revert happens inside `if` statement its 100% definitely because delegatecall failed? Unlikely. Then why is it here?

6. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L370
QA: updated(22/07/2023): why use _forwardRevert here if it seems to be using a set default value of false in all scenarios? If the intention here was to allow for some future use case where _forwardRevert is true, then this `if` statement will always fail and skip revert, even if delegatecall failed. This _forwardRevert does not belong inside the `if` statement with `success` variable. Should have its own check or some other implementation approach.

8. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/a4793e75a79060f8332927f97c6451362ae30201/contracts/usd0/modules/USDOMarketModule.sol#L242-L243
QA/LOW: gas griefing attack maybe? but definitely risk of DoS via running out of gas if pending approval array too large... any max length checks somewhere?

9. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L90
QA: Why no max/min limits checks, e.g. require statements?? current max damage? >>> 0 then DoS for (flash) minting USDO. too high then consequences?

10. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L99
QA: Why no min limit check?

11. https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/364dead3a42b06a34c802eee951cea1a654d438e/contracts/usd0/BaseUSDO.sol#L126
QA: is intended to have multiple allowed minters at same time for same chainId, or not?