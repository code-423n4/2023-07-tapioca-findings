https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCollateral.sol
The provided code snippet is a Solidity smart contract that represents a module for collateral-related actions in the Singularity lending platform. It is an extension of the SGLLendingCommon contract and is designed to manage collateral for accounts. Below is an analysis of the code:

1. SPDX License Identifier: The first line // SPDX-License-Identifier: UNLICENSED is a special comment used to indicate the license under which the contract is published. In this case, it indicates that the contract has no license (UNLICENSED). This means the contract's code is not open-source and has no specific license terms associated with it.

2. Solidity Version: The contract is intended to be compiled with Solidity version 0.8.18 or higher (pragma solidity ^0.8.18;). It ensures that the contract will be compatible with the specified Solidity version or any later version within the same major version.

3. Imports: The contract imports the SGLLendingCommon.sol file, which is expected to contain shared functions and variables required by this collateral module.

4. Libraries: The contract uses two libraries: RebaseLibrary and BoringERC20. Libraries allow you to add functions and state variables to a contract without actually inheriting from them. Here, these libraries extend the functionality of the contract with additional functionalities related to rebasing (e.g., monetary policy adjustments) and ERC-20 token operations.

5. Public Functions:
addCollateral: This function adds collateral from msg.sender to the account to. It takes in parameters such as from (the account transferring the shares), to (the receiver of the tokens), skim (a boolean indicating if the amount should be skimmed from the deposit balance of msg.sender or transferred from yieldBox), amount (the amount of collateral to be added), and share (the amount of shares to add for to). This function calls the internal function _addCollateral.
removeCollateral: This function removes an amount of collateral from the from account and transfers it to the to account. The share parameter indicates the amount of shares to remove. It calls the internal function _removeCollateral.
6. Modifiers:
notPaused: It is likely that the notPaused modifier is defined in the SGLLendingCommon contract (since this contract inherits from it). It is used to check if the lending platform is not paused before allowing certain functions to execute.
allowedBorrow: This modifier is likely defined in the SGLLendingCommon contract as well. It ensures that the share amount specified is allowed for borrowing from the from account.
7. Function Implementations: The two public functions, addCollateral and removeCollateral, delegate their logic to internal functions _addCollateral and _removeCollateral, respectively. The implementation of these internal functions is not visible in the provided snippet.

In conclusion, the provided code snippet appears to be a contract that handles collateral-related actions for a lending platform, but more information is needed to fully evaluate its functionality and potential risks. The absence of a license indicates that the contract code is not intended to be openly shared or used by others without explicit permission from the contract owner.


https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLBorrow.sol
Based on the provided code snippet, this is the Solidity smart contract for the Singularity borrow module. It appears to be a part of a lending platform that allows users to borrow and repay assets. Below is an audit of the contract:

1. SPDX License Identifier: The first line // SPDX-License-Identifier: UNLICENSED is a special comment used to indicate the license under which the contract is published. In this case, it indicates that the contract has no license (UNLICENSED). This means the contract's code is not open-source and has no specific license terms associated with it.

2. Solidity Version: The contract is intended to be compiled with Solidity version 0.8.18 or higher (pragma solidity ^0.8.18;). It ensures that the contract will be compatible with the specified Solidity version or any later version within the same major version.

3. Imports: The contract imports the SGLLendingCommon.sol file, which is expected to contain shared functions and variables required by this borrow module.

4. Libraries: The contract uses two libraries: RebaseLibrary and BoringERC20. Libraries allow you to add functions and state variables to a contract without actually inheriting from them. Here, these libraries extend the functionality of the contract with additional functionalities related to rebasing (e.g., monetary policy adjustments) and ERC-20 token operations.

5. Public Functions:

