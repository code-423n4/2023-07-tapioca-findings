# [01] TapiocaOptionLiquidityProvision.sol doesn't fully support ERC1155
According to [comments on L356](https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L356C17-L371), the contract is supposed to support ERC1155.
Quoting from [eip-1155](https://eips.ethereum.org/EIPS/eip-1155): **Smart contracts MUST implement all of the functions in the ERC1155TokenReceiver interface to accept transfers.**
To fully support ERC1155, the contract should also implement `function onERC1155BatchReceived`
