1 . Target : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCollateral.sol

-  addCollateral function does not check to make sure that the share parameter is greater than zero. This could allow an attacker to call the function with a negative share parameter, which would result in an incorrect update to the collateral balance.

- The removeCollateral function does not check to make sure that the share parameter is less than or equal to the collateral balance of the from account. This could allow an attacker to call the function with a share parameter that is greater than the collateral balance, which would result in an incorrect update to the collateral balance.

- The _addCollateral and _removeCollateral functions do not check to make sure that the from account has the necessary approvals to transfer the tokens. This could allow an attacker to call the functions with a from account that does not have the necessary approvals, which would result in an incorrect update to the token balances.

I recommend to add following checks to the addCollateral and removeCollateral functions:

require(share > 0, "Share must be greater than zero");
require(share <= collateralBalance, "Share must be less than or equal to collateral balance");
require(IERC20(collateral).allowance(from, address(this)) >= amount, "Not enough approvals");



2 . Target : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLBorrow.sol

- The function _computeAllowanceAmountInAsset does not check for overflow. This could lead to an incorrect calculation of the amount of tokens that can be borrowed.

- The function _borrow does not check for underflow. This could lead to a negative amount of tokens being borrowed.

- The function _repay does not check for overpayment. This could lead to more tokens being repaid than were originally borrowed.


3 . Target : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/yearn/YearnStrategy.sol

The _withdraw() function in the YearnStrategy contract has a rounding error. When the amount parameter to the function is greater than the queued amount of tokens that are currently held by the contract, the function will withdraw the toWithdraw amount of tokens from the Yearn vault. However, the wrappedNative.safeTransfer() function will only transfer amount - 1 tokens to the to address, due to rounding errors. This means that the to address will not receive the full amount of tokens that they were expecting.

The _withdraw() function can be modified to use the safeTransferFrom() function instead of the safeTransfer() function. This will ensure that the full amount of tokens is transferred to the to address, even if there are rounding errors.


