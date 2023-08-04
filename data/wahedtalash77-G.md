
## [G-01] For uint use != 0 instead of > 0

`  if (supplyShare > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol#L159

`require(amount > 0, "USDO: amount not valid");`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol#L89

`  if (approvals.length > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L123

`  if (approvals.length > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L197

`  if (liquidationBonusAmount > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L233

`if (dexData.length > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L338

`if (_maximumTargetUtilization > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L506

` if (_interestElasticity > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L545

` if (_lqCollateralizationRate > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L553

`    if (_liquidationMultiplier > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L565

` if (supplyShare > 0) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L348

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol

## [G-02] Make for loop unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1 and is limited to the arrays length. Even if the arrays are extremely long, it will take a massive amount of time and gas to let the for loop overflow.
`for (uint256 i = 0; i < users.length; i++) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L384

`for (uint256 i = 0; i < calls.length; i++) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L187


`  for (uint256 i = 0; i < calls.length; i++) {`	
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L215

`for (uint256 i = 0; i < len; i++) {`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionLiquidityProvision.sol#L339

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L331C6-L338C14


## [G-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
The EVM operates with 32 byte words. Therefore, if you declare state variables less than 32 bytes the EVM will need to perform extra operations to cast your value to the specified size.
`uint8 internal _decimalCache;`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFTStorage.sol#L32

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L298C4-L326C56

## [G‑04] Using private rather than public for constants, saves gas
`uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4; // 50%`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L69

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L298C4-L326C56

```
 uint256 public constant PHASE_3_AMOUNT_PER_USER = 714;
    uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L84C4-L85C57

```
    uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;


    uint256 public constant EPOCH_DURATION = 2 days;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/option-airdrop/AirdropBroker.sol#L93C5-L95C53

```
  uint16 internal constant PT_LOCK_TWTAP = 870;
    uint16 internal constant PT_UNLOCK_TWTAP = 871;
    uint16 internal constant PT_CLAIM_REWARDS = 872;
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/BaseTapOFT.sol#L37C3-L39C53

## [G-05] Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
```
uint256 constant dMAX = 50 * 1e4; // 5% - 50% discount
    uint256 constant dMIN = 5 * 1e4;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L76C5-L77C37

```
 uint256 public constant A0 = 2 * 3 ** 3 * 10_000;
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/oracle/implementations/ARBTriCryptoOracle.sol#L41

## [G-06] you can save storage by reordering Holding struct fields in the following way.
```
struct Participation {
    uint256 averageMagnitude;
    uint88 tapAmount; // amount of TAP locked
    uint56 expiry; // expiry timestamp. Big enough for over 2 billion years..
    uint40 lastActive; // Last week that the staker shares in rewards
    uint40 lastInactive; // One week BEFORE the staker gets a share of rewards
    uint24 multiplier; // Votes = multiplier * tapAmount
    bool hasVotingPower;
    bool divergenceForce; // 0 negative, 1 positive
    bool tapReleased; // allow restaking while rewards may still accumulate
}
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L33C1-L43C2

```
struct CallValue {
        uint256 value;
        bytes callData;
        address target;
        bool allowFailure;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Multicall/Multicall3.sol#L29C5-L34C6

```
 struct MarketInfo {
        uint256 collateralId;
        uint256 assetId;
        uint256 totalCollateralShare;
        uint256 userCollateralShare;
        uint256 userBorrowPart;
        uint256 currentExchangeRate;
        uint256 spotExchangeRate;
        uint256 oracleExchangeRate;
        uint256 totalBorrowCap;
        uint256 totalYieldBoxCollateralShare;
        uint256 totalYieldBoxCollateralAmount;
        uint256 totalYieldBoxAssetShare;
        uint256 totalYieldBoxAssetAmount;
        uint256 yieldBoxAssetTokenId;
        uint256 yieldBoxCollateralTokenId;
        address collateral;
        address asset;
        address yieldBoxAssetContractAddress;
        address yieldBoxAssetStrategyAddress;
        address yieldBoxCollateralContractAddress;
        address yieldBoxCollateralStrategyAddress;
        Rebase totalBorrow;
        TokenType yieldBoxCollateralTokenType;
        TokenType yieldBoxAssetTokenType;
        IOracle oracle;
        bytes oracleData;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L31C4-L58C6


```
 struct Call {
        uint256 value;
        uint16 id;
        bytes call;
        bool allowFailure;
        address target;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L78C4-L84C6

```
struct PermitData {
        uint256 value;
        uint256 deadline;
        bytes32 r;
        bytes32 s;
        uint8 v;
        address owner;
        address spender;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L91C5-L99C6

```
struct PermitAllData {
        uint256 deadline;
        bytes32 r;
        bytes32 s;
        uint8 v;
        address owner;
        address spender;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L101C5-L108C6

```
struct TOFTSendToStrategyData {
        uint256 amount;
        uint256 share;
        uint256 assetId;
        uint16 lzDstChainId;
        address from;
        address to;
        ICommonData.ISendOptions options;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L140C5-L148C6

```
   struct TOFTRetrieveFromStrategyData {
        uint256 amount;
        uint256 share;
        uint256 assetId;
        uint16 lzDstChainId;
        bytes airdropAdapterParam;
        address from;
        address zroPaymentAddress;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L150C2-L158C6


```
struct YieldBoxDepositData {
        uint256 assetId;
        uint256 amount;
        uint256 share;
        address from;
        address to;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L160C5-L166C6

```
 struct HelperDepositRepayRemoveCollateral {
        uint256 depositAmount;
        uint256 repayAmount;
        uint256 collateralAmount;
        address market;
        address user;
        bool extractFromSender;
        ICommonData.IWithdrawParams withdrawCollateralParams;
    }
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Magnetar/MagnetarV2Storage.sol#L223C4-L231C6


## [G-07] Use double if statements instead of &&

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

```
if (!success && !_forwardRevert) {
            revert(_getRevertMsg(returnData));
        }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L370C9-L372C10

```
  if (!success && !_forwardRevert) {
            revert(_getRevertMsg(returnData));
        }
```
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol#L412C7-L414C10


## [G-08] Rearranging order of state variable declarations to pack them will save storage slots and gas in the following way.

```
    uint256 public interestElasticity;
 	  uint64 public minimumInterestPerSecond;
    uint64 public maximumInterestPerSecond;
    uint64 public startingInterestPerSecond;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L61C3-L64C45

## [G-09 ] The result of a function call should be cached

```
function flashFee(
        address token,
        uint256 amount
    ) public view override returns (uint256) {
        require(token == address(this), "USDO: token not valid");
        return (amount * flashMintFee) / FLASH_MINT_FEE_PRECISION;
    }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L64C5-L70C6

```
 return (total * (block.timestamp - start)) / duration;
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L172

```
 return ((timestamp - emissionsStartTime) / WEEK) + 1; // Starts at week 1
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L242

## [G-010] Use `calldata` keyword instead of `memory` keyword in function arguments

```
    function lend(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
			...
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L134C1-L140C15

```
   function exercise(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
	 ...
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L138C2-L144C15

```
   function _executeOnDestination(
        Module _module,
        bytes memory _data,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L375C2-L381C30

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/TapiocaOFT.sol#L37C8-L38C31



## [G-11] Use immutable for constant variables.

There are variables that are only assigned once (e.g. in a constructor). You should mark such variables with the keyword "immutable", this greatly reduces the gas costs. A concrete example of such a variable is "VADER" which is only initialized once and cannot be changed later:
VADER = _vader;

## [G-12] the block of code which is not used should be delete it.

```
114 function totalSupply() public view virtual override returns (uint256) {}
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol#L114

## [G-13]  Avoiding Initialization of the variables to it's default value.
` for (uint256 i = 0; i < approvals.length; ) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol#L244

`for (uint256 i = 0; i < approvals.length; ) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L245

```
uint256 needed = 0;
for (uint256 i = 0; i < maxBorrowParts.length; i++) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L44C17-L45C70

` uint256 minAssetAmount = 0;`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L337

```
  uint256 liquidatedCount = 0;
        for (uint256 i = 0; i < users.length; i++) {
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLLiquidation.sol#L383C7-L384C53
	
`for (uint256 i = 0; i < calls.length; i++) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L187	
	
`  for (uint256 i = 0; i < calls.length; i++) {`	
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L215
	
`uint256 public seeded = 0;`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L29
	
	
## [G-14]	use a  single file for `_callApproval` function  and then import it instead of creating `_callApproval` function every time.

```
function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }
    }
```

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOMarketModule.sol#L243C4-L298C6

```
   function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }
    }

```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOLeverageModule.sol#L254

```
   function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; ) {
            if (approvals[i].permitBorrow) {
                try
                    IPermitBorrow(approvals[i].target).permitBorrow(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try
                    IPermitAll(approvals[i].target).permitAll(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try
                    IERC20Permit(approvals[i].target).permit(
                        approvals[i].owner,
                        approvals[i].spender,
                        approvals[i].value,
                        approvals[i].deadline,
                        approvals[i].v,
                        approvals[i].r,
                        approvals[i].s
                    )
                {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }

            unchecked {
                ++i;
            }
        }
    }
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/modules/USDOOptionsModule.sol#L244C2-L299C6

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTMarketModule.sol#L260C4-L315

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L259C4-L314

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L284C4-L339

## [G-15]Remove the unnecessary `return` statement to save gas
```
54: return;
```
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol#L54

## [G-16] `variable == false ` instead of   `!variable`.
==a bit cheapier when you replace:==
639: `if (!success) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L639

474: ` if (!_isEthMarket) {`
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L474

163: `  if (!success) {`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L163

## [G-17] Events are not indexed

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/Penrose.sol#L150C4-L168C72

## [G-18] abi.encode() is less efficient than abi.encodePacked()
In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost
`bytes memory lzPayload = abi.encode(`
https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L60

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol#L102

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol#L90

https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol#L56

## [G-19] Make 3 event parameters indexed when possible
It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

[Reference](https://code4rena.com/reports/2023-01-drips#g-05-make-3-event-parameters-indexed-when-possible)

```
    event UserRegistered(address indexed user, uint256 amount);
    /// @notice event emitted when someone claims available tokens
    event Claimed(address indexed user, uint256 amount);

```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L60C1-L63C1

```
 event MinterUpdated(address indexed _old, address indexed _new);
    /// @notice event emitted when a new emission is called
    event Emitted(uint256 week, uint256 amount);
    /// @notice event emitted when new TAP is burned
    event Burned(address indexed _from, uint256 _amount);
    /// @notice event emitted when the governance chain identifier is updated
    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
    /// @notice event emitted when pause state is changed
    event PausedUpdated(bool oldState, bool newState);
```

https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/tokens/TapOFT.sol#L76C4-L86C55

`event SetTapOracle(IOracle oracle, bytes oracleData);`
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/TapiocaOptionBroker.sol#L131

## [G-20] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
	//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/governance/twTAP.sol#L474C8-L479C11

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L112

https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/BaseSwapper.sol#L103

## [G-21] Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard
Using true and false will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using 1 and 2 (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

```
 function swap(
        SwapData calldata swapData,
        uint256 amountOutMin,
        address to,
        bytes memory data
    )
        external
        override
        nonReentrant
        returns (uint256 amountOut, uint256 shareOut)
    {
        // Get tokens' addresses
        (address tokenIn, address tokenOut) = _getTokens(
            swapData.tokensData,
            yieldBox
        );
```
https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/023751a4e987cf7c203ab25d3abba58f7344f213/contracts/Swapper/UniswapV2Swapper.sol#L107C4-L122C11
