0. Dont need to explicitly initialize with default value zero; and should preferably assign uint approvals_length = approvals.length; and use that inside for loop condition

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/a4793e75a79060f8332927f97c6451362ae30201/contracts/usd0/modules/USDOMarketModule.sol#L243-L245

Recommendation:

    function _callApproval(ICommonData.IApproval[] memory approvals) private {  
    		uint256 approvals_length = approvals.length;
        for (uint256 i; i < approvals_length; ) {


1. Why the public visibility modifier here? It's not called internally, so external modifier should suffice, and save on gas.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/a4793e75a79060f8332927f97c6451362ae30201/contracts/usd0/modules/USDOLeverageModule.sol#L93

Recommendation:

function multiHop(bytes memory _payload) external {