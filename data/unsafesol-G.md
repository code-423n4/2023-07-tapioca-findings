Gas-1= Reducing Bytecode Size With Modifiers
Consider refactoring a modifier so it calls an internal function. This approach is also used in OpenZeppelin's Ownable.sol
Let's consider example of registeredSingularityMasterContract modifier at :
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol#L173
 modifier registeredSingularityMasterContract(address mc) {
        require(
            isSingularityMasterContractRegistered[mc] == true,
            "Penrose: MC not registered"
        );
        _;
    }

It can be changed to :
modifier registeredSingularityMasterContract(address mc) {
registerSingularityMasterContract();
_;
}
function registerSingularityMasterContract(){
 require(isSingularityMasterContractRegistered[mc] == true,
            "Penrose: MC not registered"
        );
}
 
In the same way all other modifier can changed for better gas optimizations.