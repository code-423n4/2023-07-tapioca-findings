
## [L-01] Stop Using Solidity's transfer() Now
**Proof Of Concept**
https://consensys.io/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

```
160:  yieldBox.transfer(from, address(swapper), assetId, supplyShare);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L160

```
185:  emit Transfer(from, to, amount);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L185

```
215:   emit Transfer(address(0), to, fraction);
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L215

## [L-02] Keccak Constant values should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

```
38: bytes32 internal constant FLASH_MINT_CALLBACK_SUCCESS =
39:        keccak256("ERC3156FlashBorrower.onFlashLoan");
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L38C4-L39C55

```
bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
    bytes32 private constant _PERMIT_TYPEHASH_BORROW =
        keccak256(
            "PermitBorrow(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L29C1-L36C11

## [L03] Tapioca DAO is UNLICENSED it's better to choose a proper LICENSE.
`// SPDX-License-Identifier: UNLICENSED`
> all contest

## [L-04] Divide before multiply
Solidity's integer division truncates. Thus, performing division befor multiplication can lead to precision loss.
```
uint256 collateralAmountInAsset = yieldBox.toAmount(
            collateralId,
            (collateralShare *
                (EXCHANGE_RATE_PRECISION / FEE_PRECISION) *
                lqCollateralizationRate),
            false
        )
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L81C9-L87C10

## [L-05] Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:
```
 function harvestFees() external {
        for (uint256 i = 0; i < harvestableTapiocaOFTs.length; i++) {
            harvestableTapiocaOFTs[i].harvestFees();
        }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/TapiocaWrapper.sol#L97C5-L100C10

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## [L-06]  Empty receive()/payable fallback() function does not authenticate requests
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

644: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L644

43: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFTStorage.sol#L43

342: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L342

330: `receive() external payable virtual {}`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L330

# Non- Critical 

## [N-01] Use the delete keyword instead of assigning a value of 0
Using the ‘delete’ keyword instead of assigning a ‘0’ value is a detailed optimization that increases code readability and audit quality, and clearly indicates the intent.

Other hand, if use delete instead 0 value assign , it will be gas saved.

```
		extraAmount = 0;
        feeFraction = 0;
        logStartingInterest = false;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLCommon.sol#L51C9-L53C37

## [N-02] For immutable variables in Solidity, the convention is to use CAPS_CASE or UPPER_CASE_WITH_UNDERSCORES.

`IYieldBoxBase public immutable yieldBox;`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol#L19

```
  YieldBox public immutable yieldBox;
    /// @notice returns the TAP contract
    IERC20 public immutable tapToken;
    /// @notice returns TAP asset id registered in the YieldBox contract
    uint256 public immutable tapAssetId;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L41C2-L45C41

```
 IERC20 public immutable wethToken;
    /// @notice returns WETH asset id registered in the YieldBox contract
    uint256 public immutable wethAssetId;
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L51C4-L53C42

```
   IStargateRouter public immutable routerETH;
    /// @notice Stargate router address
    IStargateRouter public immutable router;
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/Balancer.sol#L59C2-L61C45

```
  uint256 public immutable emissionsStartTime;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L53

## [N-03] For modern and more readable code; update import usages

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.
*Recommendation*
`import {contract1 , contract2} from "filename.sol";`

```
4: import "./SGLLendingCommon.sol";
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCollateral.sol#L4

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLBorrow.sol#L4

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L5C1-L13C73

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L4C1-L6C25

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L4C1-L14C24

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol#L4

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L5C1-L17C33

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L8C1-L12C33

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L8C1-L15C33

> all contest have this issue

## [N-4] Showing the actual values of numbers in NatSpec comments makes checking and reading code easier
```
143:    uint256 internal constant FULL_UTILIZATION = 1e18;  // 1_000_000_000_000_000_000
144:    uint256 internal constant UTILIZATION_PRECISION = 1e18;  // 1_000_000_000_000_000_000

145:    uint256 internal constant FACTOR_PRECISION = 1e18;  // 1_000_000_000_000_000_000
```

```
37: uint256 internal constant FLASH_MINT_FEE_PRECISION = 1e6; // 1_000_000 
```



 ##  [N-05] Use a single file for all system-wide constants
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

## [N-06] Use SMTChecker
The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## [N-07] Use underscores for number literals
```
   -  uint256 constant decay_rate = 8800000000000000; // 0.88%
   +  uint256 constant decay_rate = 8_800_000_000_000_000; // 0.88%
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L45
```
- uint256 public constant WEEK = 604800;
+ uint256 public constant WEEK = 604_800;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L49

## [N-08] Empty blocks should be removed or Emit something
Code contains empty block
644: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L644

43: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFTStorage.sol#L43

342: `receive() external payable {}`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L342

330: `receive() external payable virtual {}`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L330

340: `receive() external payable virtual {}`
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Magnetar/MagnetarV2Storage.sol#L340

## [N-09] NatSpec comments should be increased in contracts
> all contest

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity Official documentation

in complext project such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html