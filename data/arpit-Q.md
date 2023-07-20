https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLendingCommon.sol#L66C1-L70C1

````
     require(
            totalBorrowCap == 0 || totalBorrow.base <= totalBorrowCap,
            "SGL: borrow cap reached"
        );

````

## Summary

The `_borrow` function in the provided code is responsible for handling asset borrowing in the smart contract. It includes a check for the `totalBorrowCap`, which limits the total amount that can be borrowed from the contract.

## Vulnerability Details

The vulnerability lies in the lack of explicit handling for the contract's behavior after reaching the `totalBorrowCap`. While the function correctly checks if the current value of `totalBorrow` exceeds the cap, it does not define what should happen when the cap is reached. This ambiguity can lead to unexpected behavior or funds being locked in the contract.

## Impact

The impact of this vulnerability can vary depending on the contract's intended design and requirements. If not handled appropriately, the following scenarios might occur:

1. Borrowing Disabled: Reaching the borrow cap may lead to borrowing being disabled, preventing any further borrow actions. This can adversely affect users who have collateral and want to borrow assets but are unable to do so.

2. Funds Lock-Up: If the contract does not specify any handling after reaching the cap, users might unknowingly continue attempting to borrow, resulting in funds getting locked in the contract without successful borrow actions.

## Proof of Concept

As this vulnerability involves an ambiguous behavior after reaching the `totalBorrowCap`, a proof of concept would require a detailed analysis of the contract's intended design and interactions with other parts of the system. It might involve conducting thorough testing and simulations to demonstrate the impact of reaching the cap without appropriate handling.

## Tools Used

The vulnerability assessment was done through code analysis and manual review without specific tools.

## Recommended Mitigation Steps

To mitigate this vulnerability, the following steps are recommended:

1. Clearly Define Behavior: The contract should explicitly define what happens when the `totalBorrowCap` is reached. This could involve disabling further borrowing, waiting for repayments before allowing new borrowers, or other defined actions.

2. Implement Proper Error Handling: If borrowing is to be disabled after reaching the cap, consider adding appropriate error messages to inform users of the cap limit.

3. Conduct Comprehensive Testing: Conduct thorough testing and simulations to verify the contract's behavior when the `totalBorrowCap` is reached and ensure that funds are not locked unintentionally.

4. External Audit: Engage external security experts to conduct a comprehensive security audit of the contract to identify and address any potential vulnerabilities or ambiguities in handling the `totalBorrowCap`.

5. Ensure Contract Upgradability: If the contract is upgradable, ensure that changes to the behavior after reaching the cap can be safely implemented without compromising existing functionality.