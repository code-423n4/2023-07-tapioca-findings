1. Use uint256 data type for nonce.
For example, the below link, the nonce is treated with uint64.
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L126

uint64 value can be easily reached. max value of uint64 is `18446744073709551615`

2. unsafe use of block.chainId() . this may not be same for all the chains.

when we look at one of the place in `TapOFT.sol`

    function _getChainId() private view returns (uint256) {
        return block.chainid; -------------------------------->> this will not be same across different chains.
    }

