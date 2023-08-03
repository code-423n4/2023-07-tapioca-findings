1. Mistake Repetition of condition code in MagnetarV2.sol contract - L661-676 code was repeated again at L676-L692
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L661-L676
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2.sol#L677-L692

2. Absence of address(0) check in constructor of TapiocaOptionBroker.sol Contract - There is a need to ensure zero address check at deployment to avoid unnecessary errors