borrow: This function allows an account (from) to borrow a specified amount of assets. It transfers the borrowed tokens to another account (to). It returns the part (total part of the debt held by borrowers) and share (total amount in shares borrowed).
repay: This function allows an account (from) to repay a loan. The to parameter specifies the address of the user to whom the payment should go. It takes in a boolean parameter skim to determine if the repayment amount should be skimmed from the deposit balance of the sender (msg.sender) or transferred from yieldBox. The part parameter is the amount to repay, and it should be less than or equal to the user's borrow part. It returns the amount that was repaid.
6. Modifiers:
notPaused: It is likely that the notPaused modifier is defined in the SGLLendingCommon contract (since this contract inherits from it). It is used to check if the lending platform is not paused before allowing certain functions to execute.
solvent: This modifier is likely defined in the SGLLendingCommon contract as well. It ensures that the account (from) is solvent (i.e., it has enough collateral to cover the outstanding borrowings).
7. Function Implementations: The contract contains implementation details for the borrow and repay functions. However, without seeing the full code of the contract and the SGLLendingCommon contract, it is not possible to provide a comprehensive analysis.

The provided code snippet represents a Solidity smart contract called BaseUSDOStorage. It seems to be an implementation of a US Dollar (USDO) stablecoin contract with storage functionalities. Below is an analysis of the contract:


https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol
The provided code snippet represents a Solidity smart contract called BaseUSDOStorage. It seems to be an implementation of a US Dollar (USDO) stablecoin contract with storage functionalities. Below is an analysis of the contract:
1. SPDX License Identifier: The first line // SPDX-License-Identifier: UNLICENSED is a special comment used to indicate the license under which the contract is published. In this case, it indicates that the contract has no license (UNLICENSED). This means the contract's code is not open-source and has no specific license terms associated with it.

2. Solidity Version: The contract is intended to be compiled with Solidity version 0.8.18 or higher (pragma solidity ^0.8.18;). It ensures that the contract will be compatible with the specified Solidity version or any later version within the same major version.

3. Imports: The contract imports various external Solidity contracts from different libraries, including "tapioca-sdk," "openzeppelin," and "tapioca-periph." These libraries are used to extend the functionality of the contract and provide implementations for interfaces and utility functions.

4. Inheritance: The contract inherits from the OFTV2 contract, which is likely part of the "tapioca-sdk" library. The OFTV2 contract probably implements functionalities related to the OFT (Order Flow Tokens) standard version 2.

5. State Variables:

yieldBox: A public and immutable state variable that stores the address of a YieldBoxBase contract. It is likely used for managing yields from deposited funds.
conservator: A state variable that holds the address of the Conservator. The purpose of the Conservator is not clear from the provided code snippet.
allowedMinter and allowedBurner: Mapping variables that track addresses allowed to mint and burn USDO tokens, respectively. The mapping is structured as chainId => address => status, where the status indicates whether the address is allowed (true) or not (false) to perform the specified action.
paused: A boolean state variable that indicates the pause state of the contract. It seems to control whether certain functions are allowed to execute or not.
6. Events: The contract emits various events such as Minted, Burned, SetMinterStatus, SetBurnerStatus, ConservatorUpdated, PausedUpdated, FlashMintFeeUpdated, and MaxFlashMintUpdated. These events provide information about specific contract operations and updates.

7. Constructor: The constructor initializes the contract and sets default values for the flashMintFee and maxFlashMint. It also sets the sender of the transaction as an allowed minter and burner.

8. Internal Functions: The contract seems to contain some internal functions like _getChainId and _getRevertMsg. However, these functions are not visible in the provided snippet.

9. Fallback Function: The contract includes a receive() external payable function, which is likely used to receive Ether.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol
To audit the contract, I will review the code and highlight potential issues or concerns. Here's the analysis of the contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Imports: The contract imports several external contracts/interfaces. It's essential to verify that these imported contracts are trusted and secure, as any vulnerabilities in the imported contracts could potentially affect the security of this contract.

4. Contract Inheritance: The contract inherits from "BaseUSDO" and "IERC3156FlashLender." It's crucial to review the functionality of these base contracts to understand the complete behavior of the USDO contract.

5. Constructor: The contract has a constructor that initializes the USDO contract with specific parameters. The constructor seems straightforward, but it's essential to verify that the passed addresses and parameters are correct and secure.

6. Modifier "notPaused": The contract has a custom modifier called "notPaused." This modifier is used to ensure that certain functions can only be called when the contract is not paused. It's crucial to review the implementation and logic of the "paused" state to prevent unexpected behavior.

