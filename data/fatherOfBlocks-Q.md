**tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol**
- L56/57 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol**
- L55/56 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol**
- L58/59 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol**
- L66/69/70/71/72/73 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol**
- L66/67/69/73/75 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tap-token-audit/contracts/options/TapiocaOptionBroker.sol**
- L90/91/92 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.

- L551 - A division is made by the _paymentTokenValuation input of the function, this is something that could be reversed if it is == 0, without the exception being handled, therefore it should be validated.


**tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol**
- L74 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol**
- L70/71/73/74/75/76 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol**
- L71/72/73/74/75 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tap-token-audit/contracts/tokens/BaseTapOFT.sol**
- L228/229/230 - A for is performed through the "rewardTokens" array, but inside "rewardClaimSendParams" is also iterated, without knowing if they have the same length, you should check that they have the same length.


**tap-token-audit/contracts/governance/twTAP.sol**
- L4/8 - The ICommonOFT and ERC4494 contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.

- L74/125 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.


**tap-token-audit/contracts/option-airdrop/AirdropBroker.sol**
- L7/11 - The IERC20 and twAML contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.

- L106/107/108 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.

- L418 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.

- L530 - A division is made by the _paymentTokenValuation input of the function, this is something that could be reversed if it is == 0, without the exception being handled, therefore it should be validated.

- L32 - The Phase2Info struct is created, but it is never used, therefore it should be deleted.


**YieldBox/contracts/YieldBoxRebase.sol**
- L5/6/7/8/9/10/11/12/13/14 - Multiples contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.


**tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol**
- L6/9/10/11/15/16/17 - Multiples contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.


**tapiocaz-audit/contracts/TapiocaWrapper.sol**
- L195/196/213/214 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.


**YieldBox/contracts/YieldBox.sol**
- L30/31/36/37 - The Base64, Domain, Strings, AssetRegister contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.

- L91 - No validation is performed in the constructor and the variables are immutable, therefore they should be validated before setting the variable to != 0x.

- L320/323/345/347 - Se realiza un for por el arreglo "ids", pero dentro tambien se itera "values", sin saber si tienen el mismo length, se deberia chequear que tengan el mismo length.
Tambien sucede lo mismo en la funcion transferMultiple() con tos y shares.


**YieldBox/contracts/YieldBoxPermit.sol**
- L8 - The IYieldBox interface is imported, but it is never used, therefore it generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.


**YieldBox/contracts/BoringMath.sol**
- L26/27 - Se realizan dos divisiones por inputs de la funcion, esto es algo que podria revertir si es == 0, sin ser manejada la exception, por lo tanto se deberia validar.


**YieldBox/contracts/NativeTokenFactory.sol**
- L129/130/141/143 - A for is performed through the "tos" and "froms" array, but "amount" is also iterated inside, without knowing if they have the same length, you should check that they have the same length.
 

**YieldBox/contracts/YieldBoxURIBuilder.sol**
- L6/7 - The NativeTokenFactory and IYieldBox interface contracts are imported, but they are never used, therefore they generate more gas expense in the deploy and also generate a low understanding in the blockchain explorer.
