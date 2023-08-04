If yes then we could submit qa report and gas report
Info -1 zroPaymentAddress is set to zero 
mistake done with zropaymentAddress at https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/modules/MagnetarMarketModule.sol#L718
But the integration checklist say it should not be hardcoded to address(0)
https://layerzero.gitbook.io/docs/evm-guides/layerzero-integration-checklist
Info -2  d_max/d_min are not in bps, units are incorrect and hence calculations all become wrong
Info -3 informational: if owner passes an invalid singularity pool to TapiocaOptionLiquidityProvision#unregisterSingularity, the last pool is deleted anyway without being checked if the address matches