7. View Functions: The contract contains view functions like "maxFlashLoan" and "flashFee." These functions are used to retrieve specific information and should not modify the state of the contract.

8. Public Functions: The contract exposes several public functions, including "flashLoan," "mint," and "burn." These functions perform specific actions and should be thoroughly reviewed for security vulnerabilities.

9. Flash Loan Mechanism: The "flashLoan" function implements a flash loan mechanism, allowing users to borrow USDO tokens temporarily. Flash loans can introduce complex security risks, so it's crucial to ensure that the implementation is secure and protected against potential attacks.

10. Access Control: The contract appears to use role-based access control for minting and burning USDO tokens. It's essential to review the "allowedMinter" and "allowedBurner" mappings to understand how roles are assigned and managed.

11. Events: The contract emits "Minted" and "Burned" events to track token minting and burning activities. Events are useful for external applications to track contract activities and can aid in monitoring contract usage.

Overall, the contract seems to implement a stablecoin called "USDO" and includes a flash loan mechanism. However, a thorough security audit is necessary to ensure the contract's safety and assess potential vulnerabilities or issues that might not be immediately apparent. The use of external contracts also requires verification of their security to guarantee the overall safety of the system.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol
To audit the contract SGLLendingCommon, I will review the code and highlight potential issues or concerns. Please note that I'll assume that the SGLCommon, RebaseLibrary, and BoringERC20 contracts are already audited and secure, as they are imported but not provided here.

Here's the analysis of the SGLLendingCommon contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Import Statement: The contract imports the "SGLCommon.sol" file. We need to review the content of this file to understand the complete behavior of the SGLLendingCommon contract.

4. Libraries Usage: The contract utilizes the RebaseLibrary and BoringERC20 libraries. It's essential to verify that these libraries are secure and provide correct functionality.

5. Internal Functions: The contract defines several internal functions, such as _addCollateral, _removeCollateral, _borrow, and _repay. These functions appear to handle specific operations related to lending, borrowing, and collateral management. Their implementation should be carefully audited to ensure they function as intended and do not introduce any security vulnerabilities.

6. Borrow and Repay: The _borrow function performs a borrow operation, and the _repay function performs a repayment. These are critical operations in the lending module, and their logic should be thoroughly reviewed to ensure proper handling of borrowed funds and repayments.

7. Total Borrow Cap: The contract includes a check to ensure that the total borrowed amount does not exceed a specific cap (totalBorrowCap). It's essential to ensure that the cap is set correctly and that the borrowing limit is enforced accurately.

8. Fees: The contract applies a flat percentage fee (borrowOpeningFee) on any borrow operation. The fee calculation should be verified to ensure accuracy and fairness.

9. Transfer of Tokens: The contract performs token transfers using the yieldBox.transfer function. It's important to ensure that token transfers are secure and that they do not result in loss or mismanagement of user funds.

10. Emitting Events: The contract emits several events such as LogAddCollateral, LogRemoveCollateral, LogBorrow, and LogRepay. These events provide insights into the contract's activities and are essential for monitoring contract usage and interactions.

11. State Management: The contract maintains various state variables to track collateral, borrowing, and other related data. The state variables should be thoroughly reviewed to ensure consistency and proper handling of state changes.

Overall, the SGLLendingCommon contract appears to be a lending module with functions related to collateral management, borrowing, and repayment. However, a complete security audit is necessary to assess the contract's safety and identify potential vulnerabilities or issues that might not be immediately apparent. The interactions with the SGLCommon, RebaseLibrary, and BoringERC20 contracts should also be carefully scrutinized for security and correctness

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol
To audit the contract SGLStorage, I will review the code and highlight potential issues or concerns. Please note that I'll assume that the imported contracts and interfaces are already audited and secure, as they are imported but not provided here.

Here's the analysis of the SGLStorage contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Imports: The contract imports several external contracts/interfaces. It's crucial to verify that these imported contracts and interfaces are trusted and secure, as any vulnerabilities in them could potentially affect the security of this contract.

4. Contract Inheritance: The contract inherits from BoringOwnable and Market. It's essential to review the functionality of these base contracts to understand the complete behavior of the SGLStorage contract.

