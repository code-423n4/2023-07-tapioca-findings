| ID     |                                     Title                                      | Instances |
| :----- | :----------------------------------------------------------------------------: | --------: |
| [L-01] | the conservator is not set until the owner calls the function setConservator() |         1 |
| [L-02] |                                 License Change                                 |         5 |
| [L-03] |                                 Version Change                                 |         5 |

### [L-01] Conservator Issue

In the contract BaseUSDO.sol, the conservator is not set until the owner calls the function setConservator(). As a result, the default value of conservator is address(0). To address this issue, it is recommended to set `conservator=owner` in the constructor itself. This will prevent potential security issues that may arise from leaving conservator address = address(0). Later, the owner can change or setConservator as needed.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L105-L109

```solidity
    function setConservator(address _conservator) external onlyOwner {
        require(_conservator != address(0), "USDO: address not valid");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }
```

#### Steps of Mitigation

```solidity
constructor(
address _lzEndpoint,
IYieldBoxBase _yieldBox,
address _owner,
address payable _leverageModule,
address payable _marketModule,
address payable _optionsModule
) BaseUSDOStorage(_lzEndpoint, _yieldBox) ERC20Permit("USDO") {
leverageModule = USDOLeverageModule(_leverageModule);
marketModule = USDOMarketModule(_marketModule);
optionsModule = USDOOptionsModule(_optionsModule);

   transferOwnership(_owner);
   conservator = owner ;
    }

```

### [L-02] License Change

Changing the license of a contract can have legal implications. It is important to ensure that the new license is compatible with the original license and that all contributors have agreed to the change.

```solidity
// SPDX-License-Identifier: UNLICENSED//@audit change the license to another one as UNLICENSED creates questions to trust the project
```

### [L-03] Version Change

Changing the version of Solidity used to compile a contract can introduce breaking changes or unexpected behavior. It is important to thoroughly test the contract with the new version before deploying it.

```solidity
pragma solidity >=0.6.0; //@audit pragma version
```