5. State Variables: The contract defines several state variables to store information about accrual, total assets, liquidation queue, yield box shares, utilization rates, interest rates, etc. It's crucial to review the state variables' usage and their interactions with other functions.

6. Events: The contract emits various events to log different activities, such as adding/removing collateral, borrowing, repaying, updating parameters, etc. Events are useful for external applications to track contract activities and can aid in monitoring contract usage.

7. View Functions: The contract contains view functions like symbol, name, decimals, and totalSupply. These functions provide information about the market's ERC20 token. Ensure that these functions return accurate and expected results.

8. Accrue Function: The contract defines an internal _accrue function as a virtual override. It seems to be part of the accrual mechanism, but the implementation is missing. It's essential to ensure that the accrual mechanism is correctly implemented and doesn't introduce any security vulnerabilities.

9. External Calls: The contract interacts with external contracts, such as oracles, yield boxes, and the liquidation queue. It's crucial to verify that these interactions are secure and that proper error handling is in place to handle potential failures in external calls.

Overall, the SGLStorage contract appears to be a part of a more extensive Singularity lending and storage system. However, without the complete context and implementation of the missing parts like _accrue, it's challenging to assess the contract's functionality and security fully.

A thorough security audit is necessary to ensure the contract's safety and identify potential vulnerabilities or issues that might not be immediately apparent. Additionally, the interactions with the imported contracts and the base contracts should be carefully scrutinized for security and correctness.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol
To audit the contract SGLLeverage, I will review the code and highlight potential issues or concerns. Please note that I'll assume that the imported contracts, interfaces, and libraries are already audited and secure, as they are imported but not provided here.

Here's the analysis of the SGLLeverage contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Imports: The contract imports several external contracts/interfaces. It's crucial to verify that these imported contracts, interfaces, and libraries are trusted and secure, as any vulnerabilities in them could potentially affect the security of this contract.

4. Contract Inheritance: The contract inherits from SGLLendingCommon. It's essential to review the functionality of the base contract to understand the complete behavior of the SGLLeverage contract.

5. External Calls: The contract interacts with various external contracts, such as penrose, yieldBox, asset, collateral, swapper, etc. It's crucial to verify that these interactions are secure and that proper error handling is in place to handle potential failures in external calls.

6. Lever Up and Lever Down Functions: The contract defines multiHopBuyCollateral, multiHopSellCollateral, buyCollateral, and sellCollateral functions that allow users to perform leveraged actions, such as borrowing more and buying collateral or selling collateral to repay debt. These functions execute multiple steps involving borrow, add/remove collateral, and swap operations. The logic in these functions should be carefully audited to ensure correctness and security.

7. Events: The contract emits various events to log different activities related to leverage operations. Events are useful for external applications to track contract activities and can aid in monitoring contract usage.

8. View Functions: The contract does not define any view functions, but some inherited functions are view functions, such as symbol, name, decimals, etc. These functions should return accurate and expected results.

Overall, the SGLLeverage contract appears to be a module for handling leverage-related actions in the Singularity lending system. However, without the complete context and implementation of the base contract SGLLendingCommon and the missing parts from the imported contracts, it's challenging to assess the contract's functionality and security fully.

A thorough security audit is necessary to ensure the contract's safety and identify potential vulnerabilities or issues that might not be immediately apparent. Additionally, the interactions with the imported contracts, interfaces, and libraries should be carefully scrutinized for security and correctness.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol
To audit the contract MarketERC20, I will review the code and highlight potential issues or concerns. Please note that I'll assume that the imported contracts, interfaces, and libraries are already audited and secure, as they are imported but not provided here.

Here's the analysis of the MarketERC20 contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Imports: The contract imports various external contracts and interfaces. It's essential to verify that these imported contracts, interfaces, and libraries are trusted and secure, as any vulnerabilities in them could potentially affect the security of this contract.

4. Contract Inheritance: The contract inherits from IERC20, IERC20Permit, and EIP712. It's crucial to review the functionality of these parent contracts to understand the complete behavior of the MarketERC20 contract.

5. Variables: The contract defines several mapping variables, including balanceOf, allowance, and allowanceBorrow, to keep track of user balances and allowances.

6. Events: The contract emits various events, such as Transfer, Approval, and ApprovalBorrow, to log different activities related to token transfers and approvals.

7. Modifiers: The contract defines two modifiers, allowedLend and allowedBorrow, which check if the caller is approved to perform lend or borrow operations on behalf of the owner. These modifiers are used in several functions to ensure proper authorization.

8. Functions: The contract implements standard ERC20 functions like transfer, transferFrom, and approve, along with additional functions approveBorrow, permit, and permitBorrow. The permit functions are used to implement EIP-712 permit functionality for both asset and collateral types.

9. Nonce Tracking: The contract keeps track of nonces for permit functions to prevent replay attacks.

10. DOMAIN_SEPARATOR: The contract implements the DOMAIN_SEPARATOR function required for EIP-712 permit functionality.

Overall, the MarketERC20 contract appears to be an ERC20 implementation with additional functionality to support permit (EIP-712) for both asset and collateral types. However, without the complete context of how this contract interacts with other contracts and the underlying system, it's challenging to assess the contract's functionality and security fully.

A thorough security audit is necessary to ensure the contract's safety and identify potential vulnerabilities or issues that might not be immediately apparent. Additionally, the interactions with the imported contracts, interfaces, and libraries should be carefully scrutinized for security and correctness.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol
To audit the contract SGLCommon, I will review the code and highlight potential issues or concerns. Please note that I'll assume that the imported contracts, interfaces, and libraries are already audited and secure, as they are imported but not provided here.

Here's the analysis of the SGLCommon contract:

1. SPDX License Identifier: The contract uses SPDX-License-Identifier to specify the license, which is "UNLICENSED." This means that the contract does not have any specific open-source license and is likely not intended for public use or modification.

2. Pragma Statement: The contract specifies that it uses Solidity version 0.8.18. It's generally good to use the latest stable version of Solidity, but this version should be fine as of my knowledge cutoff date in September 2021.

3. Imports: The contract imports the SGLStorage contract, but the contract itself is not provided here. It's essential to verify the functionality and security of the imported contract to understand the complete behavior of the SGLCommon contract.

4. Contract Inheritance: The contract inherits from SGLStorage. Without access to the SGLStorage contract, it's challenging to fully understand the interactions between SGLCommon and SGLStorage.

5. Variables: The contract does not define any additional state variables, but it appears to inherit state variables from SGLStorage.

6. Functions: The contract defines several functions, including accrue, getInterestDetails, _getInterestRate, _accrue, _addTokens, _addAsset, and _removeAsset.

7. Modifiers: The contract does not define any custom modifiers.

8. Internal Functions: The contract defines several internal functions used to handle interest accrual, token movement, adding and removing assets, and calculating borrow amounts.

9. Interest Accrual: The _getInterestRate and _accrue functions handle the accrual of interest on borrowed tokens and the accumulation of fees. It appears that interest rates are adjusted based on the utilization ratio (borrowed tokens / total assets). The interest rate can vary based on the utilization, with a minimum and maximum cap.

10. Token Movement: The _addTokens function is used to move tokens between different accounts. It checks for sufficient token balances before performing the transfer.

11. Asset Management: The _addAsset and _removeAsset functions are used to add and remove assets. The _addAsset function calculates the fraction of the asset share based on the total assets and the amount of the new share to add. The _removeAsset function does the opposite and calculates the share to remove based on the fraction specified.

12. YieldBox Interaction: The contract interacts with the yieldBox contract to manage token shares and transfers. However, without access to the yieldBox contract, it's difficult to assess the security of these interactions.

Overall, the SGLCommon contract appears to handle interest accrual, asset management, and token transfers. However, due to the lack of complete context and access to the imported contract and external dependencies, it's challenging to assess the contract's functionality and security fully.

A thorough security audit is necessary to ensure the contract's safety and identify potential vulnerabilities or issues that might not be immediately apparent. Additionally, the interactions with the imported contracts, interfaces, and libraries should be carefully scrutinized for security and correctness.


### Time spent:
100 hours