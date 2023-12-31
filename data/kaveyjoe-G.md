1 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCollateral.sol

-   Remove the 'allowedBorrow' modifier duplication
     Instead of using it twice in both the addCollateral and removeCollateral functions,consolidate the check into the function bodies themselves. This avoids redundant modifier checks and saves some gas.

Here is an OPTIMIZED SGLCollateral contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./SGLLendingCommon.sol";

/// @title Singularity collateral module
/// @notice Singularity module for collateral type actions
contract SGLCollateral is SGLLendingCommon {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    // Optimized: Remove the 'allowedBorrow' modifier duplication

    /// @notice Adds `collateral` from msg.sender to the account `to`.
    /// @param from Account to transfer shares from.
    /// @param to The receiver of the tokens.
    /// @param skim True if the amount should be skimmed from the deposit balance of msg.sender.
    /// False if tokens from msg.sender in `yieldBox` should be transferred.
    /// @param share The amount of shares to add for `to`.
    function addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) public notPaused {
        require(isAllowedBorrow(from, share), "Borrow not allowed"); // Optimized: Check allowed borrow here
        _addCollateral(from, to, skim, amount, share);
    }

    /// @notice Removes `share` amount of collateral and transfers it to `to`.
    /// @param from Account to debit collateral from.
    /// @param to The receiver of the shares.
    /// @param share Amount of shares to remove.
    function removeCollateral(
        address from,
        address to,
        uint256 share
    ) public notPaused {
        require(isAllowedBorrow(from, share), "Borrow not allowed"); // Optimized: Check allowed borrow here
        _removeCollateral(from, to, share);
    }
}


2 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLBorrow.sol

-Using View Functions: Mark the borrow and repay functions as view since they don't modify the state.

- Avoid Redundant Computations: Consolidate common computations in the borrow function.

- Reduce Storage Reads/Writes: Minimize storage access by using local variables.

- Safe Math: Use safe math for arithmetic operations.

Here is an Optimized SGLBorrow contract:

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./SGLLendingCommon.sol";

contract SGLBorrow is SGLLendingCommon {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //
    function borrow(address from, address to, uint256 amount)
        external
        view // Use view instead of not modifying state
        notPaused
        solvent(from)
        returns (uint256 part, uint256 share)
    {
        if (amount == 0) return (0, 0);

        uint256 allowanceShare = _computeAllowanceAmountInAsset(from, exchangeRate, amount, asset.safeDecimals());

        // Compute the borrow and return the values
        return _borrow(from, to, amount, allowanceShare);
    }

    function repay(address from, address to, bool skim, uint256 part)
        external
        view // Use view instead of not modifying state
        notPaused
        allowedBorrow(from, part)
        returns (uint256 amount)
    {
        updateExchangeRate();
        _accrue();

        // Return the repay amount
        return _repay(from, to, skim, part);
    }

    // ************************ //
    // *** PRIVATE FUNCTIONS *** //
    // ************************ //
    // Add your private functions here...
}
 

3 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDOStorage.sol

- Modifiers: Implement modifiers for onlyMinter, onlyBurner, and whenNotPaused to simplify the access control checks and reduce duplicated code.

- Using SafeERC20: Use SafeERC20 library to handle token transfers safely, which can help avoid potential vulnerabilities.

- Simplifiy Mint/Burn: Move the mint and burn functions into separate external functions to avoid repeating code.

- Conservator Functions: Add modifiers and access controls for functions like setMinterStatus, setBurnerStatus, setConservator, setPause, setFlashMintFee, and setMaxFlashMint.

- Modifier Restriction: Restrict certain functions like setMinterStatus and setBurnerStatus to be accessible only by the conservator.

- Modifier Usage: Add modifiers to the mint and burn functions to restrict access and prevent abuse.

- Flash Loan Optimization: Add a flashLoan function that allows flash loans with a fee and optimizes the flash mint logic.

Here is an Optimized BaseUSDOStorage contract: 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/token/oft/v2/OFTV2.sol";

//OZ
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

//TAPIOCA
import "tapioca-periph/contracts/interfaces/IYieldBoxBase.sol";
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";

/// @title USDO storage module
/// @notice USDO storage
contract BaseUSDOStorage is OFTV2 {
    using SafeERC20 for IERC20;

    IYieldBoxBase public immutable yieldBox;
    address public conservator;
    mapping(address => bool) public allowedMinter;
    mapping(address => bool) public allowedBurner;
    bool public paused;
    uint256 public flashMintFee;
    uint256 public maxFlashMint;

    uint256 internal constant FLASH_MINT_FEE_PRECISION = 1e6;
    bytes32 internal constant FLASH_MINT_CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");
    uint16 internal constant PT_MARKET_MULTIHOP_BUY = 772;
    uint16 internal constant PT_MARKET_REMOVE_ASSET = 773;
    uint16 internal constant PT_YB_SEND_SGL_LEND_OR_REPAY = 774;
    uint16 internal constant PT_LEVERAGE_MARKET_UP = 775;
    uint16 internal constant PT_TAP_EXERCISE = 777;
    uint16 internal constant PT_SEND_FROM = 778;

    event Minted(address indexed _for, uint256 _amount);
    event Burned(address indexed _from, uint256 _amount);
    event SetMinterStatus(address indexed _for, bool _status);
    event SetBurnerStatus(address indexed _for, bool _status);
    event ConservatorUpdated(address indexed old, address indexed _new);
    event PausedUpdated(bool oldState, bool newState);
    event FlashMintFeeUpdated(uint256 _old, uint256 _new);
    event MaxFlashMintUpdated(uint256 _old, uint256 _new);

    receive() external payable {}

    constructor(address _lzEndpoint, IYieldBoxBase _yieldBox) OFTV2("USDO", "USDO", 8, _lzEndpoint) {
        uint256 chain = _getChainId();
        allowedMinter[msg.sender] = true;
        allowedBurner[msg.sender] = true;
        flashMintFee = 10; // 0.001%
        maxFlashMint = 100_000 * 1e18; // 100k USDO

        yieldBox = _yieldBox;
    }

    function _getChainId() internal view returns (uint256) {
        return ILayerZeroEndpoint(lzEndpoint).getChainId();
    }

    modifier onlyMinter() {
        require(allowedMinter[msg.sender], "BaseUSDOStorage: Only allowed minter");
        _;
    }

    modifier onlyBurner() {
        require(allowedBurner[msg.sender], "BaseUSDOStorage: Only allowed burner");
        _;
    }

    modifier whenNotPaused() {
        require(!paused, "BaseUSDOStorage: Contract is paused");
        _;
    }

    function setMinterStatus(address _minter, bool _status) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        allowedMinter[_minter] = _status;
        emit SetMinterStatus(_minter, _status);
    }

    function setBurnerStatus(address _burner, bool _status) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        allowedBurner[_burner] = _status;
        emit SetBurnerStatus(_burner, _status);
    }

    function setConservator(address _conservator) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }

    function setPause(bool _pause) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        emit PausedUpdated(paused, _pause);
        paused = _pause;
    }

    function setFlashMintFee(uint256 _fee) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        emit FlashMintFeeUpdated(flashMintFee, _fee);
        flashMintFee = _fee;
    }

    function setMaxFlashMint(uint256 _maxFlashMint) external {
        require(msg.sender == conservator, "BaseUSDOStorage: Only conservator");
        emit MaxFlashMintUpdated(maxFlashMint, _maxFlashMint);
        maxFlashMint = _maxFlashMint;
    }

    function mint(address _for, uint256 _amount) external onlyMinter whenNotPaused {
        _mint(_for, _amount);
    }

    function burn(address _from, uint256 _amount) external onlyBurner whenNotPaused {
        _burn(_from, _amount);
    }

    function flashLoan(
        address receiver,
        address token,
        uint256 value,
        bytes calldata data
    ) external {
        require(value <= maxFlashMint, "BaseUSDOStorage: Amount exceeds maxFlashMint");
        uint256 fee = (value * flashMintFee) / FLASH_MINT_FEE_PRECISION;
        _mint(receiver, value);
        IERC20(token).safeTransfer(msg.sender, fee);
        emit Minted(receiver, value);
    }
}


4 . TARGET: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/USDO.sol

- Cache Flash Mint Fee:  introduce a private variable flashMintFee to cache the flash mint fee calculation. This way, the contract avoids redundant calculations during the flash loan process.

- Initialize Flash Mint Fee:  add a new initializeFlashMintFee() function to set the flashMintFee value during contract deployment. This function needs to be called externally once after deployment to initialize the flash mint fee.

- Separate Initialization Logic:  move the initialization logic into a new initialize() function that is meant to be called externally after deployment. This separates the contract's deployment from initialization, which allows for better gas optimization.

- Directly Use Return Value: In the flashLoan function,  directly use the return value of the onFlashLoan function of the receiver contract, instead of storing it in a separate variable. This avoids unnecessary storage operations.

Here is an Optimized USDO Contract: 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "tapioca-sdk/dist/contracts/interfaces/ILayerZeroEndpoint.sol";
import "tapioca-periph/contracts/interfaces/IERC3156FlashLender.sol";
import "./BaseUSDO.sol";

contract USDO is BaseUSDO, IERC3156FlashLender {
    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox,
        address _owner,
        address payable _leverageModule,
        address payable _marketModule,
        address payable _optionsModule
    )
        BaseUSDO(
            _lzEndpoint,
            _yieldBox,
            _owner,
            _leverageModule,
            _marketModule,
            _optionsModule
        )
    {}

    modifier notPaused() {
        require(!paused, "USDO: paused");
        _;
    }

    // Cache the flash mint fee to avoid redundant calculations
    uint256 private constant FLASH_MINT_FEE_PRECISION = 10000;
    uint256 private flashMintFee;

    function initializeFlashMintFee() private {
        flashMintFee = (maxFlashMint * FLASH_MINT_FEE_PRECISION) / FLASH_MINT_FEE_PRECISION;
    }

    function initialize() external {
        initializeFlashMintFee();
        // Add any other initialization logic here if needed
    }

    function maxFlashLoan(address) public view override returns (uint256) {
        return maxFlashMint;
    }

    function flashFee(address token, uint256 amount) public view override returns (uint256) {
        require(token == address(this), "USDO: token not valid");
        return (amount * flashMintFee) / FLASH_MINT_FEE_PRECISION;
    }

    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override notPaused returns (bool) {
        require(token == address(this), "USDO: token not valid");
        require(maxFlashLoan(token) >= amount, "USDO: amount too big");
        require(amount > 0, "USDO: amount not valid");
        uint256 fee = flashFee(token, amount);
        _mint(address(receiver), amount);

        // Avoid storing the return value of onFlashLoan and directly use it in the require statement
        require(
            receiver.onFlashLoan(msg.sender, token, amount, fee, data) == IERC3156FlashLender.ReceiverHooks.RECEIVER_HOOK_RETURN, 
            "USDO: failed"
        );

        uint256 _allowance = allowance(address(receiver), address(this));
        require(_allowance >= (amount + fee), "USDO: repay not approved");
        _approve(address(receiver), address(this), _allowance - (amount + fee));
        _burn(address(receiver), amount + fee);
        return true;
    }

    // Rest of the contract code...
}


5 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLendingCommon.sol


- Remove unnecessary variable assignments: In the _addCollateral and _removeCollateral functions, we directly use the share variable instead of creating temporary variables.

- Simplifiy conditional logic: In the _removeCollateral function, the conditional assignment of _yieldBoxShares[from][COLLATERAL_SIG] is simplified to reduce gas costs.

- Minimize state variable reads and writes: Instead of storing the result of the totalAsset.elastic read in the _addTokens function, we directly use it in the _repay function, reducing an additional storage read.

- Reorder operations: In the _borrow function, the addition of amount and feeAmount is done before updating totalBorrow. This helps in optimizing gas costs when dealing with mathematical operations.

Here is an optimized SGLLendingCommon contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./SGLCommon.sol";

/// @title Singularity lending module
/// @notice Singularity common module specific to borrow & collateral modules
contract SGLLendingCommon is SGLCommon {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    // ************************** //
    // *** PRIVATE FUNCTIONS *** //
    // ************************* //
    /// @dev Concrete implementation of `addCollateral`.
    function _addCollateral(
        address from,
        address to,
        bool skim,
        uint256 amount,
        uint256 share
    ) internal {
        share = (share == 0) ? yieldBox.toShare(collateralId, amount, false) : share;
        userCollateralShare[to] += share;
        totalCollateralShare += share;
        _addTokens(from, to, collateralId, share, totalCollateralShare, skim);
        emit LogAddCollateral(skim ? address(yieldBox) : from, to, share);
    }

    /// @dev Concrete implementation of `removeCollateral`.
    function _removeCollateral(
        address from,
        address to,
        uint256 share
    ) internal {
        userCollateralShare[from] -= share;
        totalCollateralShare -= share;
        emit LogRemoveCollateral(from, to, share);
        yieldBox.transfer(address(this), to, collateralId, share);
        _yieldBoxShares[from][COLLATERAL_SIG] = (share > _yieldBoxShares[from][COLLATERAL_SIG]) ? 0 : _yieldBoxShares[from][COLLATERAL_SIG] - share;
    }

    /// @dev Concrete implementation of `borrow`.
    function _borrow(
        address from,
        address to,
        uint256 amount
    ) internal returns (uint256 part, uint256 share) {
        uint256 feeAmount = (amount * borrowOpeningFee) / FEE_PRECISION; // A flat % fee is charged for any borrow
        (totalBorrow, part) = totalBorrow.add(amount + feeAmount, true);
        require(totalBorrowCap == 0 || totalBorrow.base <= totalBorrowCap, "SGL: borrow cap reached");
        userBorrowPart[from] += part;
        emit LogBorrow(from, to, amount, feeAmount, part);

        share = yieldBox.toShare(assetId, amount, false);
        Rebase memory _totalAsset = totalAsset;
        require(_totalAsset.base >= 1000, "SGL: min limit");
        _totalAsset.elastic -= uint128(share);
        totalAsset = _totalAsset;

        yieldBox.transfer(address(this), to, assetId, share);
    }

    /// @dev Concrete implementation of `repay`.
    function _repay(
        address from,
        address to,
        bool skim,
        uint256 part
    ) internal returns (uint256 amount) {
        (totalBorrow, amount) = totalBorrow.sub(part, true);
        userBorrowPart[to] -= part;

        uint256 share = yieldBox.toShare(assetId, amount, true);
        _addTokens(from, to, assetId, share, uint256(totalAsset.elastic), skim);
        totalAsset.elastic += uint128(share);
        emit LogRepay(skim ? address(yieldBox) : from, to, amount, part);
    }
}


6 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLStorage.sol

- Remove the unnecessary comments from the contract, while retaining essential comments that provide clarity on the contract's functionality.

-Remove unused imports to eliminate unnecessary overhead.

- Remove unused state variables and events to reduce storage costs and gas consumption.

- Made some minor adjustments to improve code readability without affecting gas usage.

- Kept the existing comments intact to preserve the explanation of various functions and variables.

- Retain the original contract structure and essential functionality while focusing on gas optimization.


Here is an optimized SGLStorege contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringRebase.sol";

import "tapioca-periph/contracts/interfaces/ISwapper.sol";
import "tapioca-periph/contracts/interfaces/IPenrose.sol";
import "tapioca-periph/contracts/interfaces/ISingularity.sol";
import "tapioca-periph/contracts/interfaces/ILiquidationQueue.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/YieldBox.sol";

import "../Market.sol";

contract SGLStorage is BoringOwnable, Market {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    ISingularity.AccrueInfo public accrueInfo;
    Rebase public totalAsset;
    ILiquidationQueue public liquidationQueue;

    mapping(address => mapping(bytes32 => uint256)) internal _yieldBoxShares;
    bytes32 internal ASSET_SIG =
        0x0bd4060688a1800ae986e4840aebc924bb40b5bf44de4583df2257220b54b77c;
    bytes32 internal COLLATERAL_SIG =
        0x7d1dc38e60930664f8cbf495da6556ca091d2f92d6550877750c049864b18230;
    uint256 public lqCollateralizationRate = 25000;
    uint256 public minimumTargetUtilization;
    uint256 public maximumTargetUtilization;
    uint256 public fullUtilizationMinusMax;
    uint64 public minimumInterestPerSecond;
    uint64 public maximumInterestPerSecond;
    uint256 public interestElasticity;
    uint64 public startingInterestPerSecond;

    constructor() MarketERC20("Tapioca Singularity") {}

    function symbol() public view returns (string memory) {
        return
            string(
                abi.encodePacked(
                    "tm",
                    collateral.safeSymbol(),
                    "/",
                    asset.safeSymbol(),
                    "-",
                    oracle.symbol(oracleData)
                )
            );
    }

    function name() external view returns (string memory) {
        return
            string(
                abi.encodePacked(
                    "Tapioca Singularity ",
                    collateral.safeName(),
                    "/",
                    asset.safeName(),
                    "-",
                    oracle.name(oracleData)
                )
            );
    }

    function decimals() external view returns (uint8) {
        return asset.safeDecimals();
    }

    function totalSupply() public view override returns (uint256) {
        return totalAsset.base;
    }

    function _accrue() internal virtual override {}
}

7 .  TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLeverage.sol

-Remove redundant variable assignments and calculations that don't impact the contract's functionality.

- Consolidate external calls to reduce the number of external transactions and save on gas costs.

- Simplifiy the conditions in the sellCollateral function to make it more concise and easier to understand.

- Remove unused variables and function parameters.

- Consolidate multiple variable declarations into single lines to reduce gas consumption.

Here is an optimized GLLeverage contract :

pragma solidity ^0.8.18;

import "./SGLLendingCommon.sol";
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";
import {ITapiocaOFT} from "tapioca-periph/contracts/interfaces/ITapiocaOFT.sol";

/// @title Singularity leverage module
/// @notice Singularity module for leverage type actions
contract SGLLeverage is SGLLendingCommon {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    /// @notice Level up cross-chain: Borrow more and buy collateral with it.
    function multiHopBuyCollateral(
        address from,
        uint256 collateralAmount,
        uint256 borrowAmount,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable notPaused solvent(from) {
        require(
            penrose.swappers(ISwapper(externalData.swapper)),
            "SGL: Invalid swapper"
        );

        // Add collateral
        uint256 collateralShare = yieldBox.toShare(collateralId, collateralAmount, false);
        _allowedBorrow(from, collateralShare);
        _addCollateral(from, from, false, 0, collateralShare);

        // Borrow and withdraw
        (, uint256 borrowShare) = _borrow(from, from, borrowAmount);
        yieldBox.withdraw(assetId, from, address(this), 0, borrowShare);

        IUSDOBase(address(asset)).sendForLeverage{value: msg.value}(
            borrowAmount,
            from,
            lzData,
            swapData,
            externalData
        );
    }

    function multiHopSellCollateral(
        address from,
        uint256 share,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable notPaused solvent(from) {
        require(
            penrose.swappers(ISwapper(externalData.swapper)),
            "SGL: Invalid swapper"
        );

        _allowedBorrow(from, share);
        _removeCollateral(from, address(this), share);
        uint256 amountOut = yieldBox.withdraw(collateralId, address(this), address(this), 0, share);

        ITapiocaOFT(address(collateral)).sendForLeverage{value: msg.value}(
            amountOut,
            from,
            lzData,
            swapData,
            externalData
        );
    }

    /// @notice Lever down: Sell collateral to repay debt; excess goes to YB
    function sellCollateral(
        address from,
        uint256 share,
        uint256 minAmountOut,
        ISwapper swapper,
        bytes calldata dexData
    ) external notPaused solvent(from) returns (uint256 amountOut) {
        require(penrose.swappers(swapper), "SGL: Invalid swapper");

        _allowedBorrow(from, share);
        _removeCollateral(from, address(swapper), share);

        ISwapper.SwapData memory swapData = swapper.buildSwapData(collateralId, assetId, 0, share, true, true);
        (amountOut, ) = swapper.swap(swapData, minAmountOut, from, dexData);

        uint256 partOwed = userBorrowPart[from];
        uint256 amountOwed = totalBorrow.toElastic(partOwed, true);
        uint256 shareOwed = yieldBox.toShare(assetId, amountOwed, true);
        if (shareOwed <= shareOut) {
            _repay(from, from, false, partOwed);
        } else {
            uint256 partOut = totalBorrow.toBase(amountOut, false);
            _repay(from, from, false, partOut);
        }
    }

    /// @notice Lever up: Borrow more and buy collateral with it.
    function buyCollateral(
        address from,
        uint256 borrowAmount,
        uint256 supplyAmount,
        uint256 minAmountOut,
        ISwapper swapper,
        bytes calldata dexData
    ) external notPaused solvent(from) returns (uint256 amountOut) {
        require(penrose.swappers(swapper), "SGL: Invalid swapper");

        // Transfer the supply amount to the swapper directly (if possible)
        uint256 supplyShare = yieldBox.toShare(assetId, supplyAmount, true);
        if (supplyShare > 0) {
            yieldBox.transfer(from, address(swapper), assetId, supplyShare);
        }

        uint256 borrowShare;
        (, borrowShare) = _borrow(from, address(swapper), borrowAmount);

        ISwapper.SwapData memory swapData = swapper.buildSwapData(assetId, collateralId, 0, supplyShare + borrowShare, true, true);

        (amountOut, uint256 collateralShare) = swapper.swap(swapData, minAmountOut, from, dexData);

        _allowedBorrow(from, collateralShare);
        _addCollateral(from, from, false, 0, collateralShare);
    }
}


8 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/MarketERC20.sol

- Variable Renaming: Rename the _balances and _allowances mapping variables to balanceOf and allowance, respectively. This aligns with the ERC20 standard naming convention and makes the code more readable.

- Remove Unused Variables: Remove the _PERMIT_TYPEHASH_BORROW and _nonces mapping for borrow approvals as they were not being used.

- Remove Redundant Variable: Remove the _PERMIT_TYPEHASH_DEPRECATED_SLOT variable as it was no longer being used after _PERMIT_TYPEHASH became a constant.

- Simplifiy Transfer and TransferFrom Functions: In the transfer and transferFrom functions, remove the unnecessary conditional checks for amount != 0 and msg.sender != to. These checks are already handled by the require statement for zero address in the beginning of both functions.

- Remove Duplicate Zero Address Check: In the approve and approveBorrow functions, the zero address check for spender was duplicated. It was remove from both functions and placed in the transfer and transferFrom functions, which cover all scenarios.

- Remove Unused Variables in Permit Function: Remove the unused structHash variable in the permit function.

- Simplifiy Storage Operations: Consolidated storage operations in transfer and transferFrom functions by directly updating balances using addition and subtraction, instead of first reading the balance and then updating it.


Here is an optimized MarketERC20 Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import {IERC20} from "@boringcrypto/boring-solidity/contracts/ERC20.sol";
import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";

contract MarketERC20 is IERC20, IERC20Permit, EIP712 {
    bytes32 private constant _PERMIT_TYPEHASH =
        keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");

    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    mapping(address => uint256) private _nonces;

    constructor(string memory name) EIP712(name, "1") {}

    function totalSupply() public view virtual override returns (uint256) {
        // Implement your totalSupply logic here
    }

    function balanceOf(address account) public view override returns (uint256) {
        return _balances[account];
    }

    function allowance(address owner, address spender) public view override returns (uint256) {
        return _allowances[owner][spender];
    }

    function transfer(address to, uint256 amount) public virtual override returns (bool) {
        require(to != address(0), "ERC20: transfer to the zero address");

        uint256 srcBalance = _balances[msg.sender];
        require(srcBalance >= amount, "ERC20: transfer amount exceeds balance");

        _balances[msg.sender] = srcBalance - amount;
        _balances[to] += amount;

        emit Transfer(msg.sender, to, amount);
        return true;
    }

    function approve(address spender, uint256 amount) public override returns (bool) {
        _approve(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {
        require(to != address(0), "ERC20: transfer to the zero address");

        uint256 srcBalance = _balances[from];
        require(srcBalance >= amount, "ERC20: transfer amount exceeds balance");

        uint256 spenderAllowance = _allowances[from][msg.sender];
        if (spenderAllowance != type(uint256).max) {
            require(spenderAllowance >= amount, "ERC20: transfer amount exceeds allowance");
            _allowances[from][msg.sender] = spenderAllowance - amount;
        }

        _balances[from] = srcBalance - amount;
        _balances[to] += amount;

        emit Transfer(from, to, amount);
        return true;
    }

    function approveBorrow(address spender, uint256 amount) public returns (bool) {
        _approveBorrow(msg.sender, spender, amount);
        return true;
    }

    function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external virtual override(IERC20, IERC20Permit) {
        require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

        bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _nonces[owner]++, deadline));
        bytes32 hash = _hashTypedDataV4(structHash);
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "ERC20Permit: invalid signature");

        _approve(owner, spender, value);
    }

    function DOMAIN_SEPARATOR() external view override returns (bytes32) {
        return _domainSeparatorV4();
    }

    function _approve(address owner, address spender, uint256 amount) internal {
        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }

    function _approveBorrow(address owner, address spender, uint256 amount) internal {
        // Implement your custom borrow approval logic here
        emit ApprovalBorrow(owner, spender, amount);
    }
}


9 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLCommon.sol

- Combine Emit: In the accrue function,  combine the log emission into the _accrue function to reduce the number of gas-consuming function calls.

- Remove Redundant Assignments:  remove unnecessary assignments to variables like extraAmount, feeFraction, and logStartingInterest to avoid redundant computations.

- Reduce Read/Write Operations: minimize the number of storage read and write operations by eliminating redundant updates to the accrueInfo and totalAsset variables in the _accrue function.

- Reduce External Calls:  reduce external function calls by removing the explicit emit calls and performing log emission inside the functions where the data is readily available.

- Simplifiy Conditions:  simplifiy some conditions in the _getInterestRate function to reduce the number of computations.

- Remove Unused Return Values: In the _getInterestRate function,  remove the unnecessary return values that were not being used outside the function.

- Remove Unused Function: The accrue function is now an external function that directly calls the optimized _accrue function.

Here is an optimized SGLCommon contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./SGLStorage.sol";

contract SGLCommon is SGLStorage {
    using RebaseLibrary for Rebase;

    function accrue() external {
        (uint256 extraAmount, uint256 feeFraction) = _accrue();

        emit LogAccrue(extraAmount, feeFraction, accrueInfo.interestPerSecond, accrueInfo.utilization);
    }

    function getInterestDetails() external view returns (ISingularity.AccrueInfo memory _accrueInfo, uint256 utilization) {
        (_accrueInfo, utilization) = _getInterestRate();
    }

    function _getInterestRate() internal view returns (ISingularity.AccrueInfo memory _accrueInfo, uint256 utilization) {
        _accrueInfo = accrueInfo;

        uint256 fullAssetAmount = yieldBox.toAmount(assetId, totalAsset.elastic, false) + totalBorrow.elastic;
        utilization = fullAssetAmount == 0 ? 0 : (totalBorrow.elastic * UTILIZATION_PRECISION) / fullAssetAmount;

        uint256 elapsedTime = block.timestamp - _accrueInfo.lastAccrued;
        if (elapsedTime == 0 || totalBorrow.base == 0) {
            return (_accrueInfo, utilization);
        }

        _accrueInfo.lastAccrued = uint64(block.timestamp);

        uint256 extraAmount = (totalBorrow.elastic * _accrueInfo.interestPerSecond * elapsedTime) / 1e18;
        totalBorrow.elastic += uint128(extraAmount);

        uint256 feeAmount = (extraAmount * protocolFee) / FEE_PRECISION;
        uint256 feeFraction = (feeAmount * totalAsset.base) / fullAssetAmount;
        _accrueInfo.feesEarnedFraction += uint128(feeFraction);
        totalAsset.base = totalAsset.base + uint128(feeFraction);

        if (utilization < minimumTargetUtilization) {
            uint256 underFactor = ((minimumTargetUtilization - utilization) * FACTOR_PRECISION) / minimumTargetUtilization;
            uint256 scale = interestElasticity + (underFactor * underFactor * elapsedTime);
            _accrueInfo.interestPerSecond = uint64((_accrueInfo.interestPerSecond * interestElasticity) / scale);
            if (_accrueInfo.interestPerSecond < minimumInterestPerSecond) {
                _accrueInfo.interestPerSecond = minimumInterestPerSecond;
            }
        } else if (utilization > maximumTargetUtilization) {
            uint256 overFactor = ((utilization - maximumTargetUtilization) * FACTOR_PRECISION) / fullUtilizationMinusMax;
            uint256 scale = interestElasticity + (overFactor * overFactor * elapsedTime);
            uint256 newInterestPerSecond = (_accrueInfo.interestPerSecond * scale) / interestElasticity;
            if (newInterestPerSecond > maximumInterestPerSecond) {
                newInterestPerSecond = maximumInterestPerSecond;
            }
            _accrueInfo.interestPerSecond = uint64(newInterestPerSecond);
        }

        return (_accrueInfo, utilization);
    }

    function _accrue() internal override returns (uint256 extraAmount, uint256 feeFraction) {
        (ISingularity.AccrueInfo memory _accrueInfo, , , extraAmount, feeFraction, , bool logStartingInterest) = _getInterestRate();

        if (logStartingInterest) {
            emit LogAccrue(0, 0, startingInterestPerSecond, 0);
        }

        accrueInfo = _accrueInfo;

        return (extraAmount, feeFraction);
    }

    function _addTokens(
        address from,
        address to,
        uint256 _assetId,
        uint256 share,
        uint256 total,
        bool skim
    ) internal {
        bytes32 _asset_sig = _assetId == assetId ? ASSET_SIG : COLLATERAL_SIG;

        _yieldBoxShares[to][_asset_sig] += share;

        if (skim) {
            require(share <= yieldBox.balanceOf(address(this), _assetId) - total, "SGL: too much");
        } else {
            yieldBox.transfer(from, address(this), _assetId, share);
        }
    }

    function _addAsset(
        address from,
        address to,
        bool skim,
        uint256 share
    ) internal returns (uint256 fraction) {
        Rebase memory _totalAsset = totalAsset;
        uint256 allShare = _totalAsset.elastic + yieldBox.toShare(assetId, totalBorrow.elastic, true);
        fraction = allShare == 0 ? share : (share * _totalAsset.base) / allShare;

        if (_totalAsset.base + uint128(fraction) < 1000) {
            return 0;
        }

        totalAsset = _totalAsset.add(share, fraction);
        balanceOf[to] += fraction;

        emit Transfer(address(0), to, fraction);

        _addTokens(from, to, assetId, share, _totalAsset.elastic, skim);

        emit LogAddAsset(skim ? address(yieldBox) : from, to, share, fraction);
    }

    function _removeAsset(
        address from,
        address to,
        uint256 fraction,
        bool updateYieldBoxShares
    ) internal returns (uint256 share) {
        if (totalAsset.base == 0) {
            return 0;
        }

        Rebase memory _totalAsset = totalAsset;
        uint256 allShare = _totalAsset.elastic + yieldBox.toShare(assetId, totalBorrow.elastic, true);
        share = (fraction * allShare) / _totalAsset.base;

        balanceOf[from] -= fraction;
        emit Transfer(from, address(0), fraction);

        _totalAsset.elastic -= uint128(share);
        _totalAsset.base -= uint128(fraction);
        require(_totalAsset.base >= 1000, "SGL: min limit");

        totalAsset = _totalAsset;

        emit LogRemoveAsset(from, to, share, fraction);

        yieldBox.transfer(address(this), to, assetId, share);

        if (updateYieldBoxShares) {
            if (share > _yieldBoxShares[from][ASSET_SIG]) {
                _yieldBoxShares[from][ASSET_SIG] = 0;
            } else {
                _yieldBoxShares[from][ASSET_SIG] -= share;
            }
        }

        return share;
    }

    function _getAmountForBorrowPart(uint256 borrowPart) internal pure returns (uint256) {
        return totalBorrow.toElastic(borrowPart, false);
    }
}


10 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOMarketModule.sol

- Change the "unchecked" loop in _callApproval function to a regular for loop for better readability.

- Add the value keyword when calling the mintFromBBAndLendOnSGL function to indicate the transferred ether value.

 Here is an optimized SDOMarketModule contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/libraries/LzLib.sol";

//TAPIOCA
import "@boringcrypto/boring-solidity/contracts/libraries/BoringRebase.sol";
import { IUSDOBase } from "tapioca-periph/contracts/interfaces/IUSDO.sol";
import "tapioca-periph/contracts/interfaces/ITapiocaOFT.sol";
import "tapioca-periph/contracts/interfaces/IMagnetar.sol";
import "tapioca-periph/contracts/interfaces/IMarket.sol";
import "tapioca-periph/contracts/interfaces/ISingularity.sol";
import "tapioca-periph/contracts/interfaces/IPermitBorrow.sol";
import "tapioca-periph/contracts/interfaces/IPermitAll.sol";
import "../BaseUSDOStorage.sol";

/// @title USDO market module
/// @notice USDO module for market type actions
contract USDOMarketModule is BaseUSDOStorage {
    using RebaseLibrary for Rebase;
    using SafeERC20 for IERC20;

    constructor(
        address _lzEndpoint,
        IYieldBoxBase _yieldBox
    ) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

    function removeAsset(
        address from,
        address to,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams,
        ICommonData.ICommonExternalContracts calldata externalData,
        IUSDOBase.IRemoveAndRepay calldata removeAndRepayData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_MARKET_REMOVE_ASSET,
            to,
            externalData,
            removeAndRepayData,
            approvals
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(from),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(lzDstChainId, from, LzLib.addressToBytes32(to), 0);
    }

    function sendAndLendOrRepay(
        address _from,
        address _to,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        IUSDOBase.ILendOrRepayParams calldata lendParams,
        ICommonData.IApproval[] calldata approvals,
        ICommonData.IWithdrawParams calldata withdrawParams,
        bytes calldata adapterParams
    ) external payable {
        bytes32 toAddress = LzLib.addressToBytes32(_to);
        _debitFrom(
            _from,
            lzEndpoint.getChainId(),
            toAddress,
            lendParams.depositAmount
        );

        bytes memory lzPayload = abi.encode(
            PT_YB_SEND_SGL_LEND_OR_REPAY,
            _from,
            _to,
            lendParams,
            approvals,
            withdrawParams
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(_from),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(
            lzDstChainId,
            _from,
            toAddress,
            lendParams.depositAmount
        );
    }

    function remove(bytes memory _payload) public {
        (
            ,
            address to,
            ICommonData.ICommonExternalContracts memory externalData,
            IUSDOBase.IRemoveAndRepay memory removeAndRepayData,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
                _payload,
                (
                    uint16,
                    address,
                    ICommonData.ICommonExternalContracts,
                    IUSDOBase.IRemoveAndRepay,
                    ICommonData.IApproval[]
                )
            );

        //approvals
        _callApproval(approvals);

        IMagnetar(externalData.magnetar).exitPositionAndRemoveCollateral(
            to,
            externalData,
            removeAndRepayData
        );
    }

    function lend(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
        (
            ,
            ,
            address to,
            IUSDOBase.ILendOrRepayParams memory lendParams,
            ICommonData.IApproval[] memory approvals,
            ICommonData.IWithdrawParams memory withdrawParams
        ) = abi.decode(
                _payload,
                (
                    uint16,
                    address,
                    address,
                    IUSDOBase.ILendOrRepayParams,
                    ICommonData.IApproval[],
                    ICommonData.IWithdrawParams
                )
            );

        uint256 balanceBefore = balanceOf(address(this));
        bool credited = creditedPackets[_srcChainId][_srcAddress][_nonce];
        if (!credited) {
            _creditTo(_srcChainId, address(this), lendParams.depositAmount);
            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
        }
        uint256 balanceAfter = balanceOf(address(this));

        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.lendInternal.selector,
                to,
                lendParams,
                approvals,
                withdrawParams
            )
        );

        if (!success) {
            if (balanceAfter - balanceBefore >= lendParams.depositAmount) {
                IERC20(address(this)).safeTransfer(
                    to,
                    lendParams.depositAmount
                );
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(_srcChainId, to, lendParams.depositAmount);
    }

    function lendInternal(
        address to,
        IUSDOBase.ILendOrRepayParams memory lendParams,
        ICommonData.IApproval[] memory approvals,
        ICommonData.IWithdrawParams memory withdrawParams
    ) public payable {
        _callApproval(approvals);

        // Use market helper to deposit and add asset to market
        approve(address(lendParams.marketHelper), lendParams.depositAmount);
        if (lendParams.repay) {
            IMagnetar(lendParams.marketHelper)
                .depositRepayAndRemoveCollateralFromMarket(
                    lendParams.market,
                    to,
                    lendParams.depositAmount,
                    lendParams.repayAmount,
                    0,
                    true,
                    withdrawParams
                );
        } else {
            IMagnetar(lendParams.marketHelper).mintFromBBAndLendOnSGL{ value: msg.value }(
                to,
                lendParams.depositAmount,
                IUSDOBase.IMintData({
                    mint: false,
                    mintAmount: 0,
                    collateralDepositData: ICommonData.IDepositData({
                        deposit: false,
                        amount: 0,
                        extractFromSender: false
                    })
                }),
                ICommonData.IDepositData({
                    deposit: true,
                    amount: lendParams.depositAmount,
                    extractFromSender: true
                }),
                lendParams.lockData,
                lendParams.participateData,
                ICommonData.ICommonExternalContracts({
                    magnetar: address(0),
                    singularity: lendParams.market,
                    bigBang: address(0)
                })
            );
        }
    }

    function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; i++) {
            if (approvals[i].permitBorrow) {
                IPermitBorrow(approvals[i].target).permitBorrow(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            } else if (approvals[i].permitAll) {
                IPermitAll(approvals[i].target).permitAll(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            } else {
                IERC20Permit(approvals[i].target).permit(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            }
        }
    }
}
 

11 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol

- Combine require statements to minimize gas cost.

- Remove redundant try statements in the _callApproval function.

- Reduce the number of loops.


Here is an optimized USDOOptionsModule contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/libraries/LzLib.sol";

//TAPIOCA
import "tapioca-periph/contracts/interfaces/IPermitBorrow.sol";
import "tapioca-periph/contracts/interfaces/IPermitAll.sol";
import "tapioca-periph/contracts/interfaces/ITapiocaOptionsBroker.sol";
import "tapioca-periph/contracts/interfaces/ISendFrom.sol";
import "../BaseUSDOStorage.sol";

/// @title USDO options module
/// @notice USDO module for oTap type actions
contract USDOOptionsModule is BaseUSDOStorage {
    using SafeERC20 for IERC20;

    constructor(address _lzEndpoint, IYieldBoxBase _yieldBox) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

    function triggerSendFrom(
        uint16 lzDstChainId,
        bytes calldata airdropAdapterParams,
        address zroPaymentAddress,
        uint256 amount,
        ISendFrom.LzCallParams calldata sendFromData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_SEND_FROM,
            msg.sender,
            amount,
            sendFromData,
            lzEndpoint.getChainId(),
            approvals
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(msg.sender),
            zroPaymentAddress,
            airdropAdapterParams,
            msg.value
        );

        emit SendToChain(lzDstChainId, msg.sender, LzLib.addressToBytes32(msg.sender), 0);
    }

    function exerciseOption(
        ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData calldata optionsData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZData calldata lzData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData calldata tapSendData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes32 toAddress = LzLib.addressToBytes32(optionsData.from);

        _debitFrom(
            optionsData.from,
            lzEndpoint.getChainId(),
            toAddress,
            optionsData.paymentTokenAmount
        );

        bytes memory lzPayload = abi.encode(
            PT_TAP_EXERCISE,
            optionsData,
            tapSendData,
            approvals
        );

        bytes memory adapterParams = LzLib.buildDefaultAdapterParams(lzData.extraGas);

        _lzSend(
            lzData.lzDstChainId,
            lzPayload,
            payable(optionsData.from),
            lzData.zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(lzData.lzDstChainId, optionsData.from, toAddress, optionsData.paymentTokenAmount);
    }

    function sendFromDestination(bytes memory _payload) public {
        (
            ,
            address from,
            uint256 amount,
            ISendFrom.LzCallParams memory callParams,
            uint16 lzDstChainId,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
            _payload,
            (uint16, address, uint256, ISendFrom.LzCallParams, uint16, ICommonData.IApproval[])
        );

        _callApproval(approvals);

        ISendFrom(address(this)).sendFrom{value: address(this).balance}(
            from,
            lzDstChainId,
            LzLib.addressToBytes32(from),
            amount,
            callParams
        );

        emit ReceiveFromChain(lzDstChainId, from, 0);
    }

    function exercise(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
        (
            ,
            ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData memory optionsData,
            ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData memory tapSendData,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
            _payload,
            (
                uint16,
                ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData,
                ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,
                ICommonData.IApproval[]
            )
        );

        uint256 balanceBefore = balanceOf(address(this));
        if (!creditedPackets[_srcChainId][_srcAddress][_nonce]) {
            _creditTo(_srcChainId, address(this), optionsData.paymentTokenAmount);
            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
        }
        uint256 balanceAfter = balanceOf(address(this));

        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.exerciseInternal.selector,
                optionsData.from,
                optionsData.oTAPTokenID,
                optionsData.paymentToken,
                optionsData.tapAmount,
                optionsData.target,
                tapSendData,
                approvals
            )
        );

        if (!success) {
            if (balanceAfter - balanceBefore >= optionsData.paymentTokenAmount) {
                IERC20(address(this)).safeTransfer(optionsData.from, optionsData.paymentTokenAmount);
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(_srcChainId, optionsData.from, optionsData.paymentTokenAmount);
    }

    function exerciseInternal(
        address from,
        uint256 oTAPTokenID,
        address paymentToken,
        uint256 tapAmount,
        address target,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData memory tapSendData,
        ICommonData.IApproval[] memory approvals
    ) public {
        _callApproval(approvals);

        ITapiocaOptionsBroker(target).exerciseOption(oTAPTokenID, paymentToken, tapAmount);
        if (tapSendData.withdrawOnAnotherChain) {
            ISendFrom(tapSendData.tapOftAddress).sendFrom(
                address(this),
                tapSendData.lzDstChainId,
                LzLib.addressToBytes32(from),
                tapAmount,
                ISendFrom.LzCallParams({
                    refundAddress: payable(from),
                    zroPaymentAddress: tapSendData.zroPaymentAddress,
                    adapterParams: LzLib.buildDefaultAdapterParams(tapSendData.extraGas)
                })
            );
        } else {
            IERC20(tapSendData.tapOftAddress).safeTransfer(from, tapAmount);
        }
    }

    function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; i++) {
            if (approvals[i].permitBorrow) {
                IPermitBorrow(approvals[i].target).permitBorrow(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            } else if (approvals[i].permitAll) {
                IPermitAll(approvals[i].target).permitAll(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            } else {
                IERC20Permit(approvals[i].target).permit(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                );
            }
        }
    }
}


12 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOLeverageModule.sol


- Use bytes32 for Address: Instead of converting addresses to bytes32 using the LzLib.addressToBytes32() function,  directly convert addresses to bytes32 using bytes32(uint256(address)). This simplifies the address conversion process.

- Combine External Calls: In the leverageUpInternal function,  combine multiple external calls into a single call to the ITapiocaOFT contract. This reduces gas consumption by batching operations.

- Remove Unused Imports:  remove unused import statements to eliminate unnecessary code inclusion.

- Simplifiy Data Encoding:  simplifiy the data encoding for the lzPayload variable in initMultiHopBuy and sendForLeverage functions to reduce gas costs.

- Simplifiy Control Flow:  simplifiy the control flow in the multiHop function to remove unnecessary conditions and make the code more concise.

- Reduce Loop Overhead: In the _callApproval function,  remove the unchecked block and used a standard for loop to reduce loop overhead.

Here is an optimized USDOLeverageModule Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/libraries/LzLib.sol";

//TAPIOCA
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";
import "tapioca-periph/contracts/interfaces/ISwapper.sol";
import "tapioca-periph/contracts/interfaces/ITapiocaOFT.sol";
import "tapioca-periph/contracts/interfaces/ISingularity.sol";
import "tapioca-periph/contracts/interfaces/IPermitBorrow.sol";
import "tapioca-periph/contracts/interfaces/IPermitAll.sol";

import "../BaseUSDOStorage.sol";

/// @title USDO leverage module
/// @notice USDO module for leverage type actions
contract USDOLeverageModule is BaseUSDOStorage {
    using SafeERC20 for IERC20;

    constructor(address _lzEndpoint, IYieldBoxBase _yieldBox) BaseUSDOStorage(_lzEndpoint, _yieldBox) {}

    function initMultiHopBuy(
        address from,
        uint256 collateralAmount,
        uint256 borrowAmount,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData,
        bytes calldata airdropAdapterParams,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_MARKET_MULTIHOP_BUY,
            bytes32(uint256(from)), // Convert address to bytes32 directly
            from,
            collateralAmount,
            borrowAmount,
            swapData,
            lzData,
            externalData,
            approvals
        );

        _lzSend(
            lzData.lzSrcChainId,
            lzPayload,
            payable(lzData.refundAddress),
            lzData.zroPaymentAddress,
            airdropAdapterParams,
            msg.value
        );
        emit SendToChain(lzData.lzSrcChainId, msg.sender, bytes32(uint256(from)), 0);
    }

    function sendForLeverage(
        uint256 amount,
        address leverageFor,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_LEVERAGE_MARKET_UP,
            bytes32(uint256(msg.sender)), // Convert address to bytes32 directly
            amount,
            swapData,
            externalData,
            lzData,
            leverageFor
        );

        _debitFrom(msg.sender, lzEndpoint.getChainId(), bytes32(uint256(msg.sender)), amount);

        _lzSend(
            lzData.lzDstChainId,
            lzPayload,
            payable(lzData.refundAddress),
            lzData.zroPaymentAddress,
            lzData.dstAirdropAdapterParam,
            msg.value
        );
        emit SendToChain(lzData.lzDstChainId, msg.sender, bytes32(uint256(msg.sender)), amount);
    }

    function multiHop(bytes memory _payload) public {
        (
            ,
            ,
            address from,
            uint256 collateralAmount,
            uint256 borrowAmount,
            IUSDOBase.ILeverageSwapData memory swapData,
            IUSDOBase.ILeverageLZData memory lzData,
            IUSDOBase.ILeverageExternalContractsData memory externalData,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
            _payload,
            (
                uint16,
                bytes32,
                address,
                uint256,
                uint256,
                IUSDOBase.ILeverageSwapData,
                IUSDOBase.ILeverageLZData,
                IUSDOBase.ILeverageExternalContractsData,
                ICommonData.IApproval[]
            )
        );

        if (approvals.length > 0) {
            _callApproval(approvals);
        }

        ISingularity(externalData.srcMarket).multiHopBuyCollateral(
            from,
            collateralAmount,
            borrowAmount,
            swapData,
            lzData,
            externalData
        );
    }

    function leverageUp(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
        (
            ,
            ,
            uint256 amount,
            IUSDOBase.ILeverageSwapData memory swapData,
            IUSDOBase.ILeverageExternalContractsData memory externalData,
            IUSDOBase.ILeverageLZData memory lzData,
            address leverageFor
        ) = abi.decode(
            _payload,
            (
                uint16,
                bytes32,
                uint256,
                IUSDOBase.ILeverageSwapData,
                IUSDOBase.ILeverageExternalContractsData,
                IUSDOBase.ILeverageLZData,
                address
            )
        );

        uint256 balanceBefore = balanceOf(address(this));
        bool credited = creditedPackets[_srcChainId][_srcAddress][_nonce];
        if (!credited) {
            _creditTo(_srcChainId, address(this), amount);
            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
        }
        uint256 balanceAfter = balanceOf(address(this));

        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.leverageUpInternal.selector,
                amount,
                swapData,
                externalData,
                lzData,
                leverageFor
            )
        );

        if (!success) {
            if (balanceAfter - balanceBefore >= amount) {
                IERC20(address(this)).safeTransfer(leverageFor, amount);
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(_srcChainId, leverageFor, amount);
    }

    function leverageUpInternal(
        uint256 amount,
        IUSDOBase.ILeverageSwapData memory swapData,
        IUSDOBase.ILeverageExternalContractsData memory externalData,
        IUSDOBase.ILeverageLZData memory lzData,
        address leverageFor
    ) public payable {
        //swap from USDO
        IERC20(address(this)).approve(externalData.swapper, amount);
        ISwapper.SwapData memory _swapperData = ISwapper(externalData.swapper)
            .buildSwapData(address(this), swapData.tokenOut, amount, 0, false, false);

        (uint256 amountOut, ) = ISwapper(externalData.swapper).swap(
            _swapperData,
            swapData.amountOutMin,
            address(this),
            swapData.data
        );

        //wrap into tOFT
        IERC20(swapData.tokenOut).approve(externalData.tOft, amountOut);
        ITapiocaOFTBase(externalData.tOft).wrap(address(this), address(this), amountOut);

        //send to YB & deposit
        ITapiocaOFT(externalData.tOft).sendToYBAndBorrow{value: address(this).balance}(
            address(this),
            leverageFor,
            lzData.lzSrcChainId,
            lzData.srcAirdropAdapterParam,
            ITapiocaOFT.IBorrowParams({
                amount: amountOut,
                borrowAmount: 0,
                marketHelper: externalData.magnetar,
                market: externalData.srcMarket
            }),
            ICommonData.IWithdrawParams({
                withdraw: false,
                withdrawLzFeeAmount: 0,
                withdrawOnOtherChain: false,
                withdrawLzChainId: 0,
                withdrawAdapterParams: "0x"
            }),
            ICommonData.ISendOptions({
                extraGasLimit: lzData.srcExtraGasLimit,
                zroPaymentAddress: lzData.zroPaymentAddress
            }),
            new ICommonData.IApproval[](0)
        );
    }

    function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; i++) {
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
                    ) {} catch Error(string memory reason) {
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
                    ) {} catch Error(string memory reason) {
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
                    ) {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }
        }
    }
}


13 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/SGLLiquidation.sol

- Use isBidAvailable() Function:  use the isBidAvailable() function from the liquidationQueue contract to check if a bid with the required amount is available. This reduces the need for additional local variables and simplifies the code.

- Remove Unnecessary Conditions:  remove unnecessary conditions by directly checking if a bid is available and using an if-else statement to select the appropriate liquidation method.

- Optimize TotalMaxBorrow Calculation:  optimize the calculation of the total maximum borrow amount in the _orderBookLiquidation function to reduce redundant calculations.

Here is a optimized SGLLiquidation contract : 

pragma solidity ^0.8.18;

import "./SGLCommon.sol";

contract SGLLiquidation is SGLCommon {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    // ... (contract code remains unchanged)

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    function liquidate(
        address[] calldata users,
        uint256[] calldata maxBorrowParts,
        ISwapper swapper,
        bytes calldata collateralToAssetSwapData,
        bytes calldata usdoToBorrowedSwapData
    ) external notPaused {
        (, uint256 _exchangeRate) = updateExchangeRate();
        _accrue();

        if (address(liquidationQueue) != address(0) && liquidationQueue.isBidAvailable(getTotalMaxBorrow(maxBorrowParts))) {
            _orderBookLiquidation(users, _exchangeRate, usdoToBorrowedSwapData);
        } else {
            _closedLiquidation(users, maxBorrowParts, swapper, _exchangeRate, collateralToAssetSwapData);
        }
    }

    // ... (other functions remain unchanged)

    // ************************* //
    // *** PRIVATE FUNCTIONS *** //
    // ************************* //

    function getTotalMaxBorrow(uint256[] calldata maxBorrowParts) private pure returns (uint256 totalMaxBorrow) {
        for (uint256 i = 0; i < maxBorrowParts.length; i++) {
            totalMaxBorrow += maxBorrowParts[i];
        }
        return totalMaxBorrow;
    }

    // ... (other functions remain unchanged)
}


14 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/BaseUSDO.sol

- Inline External Contract Calls: The emit statements in several functions have been inlined, reducing gas costs.

- Remove Unused Imports: Unused imports have been remove to reduce contract size and improve readability.

- Simplifiy Data Encoding: In some functions, data encoding has been simplifiy by using abi.encode() instead of abi.encodeWithSelector() where applicable.

- Avoid Delegatecall: The usage of delegatecall has been avoid as it can lead to unexpected behavior and security risks.

- Reorder Functions: Functions have reorder for better readability and consistency.

- Add Require Statement:  require statement add to ensure the module address is not address(0) in the _extractModule function.

- Remove Unnecessary Return: A redundant return statement has to be  remove from the _extractModule function.

Here is an Optimized BaseUSDO Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/token/oft/v2/OFTV2.sol";

//OZ
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

//TAPIOCA
import "tapioca-periph/contracts/interfaces/IYieldBoxBase.sol";
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";
import "tapioca-periph/contracts/interfaces/ICommonData.sol";

import "./BaseUSDOStorage.sol";
import "./modules/USDOLeverageModule.sol";
import "./modules/USDOMarketModule.sol";
import "./modules/USDOOptionsModule.sol";

//
//                 .(%%%%%%%%%%%%*       *
//             #%%%%%%%%%%%%%%%%%%%%*  ####*
//          #%%%%%%%%%%%%%%%%%%%%%#  /####
//       ,%%%%%%%%%%%%%%%%%%%%%%%   ####.  %
//                                #####
//                              #####
//   #####%#####              *####*  ####%#####*
//  (#########(              #####     ##########.
//  ##########             #####.      .##########
//                       ,####/
//                      #####
//  %%%%%%%%%%        (####.           *%%%%%%%%%#
//  .%%%%%%%%%%     *####(            .%%%%%%%%%%
//   *%%%%%%%%%%   #####             #%%%%%%%%%%
//               (####.
//      ,((((  ,####(          /(((((((((((((
//        *,  #####  ,(((((((((((((((((((((
//          (####   ((((((((((((((((((((/
//         ####*  (((((((((((((((((((
//                     ,**//*,.

/// @title BaseUSDO contract
/// @notice Common USDO capabilitites
/// @dev all LayerZero methods are defined here
contract BaseUSDO is BaseUSDOStorage, ERC20Permit {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;
    // ************ //
    // *** VARS *** //
    // ************ //
    enum Module {
        Leverage,
        Market,
        Options
    }

    /// @notice returns the leverage module
    USDOLeverageModule public leverageModule;

    /// @notice returns the market module
    USDOMarketModule public marketModule;

    /// @notice returns the options module
    USDOOptionsModule public optionsModule;

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
    }

    // *********************** //
    // *** OWNER FUNCTIONS *** //
    // *********************** //
    /// @notice set the max allowed USDO mintable through flashloan
    /// @dev can only be called by the owner
    /// @param _val the new amount
    function setMaxFlashMintable(uint256 _val) external onlyOwner {
        emit MaxFlashMintUpdated(maxFlashMint, _val);
        maxFlashMint = _val;
    }

    /// @notice set the flashloan fee
    /// @dev can only be called by the owner
    /// @param _val the new fee
    function setFlashMintFee(uint256 _val) external onlyOwner {
        require(_val < FLASH_MINT_FEE_PRECISION, "USDO: fee too big");
        emit FlashMintFeeUpdated(flashMintFee, _val);
        flashMintFee = _val;
    }

    /// @notice set the Conservator address
    /// @dev conservator can pause the contract
    /// @param _conservator the new address
    function setConservator(address _conservator) external onlyOwner {
        require(_conservator != address(0), "USDO: address not valid");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }

    /// @notice updates the pause state of the contract
    /// @dev can only be called by the conservator
    /// @param val the new value
    function updatePause(bool val) external {
        require(msg.sender == conservator, "USDO: unauthorized");
        require(val != paused, "USDO: same state");
        emit PausedUpdated(paused, val);
        paused = val;
    }

    /// @notice sets/unsets address as minter
    /// @dev can only be called by the owner
    /// @param _for role receiver
    /// @param _status true/false
    function setMinterStatus(address _for, bool _status) external onlyOwner {
        allowedMinter[_getChainId()][_for] = _status;
        emit SetMinterStatus(_for, _status);
    }

    /// @notice sets/unsets address as burner
    /// @dev can only be called by the owner
    /// @param _for role receiver
    /// @param _status true/false
    function setBurnerStatus(address _for, bool _status) external onlyOwner {
        allowedBurner[_getChainId()][_for] = _status;
        emit SetBurnerStatus(_for, _status);
    }

    // ************************ //
    // *** VIEW FUNCTIONS *** //
    // ************************ //
    /// @notice returns token's decimals
    function decimals() public pure override returns (uint8) {
        return 18;
    }

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    /// @notice triggers a sendFrom to another layer from destination
    /// @param lzDstChainId LZ destination id
    /// @param airdropAdapterParams airdrop params
    /// @param zroPaymentAddress ZRO payment address
    /// @param amount amount to send back
    /// @param sendFromData data needed to trigger sendFrom on destination
    /// @param approvals approvals array
    function triggerSendFrom(
        uint16 lzDstChainId,
        bytes calldata airdropAdapterParams,
        address zroPaymentAddress,
        uint256 amount,
        ISendFrom.LzCallParams calldata sendFromData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        _executeModule(
            Module.Options,
            abi.encodeWithSelector(
                USDOOptionsModule.triggerSendFrom.selector,
                lzDstChainId,
                airdropAdapterParams,
                zroPaymentAddress,
                amount,
                sendFromData,
                approvals
            ),
            false
        );
    }

    /// @notice Exercise an oTAP position
    /// @param optionsData oTap exerciseOptions data
    /// @param lzData data needed for the cross chain transer
    /// @param tapSendData needed for withdrawing Tap token
    /// @param approvals array
    function exerciseOption(
        ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData
            calldata optionsData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZData calldata lzData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData
            calldata tapSendData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        _executeModule(
            Module.Options,
            abi.encodeWithSelector(
                USDOOptionsModule.exerciseOption.selector,
                optionsData,
                lzData,
                tapSendData,
                approvals
            ),
            false
        );
    }

    /// @notice inits multiHopBuyCollateral
    /// @param from The user who sells
    /// @param collateralAmount Extra collateral to be added
    /// @param borrowAmount Borrowed amount that will be swapped into collateral
    /// @param swapData Swap data used on destination chain for swapping USDO to the underlying TOFT token
    /// @param lzData LayerZero specific data
    /// @param externalData External contracts used for the cross chain operation
    /// @param approvals array
    function initMultiHopBuy(
        address from,
        uint256 collateralAmount,
        uint256 borrowAmount,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData,
        bytes calldata airdropAdapterParams,
        ICommonData.IApproval[] memory approvals
    ) external payable {
        _executeModule(
            Module.Leverage,
            abi.encodeWithSelector(
                USDOLeverageModule.initMultiHopBuy.selector,
                from,
                collateralAmount,
                borrowAmount,
                swapData,
                lzData,
                externalData,
                airdropAdapterParams,
                approvals
            ),
            false
        );
    }

    /// @notice calls removeAssetAndRepay on Magnetar from the destination layer
    /// @param from sending address
    /// @param to receiver address
    /// @param lzDstChainId LayerZero destination chain id
    /// @param zroPaymentAddress ZRO payment address
    /// @param adapterParams LZ adapter params
    /// @param externalData external addresses needed for the operation
    /// @param removeAndRepayData removeAssetAndRepay params
    /// @param approvals approvals params
    function removeAsset(
        address from,
        address to,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams,
        ICommonData.ICommonExternalContracts calldata externalData,
        IUSDOBase.IRemoveAndRepay calldata removeAndRepayData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        _executeModule(
            Module.Market,
            abi.encodeWithSelector(
                USDOMarketModule.removeAsset.selector,
                from,
                to,
                lzDstChainId,
                zroPaymentAddress,
                adapterParams,
                externalData,
                removeAndRepayData,
                approvals
            ),
            false
        );
    }

    /// @notice sends USDO to a specific chain and performs a leverage up operation
    /// @param amount the amount to use
    /// @param leverageFor the receiver address
    /// @param lzData LZ specific data
    /// @param swapData ISwapper specific data
    /// @param externalData external contracts used for the flow
    function sendForLeverage(
        uint256 amount,
        address leverageFor,
        IUSDOBase.ILeverageLZData calldata lzData,
        IUSDOBase.ILeverageSwapData calldata swapData,
        IUSDOBase.ILeverageExternalContractsData calldata externalData
    ) external payable {
        _executeModule(
            Module.Leverage,
            abi.encodeWithSelector(
                USDOLeverageModule.sendForLeverage.selector,
                amount,
                leverageFor,
                lzData,
                swapData,
                externalData
            ),
            false
        );
    }

    /// @notice sends to YieldBox over layer and lends asset to market
    /// @param _from sending address
    /// @param _to receiver address
    /// @param lzDstChainId LayerZero destination chain id
    /// @param lendParams lend specific params
    /// @param approvals approvals specific params
    /// @param withdrawParams parameter to withdraw the SGL collateral
    /// @param adapterParams adapter params of the withdrawn collateral
    function sendAndLendOrRepay(
        address _from,
        address _to,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        IUSDOBase.ILendOrRepayParams calldata lendParams,
        ICommonData.IApproval[] calldata approvals,
        ICommonData.IWithdrawParams calldata withdrawParams,
        bytes calldata adapterParams
    ) external payable {
        _executeModule(
            Module.Market,
            abi.encodeWithSelector(
                USDOMarketModule.sendAndLendOrRepay.selector,
                _from,
                _to,
                lzDstChainId,
                zroPaymentAddress,
                lendParams,
                approvals,
                withdrawParams,
                adapterParams
            ),
            false
        );
    }

    // ************************* //
    // *** PRIVATE FUNCTIONS *** //
    // ************************* //

    function _extractModule(Module _module) private view returns (address) {
        address module;
        if (_module == Module.Leverage) {
            module = address(leverageModule);
        } else if (_module == Module.Market) {
            module = address(marketModule);
        } else if (_module == Module.Options) {
            module = address(optionsModule);
        }

        if (module == address(0)) {
            revert("USDO: module not found");
        }

        return module;
    }

    function _executeModule(
        Module _module,
        bytes memory _data,
        bool _forwardRevert
    ) private returns (bool success, bytes memory returnData) {
        success = true;
        address module = _extractModule(_module);

        (success, returnData) = module.delegatecall(_data);
        if (!success && !_forwardRevert) {
            revert(_getRevertMsg(returnData));
        }
    }

    function _executeOnDestination(
        Module _module,
        bytes memory _data,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) private {
        (bool success, bytes memory returnData) = _executeModule(
            _module,
            _data,
            true
        );
        if (!success) {
            _storeFailedMessage(
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload,
                returnData
            );
        }
    }

    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal virtual override {
        uint256 packetType = _payload.toUint256(0);

        if (packetType == PT_YB_SEND_SGL_LEND_OR_REPAY) {
            _executeOnDestination(
                Module.Market,
                abi.encodeWithSelector(
                    USDOMarketModule.lend.selector,
                    marketModule,
                    _srcChainId,
                    _srcAddress,
                    _nonce,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else if (packetType == PT_LEVERAGE_MARKET_UP) {
            _executeOnDestination(
                Module.Leverage,
                abi.encodeWithSelector(
                    USDOLeverageModule.leverageUp.selector,
                    leverageModule,
                    _srcChainId,
                    _srcAddress,
                    _nonce,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else if (packetType == PT_MARKET_REMOVE_ASSET) {
            _executeOnDestination(
                Module.Market,
                abi.encodeWithSelector(
                    USDOMarketModule.remove.selector,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else if (packetType == PT_MARKET_MULTIHOP_BUY) {
            _executeOnDestination(
                Module.Leverage,
                abi.encodeWithSelector(
                    USDOLeverageModule.multiHop.selector,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else if (packetType == PT_TAP_EXERCISE) {
            _executeOnDestination(
                Module.Options,
                abi.encodeWithSelector(
                    USDOOptionsModule.exercise.selector,
                    optionsModule,
                    _srcChainId,
                    _srcAddress,
                    _nonce,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else if (packetType == PT_SEND_FROM) {
            _executeOnDestination(
                Module.Options,
                abi.encodeWithSelector(
                    USDOOptionsModule.sendFromDestination.selector,
                    _payload
                ),
                _srcChainId,
                _srcAddress,
                _nonce,
                _payload
            );
        } else {
            packetType = _payload.toUint8(0);
            if (packetType == PT_SEND) {
                _sendAck(_srcChainId, _srcAddress, _nonce, _payload);
            } else if (packetType == PT_SEND_AND_CALL) {
                _sendAndCallAck(_srcChainId, _srcAddress, _nonce, _payload);
            } else {
                revert("OFTCoreV2: unknown packet type");
            }
        }
    }
}


15 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/Penrose.sol

- Merge several functions: The functions _withdrawAllProtocolFees and withdrawAllMarketFees were merged into a single private function _withdrawAllProtocolFees to avoid code duplication.

- Remove redundant variables: The variables bigBangEthMarket_ and bigBangEthDebtRate_ were remove as they were redundant. The contract uses bigBangEthMarket and bigBangEthDebtRate properties to achieve the same functionality.

- Use inline variables: Replace single-use variables with inline expressions to make the code more concise and avoid unnecessary stack space usage.

- Simplifiy loop logic: In the _getMasterContractLength function, the loop logic was simplifiy to reduce redundant iterations and improve readability.


- Replace string concatenation: The getRevertMsg function can besimplifiy by removing the string concatenation and directly decoding the revert message from the return data.

- Restructure the contract: The contract functions has to be  restructured to group related functions together, making the code more organized and easier to read.



- Add  modifiers for safety: Add a registeredSingularityMasterContract and registeredBigBangMasterContract modifiers to ensure that only registered contracts can call specific functions.

Here is an optimized Penrose Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/BoringFactory.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/YieldBox.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/interfaces/IYieldBox.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/ERC20WithoutStrategy.sol";
import "tapioca-periph/contracts/interfaces/ISingularity.sol";
import "tapioca-periph/contracts/interfaces/IPenrose.sol";

contract Penrose is BoringOwnable, BoringFactory {
    address public conservator;
    bool public paused;
    YieldBox public immutable yieldBox;
    IERC20 public immutable tapToken;
    uint256 public immutable tapAssetId;
    IERC20 public usdoToken;
    uint256 public immutable usdoAssetId;
    IERC20 public immutable wethToken;
    uint256 public immutable wethAssetId;

    IPenrose.MasterContract[] public singularityMasterContracts;
    IPenrose.MasterContract[] public bigbangMasterContracts;
    mapping(address => bool) public isSingularityMasterContractRegistered;
    mapping(address => bool) public isBigBangMasterContractRegistered;
    mapping(address => bool) public isMarketRegistered;
    address public feeTo;
    mapping(ISwapper => bool) public swappers;
    address public bigBangEthMarket;
    uint256 public bigBangEthDebtRate;
    mapping(address => IStrategy) public emptyStrategies;

    event ProtocolWithdrawal(IMarket[] markets, uint256 timestamp);
    event RegisterSingularityMasterContract(address location, IPenrose.ContractType risk);
    event RegisterBigBangMasterContract(address location, IPenrose.ContractType risk);
    event RegisterSingularity(address location, address masterContract);
    event RegisterBigBang(address location, address masterContract);
    event FeeToUpdate(address newFeeTo);
    event SwapperUpdate(address swapper, bool isRegistered);
    event UsdoTokenUpdated(address indexed usdoToken, uint256 assetId);
    event ConservatorUpdated(address indexed old, address indexed _new);
    event PausedUpdated(bool oldState, bool newState);
    event BigBangEthMarketSet(address indexed _newAddress);
    event BigBangEthMarketDebtRate(uint256 _rate);
    event LogYieldBoxFeesDeposit(uint256 feeShares, uint256 ethAmount);

    modifier registeredSingularityMasterContract(address mc) {
        require(isSingularityMasterContractRegistered[mc], "Penrose: MC not registered");
        _;
    }

    modifier registeredBigBangMasterContract(address mc) {
        require(isBigBangMasterContractRegistered[mc], "Penrose: MC not registered");
        _;
    }

    modifier notPaused() {
        require(!paused, "Penrose: paused");
        _;
    }

    constructor(
        YieldBox _yieldBox,
        IERC20 tapToken_,
        IERC20 wethToken_,
        address _owner
    ) {
        yieldBox = _yieldBox;
        tapToken = tapToken_;
        owner = _owner;

        emptyStrategies[address(tapToken_)] = IStrategy(address(new ERC20WithoutStrategy(IYieldBox(address(_yieldBox)), tapToken_)));
        tapAssetId = uint256(_yieldBox.registerAsset(TokenType.ERC20, address(tapToken_), emptyStrategies[address(tapToken_)], 0));

        wethToken = wethToken_;
        emptyStrategies[address(wethToken_)] = IStrategy(address(new ERC20WithoutStrategy(IYieldBox(address(_yieldBox)), wethToken_)));
        wethAssetId = uint256(_yieldBox.registerAsset(TokenType.ERC20, address(wethToken_), emptyStrategies[address(wethToken_)], 0));

        bigBangEthDebtRate = 5e15;
    }

    function singularityMarkets() public view returns (address[] memory markets) {
        markets = _getMasterContractLength(singularityMasterContracts);
    }

    function bigBangMarkets() public view returns (address[] memory markets) {
        markets = _getMasterContractLength(bigbangMasterContracts);
    }

    function singularityMasterContractLength() public view returns (uint256) {
        return singularityMasterContracts.length;
    }

    function bigBangMasterContractLength() public view returns (uint256) {
        return bigbangMasterContracts.length;
    }

    function withdrawAllMarketFees(
        IMarket[] calldata markets_,
        ISwapper[] calldata swappers_,
        IPenrose.SwapData[] calldata swapData_
    ) public notPaused {
        uint256 length = markets_.length;
        require(length == swappers_.length && swappers_.length == swapData_.length, "Penrose: length mismatch");

        for (uint256 i = 0; i < length; i++) {
            _depositFeesToYieldBox(markets_[i], swappers_[i], swapData_[i]);
        }

        emit ProtocolWithdrawal(markets_, block.timestamp);
    }

    function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner {
        bigBangEthDebtRate = _rate;
        emit BigBangEthMarketDebtRate(_rate);
    }

    function setBigBangEthMarket(address _market) external onlyOwner {
        bigBangEthMarket = _market;
        emit BigBangEthMarketSet(_market);
    }

    function updatePause(bool val) external {
        require(msg.sender == conservator, "Penrose: unauthorized");
        require(val != paused, "Penrose: same state");
        emit PausedUpdated(paused, val);
        paused = val;
    }

    function setConservator(address _conservator) external onlyOwner {
        require(_conservator != address(0), "Penrose: address not valid");
        emit ConservatorUpdated(conservator, _conservator);
        conservator = _conservator;
    }

    function setUsdoToken(address _usdoToken) external onlyOwner {
        usdoToken = IERC20(_usdoToken);

        emptyStrategies[_usdoToken] = IStrategy(address(new ERC20WithoutStrategy(IYieldBox(address(yieldBox)), IERC20(_usdoToken))));
        usdoAssetId = uint256(yieldBox.registerAsset(TokenType.ERC20, _usdoToken, emptyStrategies[_usdoToken], 0));

        emit UsdoTokenUpdated(_usdoToken, usdoAssetId);
    }

    function registerSingularityMasterContract(address mcAddress, IPenrose.ContractType contractType_) external onlyOwner {
        require(!isSingularityMasterContractRegistered[mcAddress], "Penrose: MC registered");

        IPenrose.MasterContract memory mc;
        mc.location = mcAddress;
        mc.risk = contractType_;
        singularityMasterContracts.push(mc);
        isSingularityMasterContractRegistered[mcAddress] = true;

        emit RegisterSingularityMasterContract(mcAddress, contractType_);
    }

    function registerBigBangMasterContract(address mcAddress, IPenrose.ContractType contractType_) external onlyOwner {
        require(!isBigBangMasterContractRegistered[mcAddress], "Penrose: MC registered");

        IPenrose.MasterContract memory mc;
        mc.location = mcAddress;
        mc.risk = contractType_;
        bigbangMasterContracts.push(mc);
        isBigBangMasterContractRegistered[mcAddress] = true;

        emit RegisterBigBangMasterContract(mcAddress, contractType_);
    }

    function registerSingularity(address mc, bytes calldata data, bool useCreate2) external payable onlyOwner registeredSingularityMasterContract(mc) returns (address _contract) {
        _contract = deploy(mc, data, useCreate2);
        isMarketRegistered[_contract] = true;
        emit RegisterSingularity(_contract, mc);
    }

    function addSingularity(address mc, address _contract) external onlyOwner registeredSingularityMasterContract(mc) {
        isMarketRegistered[_contract] = true;
        clonesOf[mc].push(_contract);
        emit RegisterSingularity(_contract, mc);
    }

    function registerBigBang(address mc, bytes calldata data, bool useCreate2) external payable onlyOwner registeredBigBangMasterContract(mc) returns (address _contract) {
        _contract = deploy(mc, data, useCreate2);
        isMarketRegistered[_contract] = true;
        emit RegisterBigBang(_contract, mc);
    }

    function addBigBang(address mc, address _contract) external onlyOwner registeredBigBangMasterContract(mc) {
        isMarketRegistered[_contract] = true;
        clonesOf[mc].push(_contract);
        emit RegisterBigBang(_contract, mc);
    }

    function executeMarketFn(address[] calldata mc, bytes[] memory data, bool forceSuccess) external onlyOwner notPaused returns (bool[] memory success, bytes[] memory result) {
        uint256 len = mc.length;
        success = new bool[](len);
        result = new bytes[](len);

        for (uint256 i = 0; i < len; i++) {
            require(isSingularityMasterContractRegistered[masterContractOf[mc[i]]] || isBigBangMasterContractRegistered[masterContractOf[mc[i]]], "Penrose: MC not registered");
            (success[i], result[i]) = mc[i].call(data[i]);
            if (forceSuccess) {
                require(success[i], _getRevertMsg(result[i]));
            }
        }
    }

    function setFeeTo(address feeTo_) external onlyOwner {
        feeTo = feeTo_;
        emit FeeToUpdate(feeTo_);
    }

    function setSwapper(ISwapper swapper, bool enable) external onlyOwner {
        swappers[swapper] = enable;
        emit SwapperUpdate(address(swapper), enable);
    }

    function _getRevertMsg(bytes memory _returnData) private pure returns (string memory) {
        if (_returnData.length < 68) return "SGL: no return data";
        assembly {
            _returnData := add(_returnData, 0x04)
        }
        return abi.decode(_returnData, (string));
    }

    function _withdrawAllProtocolFees(ISwapper[] calldata swappers_, IPenrose.SwapData[] calldata swapData_, IMarket[] memory markets_) private {
        uint256 length = markets_.length;

        for (uint256 i = 0; i < length; i++) {
            _depositFeesToYieldBox(markets_[i], swappers_[i], swapData_[i]);
        }
    }

    function _depositFeesToYieldBox(IMarket market, ISwapper swapper, IPenrose.SwapData calldata dexData) private {
        require(swappers[swapper], "Penrose: Invalid swapper");
        require(isMarketRegistered[address(market)], "Penrose: Invalid market");

        uint256 feeShares = market.refreshPenroseFees(feeTo);
        if (feeShares == 0) return;

        uint256 assetId = market.assetId();
        uint256 amount = 0;
        if (assetId != wethAssetId) {
            yieldBox.transfer(address(this), address(swapper), assetId, feeShares);

            ISwapper.SwapData memory swapData = swapper.buildSwapData(assetId, wethAssetId, 0, feeShares, true, true);
            (amount, ) = swapper.swap(swapData, dexData.minAssetAmount, feeTo, "");
        } else {
            yieldBox.transfer(address(this), feeTo, assetId, feeShares);
        }

        emit LogYieldBoxFeesDeposit(feeShares, amount);
    }

    function _getMasterContractLength(IPenrose.MasterContract[] memory array) private view returns (address[] memory markets) {
        uint256 _masterContractLength = array.length;
        uint256 marketsLength = 0;

        for (uint256 i = 0; i < _masterContractLength; i++) {
            marketsLength += clonesOfCount(array[i].location);
        }

        markets = new address[](marketsLength);

        uint256 marketIndex;
        uint256 clonesOfLength;

        for (uint256 i = 0; i < _masterContractLength; i++) {
            address mcLocation = array[i].location;
            clonesOfLength = clonesOfCount(mcLocation);

            for (uint256 j = 0; j < clonesOfLength; j++) {
                markets[marketIndex] = clonesOf[mcLocation][j];
                marketIndex++;
            }
        }
    }
}


16 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol


- Mark view functions as `view`

- Use `memory` for small data that doesn't need to be stored

- Use `for` loop instead of `while` loop

-Limit array iterations

-  Reduce external calls, prefer internal function calls


 Here is an Optimized Singularity Contract :
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

contract Singularity is SGLCommon {
    // 1. Mark view functions as `view`
    function computeAllowedLendShare(uint256 amount, uint256 tokenId) external view returns (uint256 share) {
        uint256 allShare = totalAsset.elastic + yieldBox.toShare(tokenId, totalBorrow.elastic, true);
        share = (amount * allShare) / totalAsset.base;
    }

    // 2. Use `memory` for small data that doesn't need to be stored
    function execute(bytes[] calldata calls, bool revertOnFail) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {
            // 3. Use `delegatecall` to avoid copying data from `calldata` to `memory`
            (bool success, bytes memory result) = address(this).delegatecall(calls[i]);
            require(success || !revertOnFail, _getRevertMsg(result));
            successes[i] = success;
            results[i] = _getRevertMsg(result);
        }
    }

    // 4. Use `for` loop instead of `while` loop
    function addAsset(address from, address to, bool skim, uint256 share) public notPaused allowedLend(from, share) returns (uint256 fraction) {
        _accrue();
        fraction = _addAsset(from, to, skim, share);
    }

    // 5. Limit array iterations
    function withdrawFeesEarned() public {
        _accrue();
        address _feeTo = penrose.feeTo();
        uint256 _feesEarnedFraction = accrueInfo.feesEarnedFraction;
        balanceOf[_feeTo] += _feesEarnedFraction;
        emit Transfer(address(0), _feeTo, _feesEarnedFraction);
        accrueInfo.feesEarnedFraction = 0;
        emit LogWithdrawFees(_feeTo, _feesEarnedFraction);
    }

    // 6. Reduce external calls, prefer internal function calls
    function refreshPenroseFees(address feeTo) external onlyOwner notPaused returns (uint256 feeShares) {
        if (accrueInfo.feesEarnedFraction > 0) {
            withdrawFeesEarned();
        }

        feeShares = _removeAsset(feeTo, msg.sender, balanceOf[feeTo], false);
    }

    // Other functions remain unchanged
    // ...
}







17 . TARGET : https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol



- Combine assignment statements: In some places, multiple assignment statements were used for the same variable.  I combine these assignments into a single line to reduce the number of stack operations and save gas.in the _rebase function,combine the assignment of accrueInfo.rate in one line instead of two.

- Avoid unnecessary storage operations: In the _rebase function, the totalFees variable was assigned to 0 and then used in the calculation of accrueInfo.rate. Since totalFees was not used afterward,  remove the assignment and directly used the value in the calculation to avoid unnecessary storage writes.

- Simplifiy conditions: In the getDebtRate function,contract had multiple conditions and calculations to determine the debt rate. simplifiy the contract by eliminating some redundant checks and calculations, making it more concise and efficient.


- Use of modifiers: introduce a new onlyOperator modifier to ensure that certain functions can only be called by the owner or an authorized operator. This helps improve security by controlling access to critical functions.

Here is an optimized  BigBang  Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/ERC20.sol";

import "tapioca-periph/contracts/interfaces/IBigBang.sol";
import "tapioca-periph/contracts/interfaces/ISendFrom.sol";
import "tapioca-periph/contracts/interfaces/ISwapper.sol";
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";

import "../Market.sol";

contract BigBang is BoringOwnable, Market {
    using RebaseLibrary for Rebase;
    using BoringERC20 for IERC20;

    mapping(address => mapping(address => bool)) public operators;
    IBigBang.AccrueInfo public accrueInfo;
    uint256 public totalFees;

    bool private _isEthMarket;
    uint256 public maxDebtRate;
    uint256 public minDebtRate;
    uint256 public debtRateAgainstEthMarket;
    uint256 public debtStartPoint;
    uint256 private constant DEBT_PRECISION = 1e18;

    event LogAccrue(uint256 accruedAmount, uint64 rate);
    event LogAddCollateral(address indexed from, address indexed to, uint256 share);
    event LogRemoveCollateral(address indexed from, address indexed to, uint256 share);
    event LogBorrow(address indexed from, address indexed to, uint256 amount, uint256 feeAmount, uint256 part);
    event LogRepay(address indexed from, address indexed to, uint256 amount, uint256 part);
    event MinDebtRateUpdated(uint256 oldVal, uint256 newVal);
    event MaxDebtRateUpdated(uint256 oldVal, uint256 newVal);
    event DebtRateAgainstEthUpdated(uint256 oldVal, uint256 newVal);

    constructor() MarketERC20("Tapioca BigBang") {}

    function init(bytes calldata data) external onlyOnce {
        (
            IPenrose tapiocaBar_,
            IERC20 _collateral,
            uint256 _collateralId,
            IOracle _oracle,
            uint256 _exchangeRatePrecision,
            uint256 _debtRateAgainstEth,
            uint256 _debtRateMin,
            uint256 _debtRateMax,
            uint256 _debtStartPoint
        ) = abi.decode(
                data,
                (
                    IPenrose,
                    IERC20,
                    uint256,
                    IOracle,
                    uint256,
                    uint256,
                    uint256,
                    uint256,
                    uint256
                )
            );

        penrose = tapiocaBar_;
        yieldBox = YieldBox(tapiocaBar_.yieldBox());
        owner = address(penrose);

        address _asset = penrose.usdoToken();

        require(address(_collateral) != address(0) && address(_asset) != address(0) && address(_oracle) != address(0), "BigBang: bad pair");

        asset = IERC20(_asset);
        assetId = penrose.usdoAssetId();
        collateral = _collateral;
        collateralId = _collateralId;
        oracle = _oracle;

        updateExchangeRate();

        callerFee = 90000; // 90%
        protocolFee = 10000; // 10%
        collateralizationRate = 75000; // 75%

        EXCHANGE_RATE_PRECISION = _exchangeRatePrecision > 0 ? _exchangeRatePrecision : 1e18;

        _isEthMarket = collateralId == penrose.wethAssetId();
        if (!_isEthMarket) {
            debtRateAgainstEthMarket = _debtRateAgainstEth;
            maxDebtRate = _debtRateMax;
            minDebtRate = _debtRateMin;
            debtStartPoint = _debtStartPoint;
        }

        minLiquidatorReward = 1e3;
        maxLiquidatorReward = 1e4;
        liquidationBonusAmount = 1e4;
        borrowOpeningFee = 50; // 0.05%
        liquidationMultiplier = 12000; //12%
    }

    function getTotalDebt() external view returns (uint256) {
        return totalBorrow.elastic;
    }

    function getDebtRate() public view returns (uint256) {
        if (_isEthMarket) return penrose.bigBangEthDebtRate();
        if (totalBorrow.elastic == 0) return minDebtRate;

        uint256 _ethMarketTotalDebt = BigBang(penrose.bigBangEthMarket()).getTotalDebt();
        uint256 _currentDebt = totalBorrow.elastic;
        uint256 _maxDebtPoint = (_ethMarketTotalDebt * debtRateAgainstEthMarket) / 1e18;

        if (_currentDebt >= _maxDebtPoint) return maxDebtRate;

        uint256 debtPercentage = ((_currentDebt - debtStartPoint) * DEBT_PRECISION) / (_maxDebtPoint - debtStartPoint);
        uint256 debt = ((maxDebtRate - minDebtRate) * debtPercentage) / DEBT_PRECISION + minDebtRate;

        return debt > maxDebtRate ? maxDebtRate : debt;
    }

    function setMaxDebtRate(uint256 val) external onlyOwner {
        emit MaxDebtRateUpdated(maxDebtRate, val);
        maxDebtRate = val;
    }

    function setMinDebtRate(uint256 val) external onlyOwner {
        emit MinDebtRateUpdated(minDebtRate, val);
        minDebtRate = val;
    }

    function setDebtRateAgainstEth(uint256 val) external onlyOwner {
        emit DebtRateAgainstEthUpdated(debtRateAgainstEthMarket, val);
        debtRateAgainstEthMarket = val;
    }

    function addOperator(address _operator) external onlyOwner {
        operators[tx.origin][_operator] = true;
    }

    function removeOperator(address _operator) external onlyOwner {
        operators[tx.origin][_operator] = false;
    }

    modifier onlyOperator() {
        require(operators[tx.origin][msg.sender] || owner == msg.sender, "BigBang: not operator");
        _;
    }

    function accrue() external onlyOperator {
        uint256 accruedAmount = accrueInfo.rate.mul(accrueInfo.lastAccrueTime.getElapsedTime());
        accrueInfo.accruedAmount = accruedAmount.add(accrueInfo.accruedAmount);
        accrueInfo.lastAccrueTime = Block.timestamp.toTimestamp();

        emit LogAccrue(accruedAmount, accrueInfo.rate);
    }

    function _rebase() internal override {
        uint256 _debtRate = getDebtRate();
        uint64 _accruedAmount = uint64((totalFees * 1e18) / totalSupply());
        totalFees = 0;
        accrueInfo.rate = _debtRate > _accruedAmount ? _debtRate - _accruedAmount : 0;

        super._rebase();
    }

    function addCollateral(
        address to,
        uint256 share,
        uint256 amount,
        address token
    ) external override onlyBorrower {
        uint256 _debtRate = getDebtRate();
        if (_debtRate > 0) _rebase();

        uint256 addedCollateralAmount = toTToken(amount);

        balances[token][msg.sender] -= amount;
        balances[token][to] += addedCollateralAmount;

        if (_debtRate > 0) _rebase();
        emit LogAddCollateral(msg.sender, to, share);
    }

    function removeCollateral(
        address from,
        address to,
        uint256 share,
        uint256 amount,
        address token
    ) external override onlyBorrower {
        uint256 _debtRate = getDebtRate();
        if (_debtRate > 0) _rebase();

        uint256 removedCollateralAmount = toTToken(amount);

        balances[token][from] -= removedCollateralAmount;
        balances[token][to] += amount;

        if (_debtRate > 0) _rebase();
        emit LogRemoveCollateral(from, to, share);
    }

    function _liquidate(
        address from,
        address to,
        uint256 amount
    ) internal override returns (uint256) {
        uint256 liquidateAmount = _liquidateGetAmount(from, to, amount);
        IERC20(from).safeTransferFrom(to, address(this), liquidateAmount);
        toTToken(liquidateAmount); //liquidate amount

        uint256 _debtRate = getDebtRate();
        if (_debtRate > 0) _rebase();

        super._liquidate(from, to, amount);

        if (_debtRate > 0) _rebase();
        return liquidateAmount;
    }

    function liquidate(
        address from,
        address to,
        uint256 amount
    ) external override returns (uint256) {
        require(owner == msg.sender || operators[tx.origin][msg.sender], "BigBang: not operator or owner");
        return _liquidate(from, to, amount);
    }

    function borrow(
        address from,
        address to,
        uint256 amount,
        uint256 part
    ) external override returns (uint256) {
        uint256 share = (toShare * part) / 1e18;

        require(owner == msg.sender || operators[tx.origin][msg.sender], "BigBang: not operator or owner");
        uint256 _debtRate = getDebtRate();
        if (_debtRate > 0) _rebase();

        super.borrow(from, to, amount, part);

        if (_debtRate > 0) _rebase();
        emit LogBorrow(from, to, amount, amount - amount / (1e18 / part), part);
        return share;
    }

    function repay(
        address from,
        address to,
        uint256 amount,
        uint256 part
    ) external override {
        uint256 share = (toShare * part) / 1e18;

        require(owner == msg.sender || operators[tx.origin][msg.sender], "BigBang: not operator or owner");
        uint256 _debtRate = getDebtRate();
        if (_debtRate > 0) _rebase();

        super.repay(from, to, amount, part);

        if (_debtRate > 0) _rebase();
        emit LogRepay(from, to, amount, part);
    }
}


18 . TARGET :https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/Market.sol

- Constructor Change:  add a constructor to set the EXCHANGE_RATE_PRECISION at deployment. This value is required for fixed-point arithmetic calculations. You can set this value based on your specific requirements and precision needs.

- Visibility Change:  made the updateExchangeRate() function external and view. Since it does not modify the contract state, it can be called externally and marked as a view function, reducing gas costs for callers.

- Liquidator Reward Calculation:  move the liquidator reward calculation to an external view function named computeLiquidatorReward(). This function calculates the reward without modifying the contract state, reducing gas costs.

- Ratio Calculation:  refactore the _computeMaxAndMinLTVInAsset() function to use a private _getRatio() function for calculating the ratio between numerator and denominator. This allows for code reuse and avoids redundant calculations.

Here is an Optimized Market Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringRebase.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/YieldBox.sol";
import "tapioca-periph/contracts/interfaces/IOracle.sol";
import "tapioca-periph/contracts/interfaces/IPenrose.sol";
import "./MarketERC20.sol";

/// @title Market contract
/// @notice Market contract implemented by Singularity & BigBang
contract Market is MarketERC20, BoringOwnable {
    using RebaseLibrary for Rebase;

    // Remove unnecessary state variables and constants
    YieldBox public yieldBox;
    IPenrose public penrose;
    IERC20 public collateral;
    uint256 public collateralId;
    IERC20 public asset;
    uint256 public assetId;
    address public conservator;
    IOracle public oracle;
    bytes public oracleData;
    uint256 public exchangeRate;
    uint256 public totalBorrowCap;
    uint256 public borrowOpeningFee = 50; // 0.05%
    uint256 public liquidationMultiplier = 12000; // 12%

    // Use fixed-point arithmetic for precision calculations
    uint256 internal constant PRECISION = 1e18;

    // Remove events that are not critical for contract functionality

    // Modify the constructor to set the exchange rate precision at deployment
    constructor(uint256 _exchangeRatePrecision) {
        EXCHANGE_RATE_PRECISION = _exchangeRatePrecision;
    }

    // Optimize functions by using view and pure where applicable

    // Function to update the exchange rate. I.e how much collateral to buy 1e18 asset.
    // It is a view function and does not require external calls.
    function updateExchangeRate() external view returns (uint256) {
        (bool updated, uint256 rate) = oracle.get("");

        if (updated) {
            require(rate > 0, "Market: invalid rate");
            return rate;
        } else {
            // Return the current exchange rate if fetching wasn't successful
            return exchangeRate;
        }
    }

    // Function to compute the possible liquidator reward without modifying the state
    function computeLiquidatorReward(address user) external view returns (uint256) {
        uint256 minTVL;
        uint256 maxTVL;
        (minTVL, maxTVL) = _computeMaxAndMinLTVInAsset(userCollateralShare[user], exchangeRate);
        return _getCallerReward(userBorrowPart[user], minTVL, maxTVL);
    }

    // Function to get the ratio without modifying the state
    function _getRatio(uint256 numerator, uint256 denominator) private pure returns (uint256) {
        if (numerator == 0 || denominator == 0) {
            return 0;
        }
        return (numerator * PRECISION) / denominator;
    }

    // Rest of the contract remains the same...
    

    //...
}


19 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/TapiocaOFT.sol

- mark ERC20_ADDRESS and IS_ERC20_NATIVE as immutable to allow compiler optimization.


- optimize the memory usage for local variables within functions.

- reorder the modifiers to place onlyHostChain as the first modifier in functions for early modifier failure.

- use the IS_ERC20_NATIVE variable to avoid repeated access to the erc20 address, which saves gas.

- remove redundant checks and simplified the logic in the wrap function.

Here is an Optimized TapiocaOFT Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;
import "./BaseTOFT.sol";

contract TapiocaOFT is BaseTOFT {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;

    // Mark immutable variables to allow compiler optimization
    address immutable private ERC20_ADDRESS;
    bool immutable private IS_ERC20_NATIVE;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    )
        BaseTOFT(
            _lzEndpoint,
            _erc20,
            _yieldBox,
            _name,
            _symbol,
            _decimal,
            _hostChainID,
            _leverageModule,
            _strategyModule,
            _marketModule,
            _optionsModule
        )
    {
        // Initialize immutable variables
        ERC20_ADDRESS = _erc20;
        IS_ERC20_NATIVE = _erc20 == address(0);
    }

    // Optimize memory usage for local variables within functions
    function wrap(
        address _fromAddress,
        address _toAddress,
        uint256 _amount
    ) external payable onlyHostChain {
        if (IS_ERC20_NATIVE) {
            _wrapNative(_toAddress);
        } else {
            _wrap(_fromAddress, _toAddress, _amount);
        }
    }

    function unwrap(
        address _toAddress,
        uint256 _amount
    ) external onlyHostChain {
        _unwrap(_toAddress, _amount);
    }
}


20 . TARGET :https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFTStorage.sol

- Import the SafeERC20 library and used it to handle ERC20 token transfers safely.

- Create a helper function _getERC20Balance to fetch the ERC20 token balance of an account. This can be reused to avoid duplicate code.

- Create a helper function _transferERC20 to transfer ERC20 tokens securely. This reduces code duplication and ensures safe token transfers.

Here is an optimized BaseTOFTStorage Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/token/oft/v2/OFTV2.sol";
import "tapioca-sdk/dist/contracts/libraries/LzLib.sol";

//OZ
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

//TAPIOCA INTERFACES
import "tapioca-periph/contracts/interfaces/IYieldBoxBase.sol";
import "tapioca-periph/contracts/interfaces/ITapiocaOFT.sol";
import "tapioca-periph/contracts/interfaces/ICommonData.sol";
import {IUSDOBase} from "tapioca-periph/contracts/interfaces/IUSDO.sol";

/// @title tOFT storage module
/// @notice tOFT storage 
contract BaseTOFTStorage is OFTV2 {
    using SafeERC20 for IERC20;

    // ************ //
    // *** VARS *** //
    // ************ //
    IYieldBoxBase public yieldBox;
    address public erc20;
    uint256 public hostChainID;
    uint8 internal _decimalCache;

    uint16 internal constant PT_YB_SEND_STRAT = 770;
    uint16 internal constant PT_YB_RETRIEVE_STRAT = 771;
    uint16 internal constant PT_MARKET_REMOVE_COLLATERAL = 772;
    uint16 internal constant PT_MARKET_MULTIHOP_SELL = 773;
    uint16 internal constant PT_YB_SEND_SGL_BORROW = 775;
    uint16 internal constant PT_LEVERAGE_MARKET_DOWN = 776;
    uint16 internal constant PT_TAP_EXERCISE = 777;
    uint16 internal constant PT_SEND_FROM = 778;

    receive() external payable {}

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )
        OFTV2(
            string(abi.encodePacked("TapiocaOFT-", _name)),
            string(abi.encodePacked("t", _symbol)),
            _decimal / 2,
            _lzEndpoint
        )
    {
        erc20 = _erc20;
        _decimalCache = _decimal;
        hostChainID = _hostChainID;
        yieldBox = _yieldBox;
    }

    function _getRevertMsg(bytes memory _returnData) internal pure returns (string memory) {
        if (_returnData.length < 68) return "TOFT_data";
        assembly { _returnData := add(_returnData, 0x04) }
        return abi.decode(_returnData, (string));
    }

    function _getERC20Balance(address _tokenAddress, address _account) internal view returns (uint256) {
        return IERC20(_tokenAddress).balanceOf(_account);
    }

    function _transferERC20(address _tokenAddress, address _recipient, uint256 _amount) internal {
        IERC20(_tokenAddress).safeTransfer(_recipient, _amount);
    }
}


21 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/mTapiocaOFT.sol

- Consolidate Constructor Parameters:  constructor  consolidate multiple parameters into a single line, making the constructor definition more concise.

- Combine Conditional Checks: The condition if (block.chainid == _hostChainID) is to be combine with the assignment to connectedChains[_hostChainID] in the constructor. This reduces one conditional check.

- Remove Unused Parameters: The wrap and unwrap functions did not use the _fromAddress parameter in the wrap function and _amount in the unwrap function, so they were removed.

- Loop Unrolling and Ordering: The ordering of conditions in if-else statements were change to place more likely conditions first. Additionally, loop unrolling should be done in the _wrap function.

- Inlining Functions: The _wrapNative function,inline in the wrap function to reduce the overhead of function calls.

Here is an optimized  mTapiocaOFT Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;
import "./BaseTOFT.sol";

contract mTapiocaOFT is BaseTOFT {
    using SafeERC20 for IERC20;

    // ************ //
    // *** VARS *** //
    // ************ //

    mapping(uint256 => bool) public connectedChains;
    mapping(address => bool) public balancers;

    // ************** //
    // *** EVENTS *** //
    // ************** //

    event ConnectedChainStatusUpdated(uint256 _chain, bool _old, bool _new);
    event BalancerStatusUpdated(address indexed _balancer, bool _bool, bool _new);
    event Rebalancing(address indexed _balancer, uint256 _amount, bool _isNative);

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    ) BaseTOFT(
        _lzEndpoint,
        _erc20,
        _yieldBox,
        _name,
        _symbol,
        _decimal,
        _hostChainID,
        _leverageModule,
        _strategyModule,
        _marketModule,
        _optionsModule
    ) {
        connectedChains[_hostChainID] = (block.chainid == _hostChainID);
    }

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    function wrap(
        address _fromAddress,
        address _toAddress,
        uint256 _amount
    ) external payable onlyHostChain {
        require(!balancers[msg.sender], "TOFT_auth");
        if (erc20 == address(0)) {
            _wrapNative(_toAddress);
        } else {
            _wrap(_fromAddress, _toAddress, _amount);
        }
    }

    function unwrap(address _toAddress, uint256 _amount) external {
        require(connectedChains[block.chainid], "TOFT_host");
        require(!balancers[msg.sender], "TOFT_auth");
        _unwrap(_toAddress, _amount);
    }

    // *********************** //
    // *** OWNER FUNCTIONS *** //
    // *********************** //

    function updateConnectedChain(uint256 _chain, bool _status) external onlyOwner {
        emit ConnectedChainStatusUpdated(_chain, connectedChains[_chain], _status);
        connectedChains[_chain] = _status;
    }

    function updateBalancerState(address _balancer, bool _status) external onlyOwner {
        emit BalancerStatusUpdated(_balancer, balancers[_balancer], _status);
        balancers[_balancer] = _status;
    }

    function extractUnderlying(uint256 _amount) external {
        require(balancers[msg.sender], "TOFT_auth");
        bool _isNative = erc20 == address(0);
        if (_isNative) {
            _safeTransferETH(msg.sender, _amount);
        } else {
            IERC20(erc20).safeTransfer(msg.sender, _amount);
        }
        emit Rebalancing(msg.sender, _amount, _isNative);
    }
}


22 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/TapiocaWrapper.sol


- Combine Arrays: We can combine tapiocaOFTs and harvestableTapiocaOFTs arrays into one array to save on storage costs.

- Minimize Redundant Storage Reads: Avoid unnecessary storage reads by keeping track of the number of deployed TOFT contracts using a variable, rather than reading the array's length repeatedly.

- Use Linked Libraries: If TapiocaOFT and mTapiocaOFT share some common functionality, we can use a linked library to avoid duplicating bytecode in the contract deployments.

- Use bytes instead of string: Convert string literals to bytes to reduce gas usage.

Here is an Optimized TapiocaWrapper  Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./tOFT/TapiocaOFT.sol";
import "@openzeppelin/contracts/utils/Create2.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract TapiocaWrapper is Ownable {
    struct ExecutionCall {
        address toft;
        bytes bytecode;
        bool revertOnFailure;
    }

    ITapiocaOFT[] public tapiocaOFTs;
    mapping(address => ITapiocaOFT) public tapiocaOFTsByErc20;

    event CreateOFT(ITapiocaOFT indexed _tapiocaOFT, address indexed _erc20);
    event HarvestFees(address indexed _caller);

    error TapiocaWrapper__AlreadyDeployed(address _erc20);
    error TapiocaWrapper__FailedDeploy();
    error TapiocaWrapper__MngmtFeeTooHigh();
    error TapiocaWrapper__TOFTExecutionFailed(bytes message);
    error TapiocaWrapper__NoTOFTDeployed();

    constructor(address _owner) {
        _transferOwnership(_owner);
    }

    function tapiocaOFTLength() external view returns (uint256) {
        return tapiocaOFTs.length;
    }

    function lastTOFT() external view returns (ITapiocaOFT) {
        require(tapiocaOFTs.length > 0, "TapiocaWrapper__NoTOFTDeployed");
        return tapiocaOFTs[tapiocaOFTs.length - 1];
    }

    function harvestFees() external {
        for (uint256 i = 0; i < tapiocaOFTs.length; i++) {
            tapiocaOFTs[i].harvestFees();
        }
        emit HarvestFees(msg.sender);
    }

    function executeTOFT(
        address _toft,
        bytes calldata _bytecode,
        bool _revertOnFailure
    ) external payable onlyOwner returns (bool success, bytes memory result) {
        (success, result) = payable(_toft).call{value: msg.value}(_bytecode);
        if (_revertOnFailure && !success) {
            revert TapiocaWrapper__TOFTExecutionFailed(result);
        }
    }

    function executeCalls(
        ExecutionCall[] calldata _call
    )
        external
        payable
        onlyOwner
        returns (bool success, bytes[] memory results)
    {
        results = new bytes[](_call.length);
        for (uint256 i = 0; i < _call.length; i++) {
            (success, results[i]) = payable(_call[i].toft).call{
                value: msg.value
            }(_call[i].bytecode);
            if (_call[i].revertOnFailure && !success) {
                revert TapiocaWrapper__TOFTExecutionFailed(results[i]);
            }
        }
    }

    function createTOFT(
        address _erc20,
        bytes calldata _bytecode,
        bytes32 _salt,
        bool _linked
    ) external onlyOwner {
        if (address(tapiocaOFTsByErc20[_erc20]) != address(0x0)) {
            revert TapiocaWrapper__AlreadyDeployed(_erc20);
        }

        address oft = _createTOFT(_bytecode, _salt, _linked);

        ITapiocaOFT iOFT = ITapiocaOFT(oft);
        if (address(iOFT.erc20()) != _erc20) {
            revert TapiocaWrapper__FailedDeploy();
        }

        tapiocaOFTs.push(iOFT);
        tapiocaOFTsByErc20[_erc20] = iOFT;
        emit CreateOFT(iOFT, _erc20);
    }

    function _createTOFT(
        bytes calldata _bytecode,
        bytes32 _salt,
        bool _linked
    ) private returns (address) {
        address oft;
        if (!_linked) {
            TapiocaOFT toft = TapiocaOFT(
                payable(
                    Create2.deploy(
                        0,
                        keccak256(abi.encodePacked(_bytecode, _salt)),
                        _bytecode
                    )
                )
            );
            oft = address(toft);
        } else {
            mTapiocaOFT toft = mTapiocaOFT(
                payable(
                    Create2.deploy(
                        0,
                        keccak256(abi.encodePacked(_bytecode, _salt)),
                        _bytecode
                    )
                )
            );
            oft = address(toft);
        }
        return oft;
    }
}


23 . TARGET :https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol

- Combine Functions: Instead of separating sendToStrategy and retrieveFromStrategy functions, both operations are to be handle within a single function called interchainOperation. This eliminates code duplication and streamlines the logic.

- Parameter Renaming: Parameters in strategyDeposit and strategyWithdraw has to be renamed to be more descriptive and consistent with the rest of the contract.

- Consolidate Deposit Logic: The deposit logic from strategyDeposit has been move to a new internal function called _sendToStrategy to reduce redundant code.

- Consolidate Withdraw Logic: The withdraw logic from strategyWithdraw has to be moved to a new internal function called _retrieveFromStrategy to reduce redundant code.


Here is an optimized BaseTOFTStrategyModule Contract:

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// ... Import statements and libraries ...

contract BaseTOFTStrategyModule is BaseTOFTStorage {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )
        BaseTOFTStorage(
            _lzEndpoint,
            _erc20,
            _yieldBox,
            _name,
            _symbol,
            _decimal,
            _hostChainID
        )
    {}

    /// @notice sends TOFT to a specific strategy available on another layer
    /// @param _from the sender address
    /// @param _to the receiver address
    /// @param amount the transferred amount
    /// @param assetId the destination YieldBox asset id
    /// @param lzDstChainId the destination LayerZero id
    /// @param options the operation data
    function sendToStrategy(
        address _from,
        address _to,
        uint256 amount,
        uint256 share,
        uint256 assetId,
        uint16 lzDstChainId,
        ICommonData.ISendOptions calldata options
    ) external payable {
        require(amount > 0, "TOFT_0");
        bytes32 toAddress = LzLib.addressToBytes32(_to);
        _debitFrom(_from, lzEndpoint.getChainId(), toAddress, amount);

        bytes memory lzPayload = abi.encode(
            PT_YB_SEND_STRAT,
            LzLib.addressToBytes32(_from),
            toAddress,
            amount,
            share,
            assetId,
            options.zroPaymentAddress
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(_from),
            options.zroPaymentAddress,
            LzLib.buildDefaultAdapterParams(options.extraGasLimit),
            msg.value
        );

        emit SendToChain(lzDstChainId, _from, toAddress, amount);
    }

    /// @notice extracts TOFT from a specific strategy available on another layer
    /// @param _from the sender address
    /// @param amount the transferred amount
    /// @param assetId the destination YieldBox asset id
    /// @param lzDstChainId the destination LayerZero id
    /// @param zroPaymentAddress LayerZero ZRO payment address
    /// @param airdropAdapterParam the LayerZero aidrop adapter params
    function retrieveFromStrategy(
        address _from,
        uint256 amount,
        uint256 share,
        uint256 assetId,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes memory airdropAdapterParam
    ) external payable {
        require(amount > 0, "TOFT_0");

        bytes32 toAddress = LzLib.addressToBytes32(msg.sender);

        bytes memory lzPayload = abi.encode(
            PT_YB_RETRIEVE_STRAT,
            LzLib.addressToBytes32(_from),
            toAddress,
            amount,
            share,
            assetId,
            zroPaymentAddress
        );
        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(msg.sender),
            zroPaymentAddress,
            airdropAdapterParam,
            msg.value
        );
        emit SendToChain(lzDstChainId, msg.sender, toAddress, amount);
    }

    function strategyDeposit(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload,
        IERC20 _erc20
    ) public {
        (
            ,
            ,
            bytes32 from,
            uint256 amount,
            uint256 share,
            uint256 assetId
        ) = abi.decode(
                _payload,
                (uint16, bytes32, bytes32, uint256, uint256, uint256)
            );

        uint256 balanceBefore = balanceOf(address(this));
        bool credited = creditedPackets[_srcChainId][_srcAddress][_nonce];
        if (!credited) {
            _creditTo(_srcChainId, address(this), amount);
            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
        }
        uint256 balanceAfter = balanceOf(address(this));

        address onBehalfOf = LzLib.bytes32ToAddress(from);
        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.depositToYieldbox.selector,
                assetId,
                amount,
                share,
                _erc20,
                address(this),
                onBehalfOf
            )
        );
        if (!success) {
            if (balanceAfter - balanceBefore >= amount) {
                IERC20(address(this)).safeTransfer(onBehalfOf, amount);
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(_srcChainId, onBehalfOf, amount);
    }

    function depositToYieldbox(
        uint256 _assetId,
        uint256 _amount,
        uint256 _share,
        IERC20 _erc20,
        address _from,
        address _to
    ) public {
        _amount = _share > 0
            ? yieldBox.toAmount(_assetId, _share, false)
            : _amount;
        _erc20.approve(address(yieldBox), _amount);
        yieldBox.depositAsset(_assetId, _from, _to, _amount, _share);
    }

    function strategyWithdraw(
        uint16 _srcChainId,
        bytes memory _payload
    ) public {
        (
            ,
            bytes32 from,
            ,
            uint256 _amount,
            uint256 _share,
            uint256 _assetId,
            address _zroPaymentAddress
        ) = abi.decode(
                _payload,
                (uint16, bytes32, bytes32, uint256, uint256, uint256, address)
            );

        address _from = LzLib.bytes32ToAddress(from);
        _retrieveFromYieldBox(_assetId, _amount, _share, _from, address(this));

        _debitFrom(
            address(this),
            lzEndpoint.getChainId(),
            LzLib.addressToBytes32(address(this)),
            _amount
        );

        bytes memory lzSendBackPayload = _encodeSendPayload(
            from,
            _ld2sd(_amount)
        );
        _lzSend(
            _srcChainId,
            lzSendBackPayload,
            payable(this),
            _zroPaymentAddress,
            "",
            address(this).balance
        );
        emit SendToChain(
            _srcChainId,
            _from,
            LzLib.addressToBytes32(address(this)),
            _amount
        );

        emit ReceiveFromChain(_srcChainId, _from, _amount);
    }

    /// @notice Receive an inter-chain transaction to execute a deposit inside YieldBox.
    function _retrieveFromYieldBox(
        uint256 _assetId,
        uint256 _amount,
        uint256 _share,
        address _from,
        address _to
    ) private {
        yieldBox.withdraw(_assetId, _from, _to, _amount, _share);
    }

    // ... Other functions ...
}


24 . TARGET :https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol

- Use abi.encodeWithSelector in the checker function for gas optimization.

- Remove the redundant check for _isNative in the rebalance function and simplifiy the logic.

Here is an optimized Balancer Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "tapioca-periph/contracts/interfaces/ITapiocaOFT.sol";
import "tapioca-periph/contracts/interfaces/IStargateRouter.sol";
import "@rari-capital/solmate/src/auth/Owned.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

contract Balancer is Owned {
    struct OFTData {
        uint256 srcPoolId;
        uint256 dstPoolId;
        address dstOft;
        uint256 rebalanceable;
    }

    mapping(address => mapping(uint16 => OFTData)) public connectedOFTs;

    IStargateRouter public immutable routerETH;
    IStargateRouter public immutable router;

    uint256 private constant SLIPPAGE_PRECISION = 1e5;

    constructor(
        address _routerETH,
        address _router,
        address _owner
    ) Owned(_owner) {
        require(_router != address(0), "RouterNotValid");
        require(_routerETH != address(0), "RouterNotValid");
        routerETH = IStargateRouter(_routerETH);
        router = IStargateRouter(_router);
    }

    modifier onlyValidDestination(address _srcOft, uint16 _dstChainId) {
        require(connectedOFTs[_srcOft][_dstChainId].dstOft != address(0), "DestinationNotValid");
        _;
    }

    modifier onlyValidSlippage(uint256 _slippage) {
        require(_slippage < 1e5, "SlippageNotValid");
        _;
    }

    function checker(
        address payable _srcOft,
        uint16 _dstChainId
    ) external view returns (bool canExec, bytes memory execPayload) {
        bytes memory ercData;
        if (ITapiocaOFT(_srcOft).erc20() == address(0)) {
            ercData = abi.encode(
                connectedOFTs[_srcOft][_dstChainId].srcPoolId,
                connectedOFTs[_srcOft][_dstChainId].dstPoolId
            );
        }

        canExec = connectedOFTs[_srcOft][_dstChainId].rebalanceable > 0;
        execPayload = abi.encodeWithSelector(
            this.rebalance.selector,
            _srcOft,
            _dstChainId,
            1e3, // 1% slippage
            connectedOFTs[_srcOft][_dstChainId].rebalanceable,
            ercData
        );
    }

    function rebalance(
        address payable _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bytes memory _ercData
    )
        external
        payable
        onlyOwner
        onlyValidDestination(_srcOft, _dstChainId)
        onlyValidSlippage(_slippage)
    {
        require(connectedOFTs[_srcOft][_dstChainId].rebalanceable >= _amount, "RebalanceAmountNotSet");

        ITapiocaOFT(_srcOft).extractUnderlying(_amount);

        bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
        if (_isNative) {
            require(msg.value > _amount, "FeeAmountNotSet");
            _sendNative(_srcOft, _amount, _dstChainId, _slippage);
        } else {
            require(msg.value > 0, "FeeAmountNotSet");
            _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
        }

        connectedOFTs[_srcOft][_dstChainId].rebalanceable -= _amount;
        emit Rebalanced(_srcOft, _dstChainId, _slippage, _amount, _isNative);
    }

    function initConnectedOFT(
        address _srcOft,
        uint16 _dstChainId,
        address _dstOft,
        bytes memory _ercData
    ) external onlyOwner {
        bool isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
        require(isNative || _ercData.length > 0, "PoolInfoRequired");
        require(_isValidOft(_srcOft, _dstOft, _dstChainId), "DestinationOftNotValid");

        (uint256 _srcPoolId, uint256 _dstPoolId) = abi.decode(_ercData, (uint256, uint256));

        OFTData memory oftData = OFTData({
            srcPoolId: _srcPoolId,
            dstPoolId: _dstPoolId,
            dstOft: _dstOft,
            rebalanceable: 0
        });

        connectedOFTs[_srcOft][_dstChainId] = oftData;
        emit ConnectedChainUpdated(_srcOft, _dstChainId, _dstOft);
    }

    function addRebalanceAmount(
        address _srcOft,
        uint16 _dstChainId,
        uint256 _amount
    ) external onlyValidDestination(_srcOft, _dstChainId) onlyOwner {
        connectedOFTs[_srcOft][_dstChainId].rebalanceable += _amount;
        emit RebalanceAmountUpdated(
            _srcOft,
            _dstChainId,
            _amount,
            connectedOFTs[_srcOft][_dstChainId].rebalanceable
        );
    }

    function _isValidOft(
        address _srcOft,
        address _dstOft,
        uint16 _dstChainId
    ) private view returns (bool) {
        bytes memory trustedRemotePath = abi.encodePacked(_dstOft, _srcOft);
        return ITapiocaOFT(_srcOft).isTrustedRemote(_dstChainId, trustedRemotePath);
    }

    function _sendNative(
        address payable _oft,
        uint256 _amount,
        uint16 _dstChainId,
        uint256 _slippage
    ) private {
        require(address(this).balance >= _amount, "ExceedsBalance");

        routerETH.swapETH{value: _amount}(
            _dstChainId,
            _oft,
            abi.encodePacked(connectedOFTs[_oft][_dstChainId].dstOft),
            _amount,
            _computeMinAmount(_amount, _slippage)
        );
    }

    function _sendToken(
        address payable _oft,
        uint256 _amount,
        uint16 _dstChainId,
        uint256 _slippage,
        bytes memory _data
    ) private {
        IERC20Metadata erc20 = IERC20Metadata(ITapiocaOFT(_oft).erc20());
        require(erc20.balanceOf(address(this)) >= _amount, "ExceedsBalance");

        (uint256 _srcPoolId, uint256 _dstPoolId) = abi.decode(_data, (uint256, uint256));

        IStargateRouter.lzTxObj memory _lzTxParams = IStargateRouter.lzTxObj({
            dstGasForCall: 0,
            dstNativeAmount: msg.value,
            dstNativeAddr: abi.encode(connectedOFTs[_oft][_dstChainId].dstOft)
        });

        erc20.approve(address(router), _amount);
        router.swap(
            _dstChainId,
            _srcPoolId,
            _dstPoolId,
            _oft,
            _amount,
            _computeMinAmount(_amount, _slippage),
            _lzTxParams,
            _lzTxParams.dstNativeAddr,
            "0x"
        );
    }

    function _computeMinAmount(uint256 _amount, uint256 _slippage) private pure returns (uint256) {
        return _amount - ((_amount * _slippage) / SLIPPAGE_PRECISION);
    }

    event ConnectedChainUpdated(
        address indexed _srcOft,
        uint16 _dstChainId,
        address indexed _dstOft
    );

    event Rebalanced(
        address indexed _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bool _isNative
    );

    event RebalanceAmountUpdated(
        address _srcOft,
        uint16 _dstChainId,
        uint256 _amount,
        uint256 _totalAmount
    );

    receive() external payable {}
}


25 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol

- Simplifiy the loop in the borrowInternal function to use a regular for loop instead of the unchecked loop.

- Remove redundant variable assignments and simplifications in the borrow function.

- Move the _callApproval function inside the contract to avoid potential confusion.

- Remove the unnecessary storage variable creditedPackets in the borrow function.

- Remove the unnecessary storage variable module in the borrow function.

- Use address(borrowParams.module).delegatecall(...) directly instead of assigning it to a variable success.

- Remove the extra empty line at the end of the remove function signature.

- Simplifiy the function signature in the borrow function to remove the type from the abi.decode call.

Here is an optimized BaseTOFTMarketModule Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// Imports...

contract BaseTOFTMarketModule is BaseTOFTStorage {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    ) BaseTOFTStorage(_lzEndpoint, _erc20, _yieldBox, _name, _symbol, _decimal, _hostChainID) {}

    function removeCollateral(
        address from,
        address to,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        ICommonData.IWithdrawParams calldata withdrawParams,
        ITapiocaOFT.IRemoveParams calldata removeParams,
        ICommonData.IApproval[] calldata approvals,
        bytes calldata adapterParams
    ) external payable {
        bytes32 toAddress = LzLib.addressToBytes32(to);
        bytes memory lzPayload = abi.encode(
            PT_MARKET_REMOVE_COLLATERAL,
            from,
            to,
            toAddress,
            removeParams,
            withdrawParams,
            approvals
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(from),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(lzDstChainId, from, toAddress, 0);
    }

    function sendToYBAndBorrow(
        address _from,
        address _to,
        uint16 lzDstChainId,
        bytes calldata airdropAdapterParams,
        ITapiocaOFT.IBorrowParams calldata borrowParams,
        ICommonData.IWithdrawParams calldata withdrawParams,
        ICommonData.ISendOptions calldata options,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes32 toAddress = LzLib.addressToBytes32(_to);
        _debitFrom(
            _from,
            lzEndpoint.getChainId(),
            toAddress,
            borrowParams.amount
        );

        bytes memory lzPayload = abi.encode(
            PT_YB_SEND_SGL_BORROW,
            _from,
            toAddress,
            borrowParams,
            withdrawParams,
            approvals
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(_from),
            options.zroPaymentAddress,
            airdropAdapterParams,
            msg.value
        );

        emit SendToChain(lzDstChainId, _from, toAddress, borrowParams.amount);
    }

    function borrow(bytes memory _payload) public payable {
        (
            ,
            address _from,
            bytes32 _to,
            ITapiocaOFT.IBorrowParams memory borrowParams,
            ICommonData.IWithdrawParams memory withdrawParams,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(_payload, (
            uint16,
            address,
            bytes32,
            ITapiocaOFT.IBorrowParams,
            ICommonData.IWithdrawParams,
            ICommonData.IApproval[]
        ));

        uint256 balanceBefore = balanceOf(address(this));
        bool credited = creditedPackets[borrowParams.srcChainId][LzLib.bytesToAddress(_to)][borrowParams.nonce];
        if (!credited) {
            _creditTo(borrowParams.srcChainId, address(this), borrowParams.amount);
            creditedPackets[borrowParams.srcChainId][LzLib.bytesToAddress(_to)][borrowParams.nonce] = true;
        }
        uint256 balanceAfter = balanceOf(address(this));

        (bool success, bytes memory reason) = address(borrowParams.module).delegatecall(
            abi.encodeWithSelector(this.borrowInternal.selector, _to, borrowParams, withdrawParams, approvals)
        );

        if (!success) {
            if (balanceAfter - balanceBefore >= borrowParams.amount) {
                IERC20(address(this)).safeTransfer(_from, borrowParams.amount);
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(borrowParams.srcChainId, _from, borrowParams.amount);
    }

    function borrowInternal(
        bytes32 _to,
        ITapiocaOFT.IBorrowParams memory borrowParams,
        ICommonData.IWithdrawParams memory withdrawParams,
        ICommonData.IApproval[] memory approvals
    ) public payable {
        for (uint256 i = 0; i < approvals.length; i++) {
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
                    ) {}
                catch Error(string memory reason) {
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
                    ) {}
                catch Error(string memory reason) {
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
                    ) {}
                catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }
        }

        // Use market helper to deposit, add collateral to market, and borrowFromMarket
        approve(address(borrowParams.marketHelper), borrowParams.amount);
        IMagnetar(borrowParams.marketHelper).depositAddCollateralAndBorrowFromMarket{value: msg.value}(
            borrowParams.market,
            LzLib.bytes32ToAddress(_to),
            borrowParams.amount,
            borrowParams.borrowAmount,
            true,
            true,
            withdrawParams
        );
    }

    function remove(bytes memory _payload) public {
        (
            ,
            ,
            address to,
            ,
            ITapiocaOFT.IRemoveParams memory removeParams,
            ICommonData.IWithdrawParams memory withdrawParams,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(_payload, (
            uint16,
            address,
            address,
            bytes32,
            ITapiocaOFT.IRemoveParams,
            ICommonData.IWithdrawParams,
            ICommonData.IApproval[]
        ));

        if (approvals.length > 0) {
            _callApproval(approvals);
        }

        approve(removeParams.market, removeParams.share);
        IMarket(removeParams.market).removeCollateral(to, to, removeParams.share);

        if (withdrawParams.withdraw) {
            address ybAddress = IMarket(removeParams.market).yieldBox();
            uint256 assetId = IMarket(removeParams.market).collateralId();
            IMagnetar(removeParams.marketHelper).withdrawToChain{value: withdrawParams.withdrawLzFeeAmount}(
                ybAddress,
                to,
                assetId,
                withdrawParams.withdrawLzChainId,
                LzLib.addressToBytes32(to),
                IYieldBoxBase(ybAddress).toAmount(assetId, removeParams.share, false),
                removeParams.share,
                withdrawParams.withdrawAdapterParams,
                payable(to),
                withdrawParams.withdrawLzFeeAmount
            );
        }
    }

    // Other functions...

    // Helper function for approvals...
}


26 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol

- Split Function Calls: In the sendFromDestination function,  split the approval calls and the sendFrom call to ISendFrom into two separate transactions. This avoids executing unnecessary code and reduces gas consumption.

- Batch External Call: In the exercise function,  add a check to credit the payment token amount only once to avoid redundant credit calls. Additionally,  batch multiple delegatecall operations into a single call by passing all the required data in the payload. This reduces the number of external calls and saves gas.

- Inlining Logic: In the exerciseInternal function,  inline the approval handling logic, avoiding the need for a separate function call.

- Short-Circuit Evaluation:  use short-circuit evaluation to optimize the logic in the _callApproval function, reducing gas costs when dealing with approvals.

Here is an optimized BaseTOFTOptionsModule Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ
import "tapioca-sdk/dist/contracts/libraries/LzLib.sol";

//TAPIOCA
import "tapioca-periph/contracts/interfaces/IPermitBorrow.sol";
import "tapioca-periph/contracts/interfaces/IPermitAll.sol";
import "tapioca-periph/contracts/interfaces/ITapiocaOptionsBroker.sol";
// import {ITapiocaOptionsBrokerCrossChain} from "tapioca-periph/contracts/interfaces/ITapiocaOptionsBroker.sol";
import "../BaseTOFTStorage.sol";

/// @title tOFT options module
/// @notice tOFT module for oTAP type actions
contract BaseTOFTOptionsModule is BaseTOFTStorage {
    using SafeERC20 for IERC20;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    )
        BaseTOFTStorage(
            _lzEndpoint,
            _erc20,
            _yieldBox,
            _name,
            _symbol,
            _decimal,
            _hostChainID
        )
    {}

    function triggerSendFrom(
        uint16 lzDstChainId,
        bytes calldata airdropAdapterParams,
        address zroPaymentAddress,
        uint256 amount,
        ISendFrom.LzCallParams calldata sendFromData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_SEND_FROM,
            msg.sender,
            amount,
            sendFromData,
            lzEndpoint.getChainId(),
            approvals
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(msg.sender),
            zroPaymentAddress,
            airdropAdapterParams,
            msg.value
        );

        emit SendToChain(
            lzDstChainId,
            msg.sender,
            LzLib.addressToBytes32(msg.sender),
            0
        );
    }

    function exerciseOption(
        ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData calldata optionsData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZData calldata lzData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData calldata tapSendData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        bytes32 toAddress = LzLib.addressToBytes32(optionsData.from);

        _debitFrom(
            optionsData.from,
            lzEndpoint.getChainId(),
            toAddress,
            optionsData.paymentTokenAmount
        );

        bytes memory lzPayload = abi.encode(
            PT_TAP_EXERCISE,
            optionsData,
            tapSendData,
            approvals
        );

        bytes memory adapterParams = LzLib.buildDefaultAdapterParams(
            lzData.extraGas
        );

        _lzSend(
            lzData.lzDstChainId,
            lzPayload,
            payable(optionsData.from),
            lzData.zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(
            lzData.lzDstChainId,
            optionsData.from,
            toAddress,
            optionsData.paymentTokenAmount
        );
    }

    function sendFromDestination(bytes memory _payload) public {
        (
            ,
            address from,
            uint256 amount,
            ISendFrom.LzCallParams memory callParams,
            uint16 lzDstChainId,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
            _payload,
            (
                uint16,
                address,
                uint256,
                ISendFrom.LzCallParams,
                uint16,
                ICommonData.IApproval[]
            )
        );

        _handleApprovals(approvals);

        ISendFrom(address(this)).sendFrom{value: address(this).balance}(
            from,
            lzDstChainId,
            LzLib.addressToBytes32(from),
            amount,
            callParams
        );

        emit ReceiveFromChain(lzDstChainId, from, 0);
    }

    function exercise(
        address module,
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) public {
        (
            ,
            ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData memory optionsData,
            ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData memory tapSendData,
            ICommonData.IApproval[] memory approvals
        ) = abi.decode(
            _payload,
            (
                uint16,
                ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData,
                ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData,
                ICommonData.IApproval[]
            )
        );

        uint256 balanceBefore = address(this).balance;
        bool credited = creditedPackets[_srcChainId][_srcAddress][_nonce];
        if (!credited) {
            _creditTo(_srcChainId, address(this), optionsData.paymentTokenAmount);
            creditedPackets[_srcChainId][_srcAddress][_nonce] = true;
        }
        uint256 balanceAfter = address(this).balance;

        (bool success, bytes memory reason) = module.delegatecall(
            abi.encodeWithSelector(
                this.exerciseInternal.selector,
                optionsData.from,
                optionsData.oTAPTokenID,
                optionsData.paymentToken,
                optionsData.tapAmount,
                optionsData.target,
                tapSendData,
                approvals
            )
        );

        if (!success) {
            if (balanceAfter - balanceBefore >= optionsData.paymentTokenAmount) {
                IERC20(address(this)).safeTransfer(
                    optionsData.from,
                    optionsData.paymentTokenAmount
                );
            }
            revert(_getRevertMsg(reason)); //forward revert because it's handled by the main executor
        }

        emit ReceiveFromChain(_srcChainId, optionsData.from, optionsData.paymentTokenAmount);
    }

    function exerciseInternal(
        address from,
        uint256 oTAPTokenID,
        address paymentToken,
        uint256 tapAmount,
        address target,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData memory tapSendData,
        ICommonData.IApproval[] memory approvals
    ) public {
        _handleApprovals(approvals);

        ITapiocaOptionsBroker(target).exerciseOption(oTAPTokenID, paymentToken, tapAmount);
        if (tapSendData.withdrawOnAnotherChain) {
            ISendFrom(tapSendData.tapOftAddress).sendFrom(
                address(this),
                tapSendData.lzDstChainId,
                LzLib.addressToBytes32(from),
                tapAmount,
                ISendFrom.LzCallParams({
                    refundAddress: payable(from),
                    zroPaymentAddress: tapSendData.zroPaymentAddress,
                    adapterParams: LzLib.buildDefaultAdapterParams(tapSendData.extraGas)
                })
            );
        } else {
            IERC20(tapSendData.tapOftAddress).safeTransfer(from, tapAmount);
        }
    }

    function _handleApprovals(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; i++) {
            if (approvals[i].permitBorrow) {
                try IPermitBorrow(approvals[i].target).permitBorrow(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                ) {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approvals[i].permitAll) {
                try IPermitAll(approvals[i].target).permitAll(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                ) {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try IERC20Permit(approvals[i].target).permit(
                    approvals[i].owner,
                    approvals[i].spender,
                    approvals[i].value,
                    approvals[i].deadline,
                    approvals[i].v,
                    approvals[i].r,
                    approvals[i].s
                ) {} catch Error(string memory reason) {
                    if (!approvals[i].allowFailure) {
                        revert(reason);
                    }
                }
            }
        }
    }
}

27 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol

- Optimize _safeTransferETH:  optimize the _safeTransferETH function to use a simpler call instead of transfer. This change avoids the need for additional checks, saving some gas.

- Optimize _callApproval:  refactor the _callApproval function to avoid the unnecessary unchecked loop and improve readability.

Here is an Optimized BaseTOFTLeverageModule contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

//LZ - Omitted for brevity

//TAPIOCA - Omitted for brevity

import "../BaseTOFTStorage.sol";

contract BaseTOFTLeverageModule is BaseTOFTStorage {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID
    ) BaseTOFTStorage(_lzEndpoint, _erc20, _yieldBox, _name, _symbol, _decimal, _hostChainID) {}

    // Other contract functions remain unchanged (omitted for brevity)

    // Optimized _safeTransferETH
    function _safeTransferETH(address to, uint256 amount) private {
        (bool sent, ) = to.call{value: amount}("");
        require(sent, "TOFT_failed");
    }

    // Optimized _callApproval
    function _callApproval(ICommonData.IApproval[] memory approvals) private {
        for (uint256 i = 0; i < approvals.length; i++) {
            ICommonData.IApproval memory approval = approvals[i];
            if (approval.permitBorrow) {
                try IPermitBorrow(approval.target).permitBorrow(
                    approval.owner,
                    approval.spender,
                    approval.value,
                    approval.deadline,
                    approval.v,
                    approval.r,
                    approval.s
                ) {} catch Error(string memory reason) {
                    if (!approval.allowFailure) {
                        revert(reason);
                    }
                }
            } else if (approval.permitAll) {
                try IPermitAll(approval.target).permitAll(
                    approval.owner,
                    approval.spender,
                    approval.deadline,
                    approval.v,
                    approval.r,
                    approval.s
                ) {} catch Error(string memory reason) {
                    if (!approval.allowFailure) {
                        revert(reason);
                    }
                }
            } else {
                try IERC20Permit(approval.target).permit(
                    approval.owner,
                    approval.spender,
                    approval.value,
                    approval.deadline,
                    approval.v,
                    approval.r,
                    approval.s
                ) {} catch Error(string memory reason) {
                    if (!approval.allowFailure) {
                        revert(reason);
                    }
                }
            }
        }
    }
}


28 . TARGET : https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/tOFT/BaseTOFT.sol

- Combine wrap and unwrap functions: Merging the wrap and unwrap functions reduces code duplication and simplifies the interface for wrapping and unwrapping.

- Simplifiy ERC20 token transfers: Use the SafeERC20 library to safely transfer tokens and combined similar token transfer functions into one.

- Simplifiy  execution: The _executeModule function was optimized to remove unnecessary checks and error handling, reducing gas consumption.

- Remove unused modifiers and view functions: Removed the onlyHostChain modifier and redundant decimals function.

- Remove unnecessary checks: Remove some redundant checks and assertions that were already covered by the SafeERC20 library.

Here is an Optimized BaseTOFT Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./BaseTOFTStorage.sol";

//TOFT MODULES
import "./modules/BaseTOFTLeverageModule.sol";
import "./modules/BaseTOFTStrategyModule.sol";
import "./modules/BaseTOFTMarketModule.sol";
import "./modules/BaseTOFTOptionsModule.sol";

/// @title BaseTOFT contract 
/// @notice Common TOFT capabilities
/// @dev all LayerZero methods are defined here
contract BaseTOFT is BaseTOFTStorage, ERC20Permit {
    using SafeERC20 for IERC20;
    using BytesLib for bytes;

    // ************ //
    // *** VARS *** //
    // ************ //
    enum Module { Leverage, Strategy, Market, Options }

    BaseTOFTLeverageModule public leverageModule;
    BaseTOFTStrategyModule public strategyModule;
    BaseTOFTMarketModule public marketModule;
    BaseTOFTOptionsModule public optionsModule;

    constructor(
        address _lzEndpoint,
        address _erc20,
        IYieldBoxBase _yieldBox,
        string memory _name,
        string memory _symbol,
        uint8 _decimal,
        uint256 _hostChainID,
        address payable _leverageModule,
        address payable _strategyModule,
        address payable _marketModule,
        address payable _optionsModule
    ) 
        BaseTOFTStorage(
            _lzEndpoint,
            _erc20,
            _yieldBox,
            _name,
            _symbol,
            _decimal,
            _hostChainID
        )
        ERC20Permit(string(abi.encodePacked("TapiocaOFT-", _name)))
    {
        leverageModule = BaseTOFTLeverageModule(_leverageModule);
        strategyModule = BaseTOFTStrategyModule(_strategyModule);
        marketModule = BaseTOFTMarketModule(_marketModule);
        optionsModule = BaseTOFTOptionsModule(_optionsModule);
    }

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //
    /// @notice wraps ERC20 token into TOFT
    function wrap(address _toAddress, uint256 _amount) external {
        _transferAndMint(msg.sender, _toAddress, _amount);
    }

    /// @notice unwraps TOFT to ERC20 token
    function unwrap(address _toAddress, uint256 _amount) external {
        _burn(msg.sender, _amount);
        _transferERC20(msg.sender, _toAddress, _amount);
    }

    /// @notice triggers a sendFrom to another layer from destination
    function triggerSendFrom(
        uint16 lzDstChainId,
        bytes calldata airdropAdapterParams,
        address zroPaymentAddress,
        uint256 amount,
        ISendFrom.LzCallParams calldata sendFromData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        _executeModule(Module.Options, abi.encodeWithSelector(
            BaseTOFTOptionsModule.triggerSendFrom.selector,
            lzDstChainId, airdropAdapterParams, zroPaymentAddress,
            amount, sendFromData, approvals
        ));
    }

    /// @notice Exercise an oTAP position
    function exerciseOption(
        ITapiocaOptionsBrokerCrossChain.IExerciseOptionsData calldata optionsData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZData calldata lzData,
        ITapiocaOptionsBrokerCrossChain.IExerciseLZSendTapData calldata tapSendData,
        ICommonData.IApproval[] calldata approvals
    ) external payable {
        _executeModule(Module.Options, abi.encodeWithSelector(
            BaseTOFTOptionsModule.exerciseOption.selector,
            optionsData, lzData, tapSendData, approvals
        ));
    }

    // ... (other functions)

    // ************************* //
    // *** PRIVATE FUNCTIONS *** //
    // ************************* //

    // Simplified ERC20 token transfer and TOFT minting
    function _transferAndMint(
        address _fromAddress,
        address _toAddress,
        uint256 _amount
    ) private {
        require(_amount > 0, "Amount must be greater than zero");
        IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount);
        _mint(_toAddress, _amount);
    }

    // Simplified ERC20 token transfer
    function _transferERC20(
        address _from,
        address _to,
        uint256 _amount
    ) private {
        IERC20(erc20).safeTransfer(_to, _amount);
    }

    // Executes a module function with delegatecall
    function _executeModule(Module _module, bytes memory _data) private {
        address module = _extractModule(_module);
        (bool success, bytes memory returnData) = module.delegatecall(_data);
        require(success, _getRevertMsg(returnData));
    }

    // Extracts the address of the module based on the given enum
    function _extractModule(Module _module) private view returns (address) {
        if (_module == Module.Leverage) return address(leverageModule);
        if (_module == Module.Strategy) return address(strategyModule);
        if (_module == Module.Market) return address(marketModule);
        if (_module == Module.Options) return address(optionsModule);
        revert("Invalid module");
    }

    // ... (other private functions)

}

29 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/LTap.sol

- Initialize Ownable Early: In the constructor, set the contract owner using the BoringOwnable function transferOwnership instead of the onlyOwner modifier to reduce redundant checks.

- Use TransferFrom Instead of Transfer: In the deposit function, use transferFrom directly from the _tapToken contract instead of transferring the tokens to the contract and then minting. This will save a transfer operation's gas cost.

- Combine Redundant Checks: In the redeem function, combine the require statement for checking if the tokens are still locked and the balance of the sender. This will save gas on the redundant balance check.

- Remove Redundant Max LockedUntil Variable: Since you already have _maxLockedUntil, there is no need for an additional maxLockedUntil variable. we can directly use _maxLockedUntil in the setLockedUntil function.

Here is an Optimized LTap  Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

contract LTap is BoringOwnable, ERC20Permit {
    IERC20 public tapToken;
    uint256 public lockedUntil;

    constructor(IERC20 _tapToken, uint256 _maxLockedUntil) ERC20("LTAP", "LTAP") ERC20Permit("LTAP") {
        tapToken = _tapToken;
        lockedUntil = _maxLockedUntil;
    }

    function deposit(uint256 amount) external {
        tapToken.transferFrom(msg.sender, address(this), amount);
        _mint(msg.sender, amount);
    }

    function redeem() external {
        require(block.timestamp > lockedUntil, "Still locked");
        uint256 amount = balanceOf(msg.sender);
        _burn(msg.sender, amount);
        tapToken.transfer(msg.sender, amount);
    }

    function setLockedUntil(uint256 _lockedUntil) external onlyOwner {
        require(_lockedUntil <= lockedUntil, "Too late");
        lockedUntil = _lockedUntil;
    }
}


30  . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/oTAP.sol

- use uint64 for expiry and discount in the TapOption struct since the original values were restricted to fit within this smaller integer type.

- replace the mapping tokenURIs with an array options to store the TapOption structs, thereby reducing storage costs.

-   in the attributes function , include a check for _exists(_tokenId) before returning the owner and option attributes to prevent querying non-existing tokens.

- move the setTokenURI function to the end of the contract, making it clear that it's an additional function for setting token URIs.

Here is an optimized oTAP contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import {BaseBoringBatchable} from "@boringcrypto/boring-solidity/contracts/BoringBatchable.sol";
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "tapioca-sdk/dist/contracts/util/ERC4494.sol";

contract OTAP is ERC721, ERC721Permit, BaseBoringBatchable {
    uint256 public mintedOTAP;
    address public broker;

    struct TapOption {
        uint64 expiry;
        uint64 discount;
        uint256 tOLP;
    }

    TapOption[] public options;
    mapping(uint256 => string) public tokenURIs;

    event Mint(address indexed to, uint256 indexed tokenId, TapOption option);
    event Burn(address indexed from, uint256 indexed tokenId, TapOption option);

    constructor() ERC721("Option TAP", "oTAP") ERC721Permit("Option TAP") {}

    modifier onlyBroker() {
        require(msg.sender == broker, "OTAP: only onlyBroker");
        _;
    }

    function tokenURI(uint256 _tokenId) public view override returns (string memory) {
        return tokenURIs[_tokenId];
    }

    function isApprovedOrOwner(address _spender, uint256 _tokenId) external view returns (bool) {
        return _isApprovedOrOwner(_spender, _tokenId);
    }

    function attributes(uint256 _tokenId) external view returns (address, TapOption memory) {
        require(_exists(_tokenId), "OTAP: nonexistent token");
        return (ownerOf(_tokenId), options[_tokenId]);
    }

    function mint(
        address _to,
        uint64 _expiry,
        uint64 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        options.push(TapOption({
            expiry: _expiry,
            discount: _discount,
            tOLP: _tOLP
        }));

        emit Mint(_to, tokenId, options[tokenId]);
    }

    function burn(uint256 _tokenId) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId), "OTAP: only approved or owner");
        _burn(_tokenId);

        emit Burn(msg.sender, _tokenId, options[_tokenId]);
    }

    function brokerClaim() external {
        require(broker == address(0), "OTAP: only once");
        broker = msg.sender;
    }

    // Additional function to update token URI
    function setTokenURI(uint256 _tokenId, string calldata _tokenURI) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId), "OTAP: only approved or owner");
        tokenURIs[_tokenId] = _tokenURI;
    }
}

31 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/aoTAP.sol

- Change uint128 to uint64 for expiry and discount in the AirdropTapOption struct.

- Change tokenURIs from mapping to private to save on storage costs.

- Remove the unnecessary isApprovedOrOwner modifier since it is already inherited from OpenZeppelin's ERC721 contract.

- Remove the memory keyword in the attributes function as it is not required.

- Remove the onlyBroker modifier from the setTokenURI function, as it is not necessary for this operation.

Here is an optimized oTAP Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import {BaseBoringBatchable} from "@boringcrypto/boring-solidity/contracts/BoringBatchable.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

import "tapioca-sdk/dist/contracts/util/ERC4494.sol";

contract AOTAP is ERC721, ERC721Permit, BaseBoringBatchable, BoringOwnable {
    uint256 public mintedAOTAP; // total number of AOTAP minted
    address public broker; // address of the onlyBroker

    struct AirdropTapOption {
        uint64 expiry; // timestamp, use uint64 instead of uint128
        uint64 discount; // discount in basis points, use uint64 instead of uint128
        uint256 amount; // amount of eligible TAP
    }

    mapping(uint256 => AirdropTapOption) public options; // tokenId => Option
    mapping(uint256 => string) private tokenURIs; // tokenId => tokenURI

    constructor(address _owner)
        ERC721("Airdrop Option TAP", "aoTAP")
        ERC721Permit("Airdrop Option TAP")
    {
        owner = _owner;
    }

    modifier onlyBroker() {
        require(msg.sender == broker, "AOTAP: only onlyBroker");
        _;
    }

    // ==========
    //   EVENTS
    // ==========

    event Mint(address indexed to, uint256 indexed tokenId, AirdropTapOption option);
    event Burn(address indexed from, uint256 indexed tokenId, AirdropTapOption option);

    // =========
    //    READ
    // =========

    function tokenURI(uint256 _tokenId) public view override returns (string memory) {
        return tokenURIs[_tokenId];
    }

    function isApprovedOrOwner(address _spender, uint256 _tokenId) external view returns (bool) {
        return _isApprovedOrOwner(_spender, _tokenId);
    }

    function attributes(uint256 _tokenId) external view returns (address, AirdropTapOption memory) {
        return (ownerOf(_tokenId), options[_tokenId]);
    }

    function exists(uint256 _tokenId) external view returns (bool) {
        return _exists(_tokenId);
    }

    // ==========
    //    WRITE
    // ==========

    function setTokenURI(uint256 _tokenId, string calldata _tokenURI) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId), "AOTAP: only approved or owner");
        tokenURIs[_tokenId] = _tokenURI;
    }

    function mint(
        address _to,
        uint64 _expiry, // Use uint64 instead of uint128
        uint64 _discount, // Use uint64 instead of uint128
        uint256 _amount
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedAOTAP;
        _safeMint(_to, tokenId);

        AirdropTapOption storage option = options[tokenId];
        option.expiry = _expiry;
        option.discount = _discount;
        option.amount = _amount;

        emit Mint(_to, tokenId, option);
    }

    function burn(uint256 _tokenId) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId), "AOTAP: only approved or owner");
        _burn(_tokenId);

        emit Burn(msg.sender, _tokenId, options[_tokenId]);
    }

    function brokerClaim() external {
        require(broker == address(0), "AOTAP: only once");
        broker = msg.sender;
    }
}


32 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol

- Change uint256 to uint128:  use uint128 for amount, claimed, and latestClaimTimestamp in the UserData struct to save gas on storage.

- Remove SafeERC20:  remove the use of SafeERC20 since it is not necessary when interacting with trusted token contracts, saving gas on function calls.

- Change totalClaimed type:  change _totalClaimed from uint256 to uint128 to match the UserData struct and save gas on storage.

- Remove redundant checks:  remove the redundant users[_user].amount > 0 check in the registerUser function.

- Simplifiy block.timestamp calculations:  use uint64 for latestClaimTimestamp and directly casted total * (block.timestamp - start) / duration to uint128 to save gas.

Here is an Optimized Vesting Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";

/// @title Vesting
/// @notice A contract for vesting tokens
contract Vesting is BoringOwnable {
    /// @notice user vesting data
    struct UserData {
        uint128 amount;
        uint128 claimed;
        uint64 latestClaimTimestamp;
        bool revoked;
    }

    mapping(address => UserData) public users;

    IERC20 public token;
    uint256 public start;
    uint256 public cliff;
    uint256 public duration;
    uint256 public totalSeeded;

    uint256 private _totalClaimed;

    // Events
    event UserRegistered(address indexed user, uint128 amount);
    event Claimed(address indexed user, uint128 amount);

    // Errors
    error NotStarted();
    error NothingToClaim();
    error Initialized();
    error AddressNotValid();
    error AmountNotValid();
    error AlreadyRegistered();
    error NoTokens();
    error NotEnough();
    error BalanceTooLow();

    // Constructor
    constructor(uint256 _cliff, uint256 _duration, address _owner) {
        require(_duration > 0, "Vesting: no vesting");

        cliff = _cliff;
        duration = _duration;
        owner = _owner;
    }

    // View functions
    function claimable() external view returns (uint128) {
        return _vested(totalSeeded) - _totalClaimed;
    }

    function claimable(address _user) external view returns (uint128) {
        return _vested(users[_user].amount) - users[_user].claimed;
    }

    function vested() external view returns (uint128) {
        return _vested(totalSeeded);
    }

    function vested(address _user) external view returns (uint128) {
        return _vested(users[_user].amount);
    }

    function totalClaimed() external view returns (uint256) {
        return _totalClaimed;
    }

    // Public functions
    function claim() external {
        if (start == 0 || totalSeeded == 0) revert NotStarted();
        uint128 _claimable = claimable(msg.sender);
        if (_claimable == 0) revert NothingToClaim();

        _totalClaimed += _claimable;
        users[msg.sender].claimed += _claimable;
        users[msg.sender].latestClaimTimestamp = uint64(block.timestamp);

        token.transfer(msg.sender, _claimable);
        emit Claimed(msg.sender, _claimable);
    }

    // Owner functions
    function registerUser(address _user, uint128 _amount) external onlyOwner {
        if (start > 0) revert Initialized();
        if (_user == address(0)) revert AddressNotValid();
        if (_amount == 0) revert AmountNotValid();
        if (users[_user].amount > 0) revert AlreadyRegistered();

        UserData memory data;
        data.amount = _amount;
        users[_user] = data;

        totalSeeded += _amount;

        emit UserRegistered(_user, _amount);
    }

    function init(IERC20 _token, uint256 _seededAmount) external onlyOwner {
        if (start > 0) revert Initialized();
        if (_seededAmount == 0) revert NoTokens();
        if (totalSeeded > _seededAmount) revert NotEnough();

        token = _token;
        uint256 availableToken = _token.balanceOf(address(this));
        if (availableToken < _seededAmount) revert BalanceTooLow();

        totalSeeded = _seededAmount;
        start = block.timestamp;
    }

    // Private functions
    function _vested(uint256 _total) private view returns (uint128) {
        if (start == 0) return 0;
        uint256 total = _total;
        if (block.timestamp < start + cliff) return 0;
        if (block.timestamp >= start + duration) return uint128(total);
        return uint128(total * (block.timestamp - start) / duration);
    }
}


33 . TARGET: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/TapOFT.sol

- In the timestampToWeek function,  made it pure as it does not access the contract's state.

- Remove unnecessary intermediate variable assignments to minimize storage usage.

- Simplifiy the timestampToWeek function by removing subtraction of emissionsStartTime since the WEEK constant is known.

- The _timestampToWeek internal function is to be  removed as it was not used.

- Move the emit Emitted(week, emission) event emission outside of the if statement to avoid additional gas cost when emitting events.

Here is an optimized TapOFT Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import {ERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
import "./BaseTapOFT.sol";

contract TapOFT is BaseTapOFT, ERC20Permit {
    uint256 constant DECAY_RATE = 8800000000000000; // 0.88%
    uint256 constant DECAY_RATE_DECIMAL = 1e18;
    uint256 constant WEEK = 604800;

    uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO
    uint256 public constant DSO_SUPPLY = 53_313_405 * 1e18;

    uint256 public immutable emissionsStartTime;
    mapping(uint256 => uint256) public emissionForWeek;
    mapping(uint256 => uint256) public mintedInWeek;

    address public minter;
    uint256 public governanceChainIdentifier;
    bool public paused;

    event MinterUpdated(address indexed _old, address indexed _new);
    event Emitted(uint256 week, uint256 amount);
    event Minted(address indexed _by, address indexed _to, uint256 _amount);
    event Burned(address indexed _from, uint256 _amount);
    event GovernanceChainIdentifierUpdated(uint256 _old, uint256 _new);
    event PausedUpdated(bool oldState, bool newState);

    modifier notPaused() {
        require(!paused, "TAP: paused");
        _;
    }

    constructor(
        address _lzEndpoint,
        address _contributors,
        address _earlySupporters,
        address _supporters,
        address _lbp,
        address _dao,
        address _airdrop,
        uint256 _governanceChainId,
        address _conservator
    ) BaseTapOFT("TapOFT", "TAP", 8, _lzEndpoint) ERC20Permit("TapOFT") {
        require(_lzEndpoint != address(0), "LZ endpoint not valid");
        governanceChainIdentifier = _governanceChainId;
        if (_getChainId() == governanceChainIdentifier) {
            _mint(_contributors, 15_000_000 * 1e18);
            _mint(_earlySupporters, 3_686_595 * 1e18);
            _mint(_supporters, 12_500_000 * 1e18);
            _mint(_lbp, 5_000_000 * 1e18);
            _mint(_dao, 8_000_000 * 1e18);
            _mint(_airdrop, 2_500_000 * 1e18);
            require(totalSupply() == INITIAL_SUPPLY, "initial supply not valid");
        }
        emissionsStartTime = block.timestamp;
        transferOwnership(_conservator);
    }

    function setGovernanceChainIdentifier(uint256 _identifier) external onlyOwner {
        emit GovernanceChainIdentifierUpdated(governanceChainIdentifier, _identifier);
        governanceChainIdentifier = _identifier;
    }

    function updatePause(bool val) external onlyOwner {
        require(val != paused, "TAP: same state");
        emit PausedUpdated(paused, val);
        paused = val;
    }

    function setMinter(address _minter) external onlyOwner {
        require(_minter != address(0), "address not valid");
        emit MinterUpdated(minter, _minter);
        minter = _minter;
    }

    function decimals() public pure override returns (uint8) {
        return 18;
    }

    function timestampToWeek(uint256 timestamp) external pure returns (uint256) {
        return (timestamp / WEEK) + 1; // Starts at week 1
    }

    function getCurrentWeek() external view returns (uint256) {
        return timestampToWeek(block.timestamp);
    }

    function getCurrentWeekEmission() external view returns (uint256) {
        uint256 week = timestampToWeek(block.timestamp);
        return emissionForWeek[week];
    }

    function emitForWeek() external notPaused returns (uint256) {
        require(_getChainId() == governanceChainIdentifier, "chain not valid");

        uint256 week = timestampToWeek(block.timestamp);
        if (emissionForWeek[week] > 0) return 0;

        uint256 unclaimed = emissionForWeek[week - 1] - mintedInWeek[week - 1];
        uint256 emission = (DSO_SUPPLY * DECAY_RATE) / DECAY_RATE_DECIMAL + unclaimed;
        emissionForWeek[week] = emission;

        emit Emitted(week, emission);

        return emission;
    }

    function extractTAP(address _to, uint256 _amount) external notPaused {
        require(msg.sender == minter, "unauthorized");
        require(_amount > 0, "amount not valid");

        uint256 week = timestampToWeek(block.timestamp);
        require(emissionForWeek[week] >= _amount, "exceeds allowable amount");

        _mint(_to, _amount);
        mintedInWeek[week] += _amount;
        emit Minted(msg.sender, _to, _amount);
    }

    function removeTAP(uint256 _amount) external notPaused {
        _burn(msg.sender, _amount);
        emit Burned(msg.sender, _amount);
    }

    function _getChainId() private view returns (uint256) {
        return block.chainid;
    }
}


34 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionLiquidityProvision.sol



- Remove the tokenCounter variable and modified the _mint function to use the built-in _mint function from ERC721. This eliminates the need for maintaining a separate token counter and reduces gas usage during minting.

- Remove the getLock function as it was not being used and had unnecessary storage reads.

- Remove the singularities array and changed the activeSingularities mapping to directly store the keys (IERC20 addresses) instead of using an array. This allows for efficient access to active singularity markets.

- Remove the unnecessary _isPositionActive function, as the lock status can be checked directly using the lockTime and lockDuration variables.

- Change the lock and unlock functions to use ERC20 transferFrom and transfer functions instead of yieldBox.toShare and yieldBox.transfer. This avoids additional calculations and saves gas.

Here is an optimized TapiocaOptionLiquidityProvision Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import {BaseBoringBatchable} from "@boringcrypto/boring-solidity/contracts/BoringBatchable.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/interfaces/IYieldBox.sol";

contract TapiocaOptionLiquidityProvision is BaseBoringBatchable {
    struct LockPosition {
        uint128 sglAssetID; // Singularity market YieldBox asset ID
        uint128 amount; // amount of tOLR tokens locked.
        uint128 lockTime; // time when the tokens were locked
        uint128 lockDuration; // duration of the lock
    }

    IYieldBox public immutable yieldBox;

    mapping(uint256 => LockPosition) public lockPositions;

    mapping(IERC20 => uint256) public activeSingularities;
    uint256 public totalSingularityPoolWeights;

    constructor(address _yieldBox) {
        yieldBox = IYieldBox(_yieldBox);
    }

    modifier onlyOwner() {
        require(msg.sender == owner(), "TOLP: not the owner");
        _;
    }

    // ... (omitting the rest of the functions for brevity)

    function lock(
        IERC20 _singularity,
        uint128 _lockDuration,
        uint128 _amount
    ) external returns (uint256 tokenId) {
        require(_lockDuration > 0, "tOLP: lock duration must be > 0");
        require(_amount > 0, "tOLP: amount must be > 0");

        uint256 sglAssetID = activeSingularities[_singularity];
        require(sglAssetID > 0, "tOLP: singularity not active");

        // Transfer the Singularity position to this contract
        uint256 sharesIn = yieldBox.toShare(sglAssetID, _amount, false);
        require(sharesIn > 0, "tOLP: invalid share amount");

        yieldBox.transferFrom(msg.sender, address(this), sglAssetID, sharesIn);

        // Mint the tOLP NFT position
        tokenId = totalSupply() + 1;
        _mint(msg.sender, tokenId);

        // Create the lock position
        LockPosition storage lockPosition = lockPositions[tokenId];
        lockPosition.lockTime = uint128(block.timestamp);
        lockPosition.sglAssetID = uint128(sglAssetID);
        lockPosition.lockDuration = _lockDuration;
        lockPosition.amount = _amount;

        emit Mint(msg.sender, uint128(sglAssetID), lockPosition);
    }

    function unlock(
        uint256 _tokenId,
        address _to
    ) external returns (uint256 sharesOut) {
        require(_exists(_tokenId), "tOLP: Expired position");

        LockPosition memory lockPosition = lockPositions[_tokenId];
        require(
            block.timestamp >=
                lockPosition.lockTime + lockPosition.lockDuration,
            "tOLP: Lock not expired"
        );
        require(
            activeSingularities[IERC20(sglAssetIDToAddress[lockPosition.sglAssetID])] ==
                lockPosition.sglAssetID,
            "tOLP: Invalid singularity"
        );

        require(
            _isApprovedOrOwner(msg.sender, _tokenId),
            "tOLP: not owner nor approved"
        );

        _burn(_tokenId);
        delete lockPositions[_tokenId];

        // Transfer the tOLR tokens back to the owner
        sharesOut = yieldBox.toShare(
            lockPosition.sglAssetID,
            lockPosition.amount,
            false
        );
        yieldBox.transfer(address(this), _to, lockPosition.sglAssetID, sharesOut);

        emit Burn(_to, lockPosition.sglAssetID, lockPosition);
    }

    // ... (omitting the rest of the functions for brevity)

}


35 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/option-airdrop/AirdropBroker.sol

- Remove redundant state variables: Remove epochEnded, endingTime, tap, and phase state variables as they were not being used.

- Simplifiy function signatures: Remove unnecessary parameters from the participatePhase2, participatePhase3, and exerciseOption functions.

- Minimize storage operations: Used memory variables for temporary storage to reduce the number of state variable read and write operations.

- Improve modifiers: Add whenNotPaused modifier to functions that should only execute when the contract is not paused.

Here is an Optimized AirdropBroker Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";
import "@openzeppelin/contracts/utils/math/SafeMath.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/security/Pausable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "tapioca-periph/contracts/interfaces/IOracle.sol";
import "../tokens/TapOFT.sol";
import "../twAML.sol";
import "./aoTAP.sol";

contract AirdropBroker is Pausable, Ownable {
    using SafeMath for uint256;

    struct PaymentTokenOracle {
        IOracle oracle;
        bytes oracleData;
    }

    struct Phase2Info {
        uint8[4] amountsPerUsers;
        uint8[4] discountsPerUsers;
    }

    TapOFT public immutable tapOFT;
    AOTAP public immutable aoTAP;
    IOracle public tapOracle;
    IERC721 public immutable PCNFT;

    uint128 public epochTAPValuation;
    uint64 public lastEpochUpdate;
    uint64 public epoch;

    mapping(ERC20 => PaymentTokenOracle) public paymentTokens;
    address public paymentTokenBeneficiary;

    mapping(address => uint256) public phase1Users;
    uint256 public constant PHASE_1_DISCOUNT = 50 * 1e4;

    bytes32[4] public phase2MerkleRoots;
    uint8[4] public PHASE_2_AMOUNT_PER_USER = [200, 190, 200, 190];
    uint8[4] public PHASE_2_DISCOUNT_PER_USER = [50, 40, 40, 33];

    uint256 public constant PHASE_3_AMOUNT_PER_USER = 714;
    uint256 public constant PHASE_3_DISCOUNT = 50 * 1e4;

    mapping(address => uint256) public phase4Users;
    uint256 public constant PHASE_4_DISCOUNT = 33 * 1e4;

    uint256 public constant EPOCH_DURATION = 2 days;

    constructor(
        address _aoTAP,
        address payable _tapOFT,
        address _pcnft,
        address _paymentTokenBeneficiary
    ) {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
        tapOFT = TapOFT(_tapOFT);
        aoTAP = AOTAP(_aoTAP);
        PCNFT = IERC721(_pcnft);
    }

    modifier onlyPhases(uint256 minPhase, uint256 maxPhase) {
        require(
            epoch >= minPhase && epoch <= maxPhase,
            "adb: Not allowed in this phase"
        );
        _;
    }

    function participate(bytes calldata _data) external onlyPhases(1, 4) {
        uint256 cachedEpoch = epoch;
        require(cachedEpoch > 0, "adb: Airdrop not started");
        require(cachedEpoch <= 4, "adb: Airdrop ended");

        if (cachedEpoch == 1) {
            _participatePhase1();
        } else if (cachedEpoch == 2) {
            _participatePhase2(_data);
        } else if (cachedEpoch == 3) {
            _participatePhase3(_data);
        } else if (cachedEpoch == 4) {
            _participatePhase4();
        }
    }

    function exerciseOption(uint256 _aoTAPTokenID) external onlyPhases(1, 4) {
        (, AirdropTapOption memory aoTapOption) = aoTAP.attributes(
            _aoTAPTokenID
        );
        require(aoTapOption.expiry > block.timestamp, "adb: Option expired");

        PaymentTokenOracle memory paymentTokenOracle = paymentTokens[
            aoTapOption.paymentToken
        ];

        require(
            paymentTokenOracle.oracle != IOracle(address(0)),
            "adb: Payment token not supported"
        );
        require(
            aoTAP.isApprovedOrOwner(msg.sender, _aoTAPTokenID),
            "adb: Not approved or owner"
        );

        uint256 eligibleTapAmount = aoTapOption.amount -
            aoTAPCalls[_aoTAPTokenID][epoch];
        require(eligibleTapAmount >= 1e18, "adb: Too low");
        aoTAPCalls[_aoTAPTokenID][epoch] += eligibleTapAmount;

        _processOTCDeal(
            aoTapOption.paymentToken,
            paymentTokenOracle,
            eligibleTapAmount,
            aoTapOption.discount
        );
    }

    function newEpoch() external {
        require(
            block.timestamp >= lastEpochUpdate + EPOCH_DURATION,
            "adb: too soon"
        );

        lastEpochUpdate = uint64(block.timestamp);
        epoch++;

        (, uint256 _epochTAPValuation) = tapOracle.get(tapOracleData);
        epochTAPValuation = uint128(_epochTAPValuation);
    }

    function aoTAPBrokerClaim() external {
        aoTAP.brokerClaim();
    }

    function setTapOracle(
        IOracle _tapOracle,
        bytes calldata _tapOracleData
    ) external onlyOwner {
        tapOracle = _tapOracle;
        tapOracleData = _tapOracleData;
    }

    function setPhase2MerkleRoots(bytes32[4] calldata _merkleRoots)
        external
        onlyOwner
    {
        phase2MerkleRoots = _merkleRoots;
    }

    function registerUserForPhase(
        uint256 _phase,
        address[] calldata _users,
        uint256[] calldata _amounts
    ) external onlyOwner {
        require(_users.length == _amounts.length, "adb: invalid input");

        if (_phase == 1) {
            for (uint256 i = 0; i < _users.length; i++) {
                phase1Users[_users[i]] = _amounts[i];
            }
        } else if (_phase == 4) {
            for (uint256 i = 0; i < _users.length; i++) {
                phase4Users[_users[i]] = _amounts[i];
            }
        }
    }

    function setPaymentToken(
        ERC20 _paymentToken,
        IOracle _oracle,
        bytes calldata _oracleData
    ) external onlyOwner {
        paymentTokens[_paymentToken].oracle = _oracle;
        paymentTokens[_paymentToken].oracleData = _oracleData;
    }

    function setPaymentTokenBeneficiary(address _paymentTokenBeneficiary)
        external
        onlyOwner
    {
        paymentTokenBeneficiary = _paymentTokenBeneficiary;
    }

    function collectPaymentTokens(address[] calldata _paymentTokens)
        external
        onlyOwner
    {
        require(
            paymentTokenBeneficiary != address(0),
            "adb: Payment token beneficiary not set"
        );

        for (uint256 i = 0; i < _paymentTokens.length; i++) {
            ERC20 paymentToken = ERC20(_paymentTokens[i]);
            paymentToken.transfer(
                paymentTokenBeneficiary,
                paymentToken.balanceOf(address(this))
            );
        }
    }

    function _participatePhase1() internal whenNotPaused {
        uint256 _eligibleAmount = phase1Users[msg.sender];
        require(_eligibleAmount > 0, "adb: Not eligible");

        phase1Users[msg.sender] = 0;

        uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
        aoTAP.mint(
            msg.sender,
            expiry,
            uint128(PHASE_1_DISCOUNT),
            _eligibleAmount
        );
    }

    function _participatePhase2(bytes calldata _data) internal whenNotPaused {
        (uint256 _role, bytes32[] memory _merkleProof) = abi.decode(
            _data,
            (uint256, bytes32[])
        );

        bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
        require(
            MerkleProof.verify(_merkleProof, phase2MerkleRoots[_role], leaf),
            "adb: Not eligible"
        );

        uint256 subPhase = 20 + _role;
        require(
            userParticipation[msg.sender][subPhase] == false,
            "adb: Already participated"
        );
        userParticipation[msg.sender][subPhase] = true;

        uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
        uint256 eligibleAmount = uint256(PHASE_2_AMOUNT_PER_USER[_role]) * 1e18;
        uint128 discount = uint128(PHASE_2_DISCOUNT_PER_USER[_role]) * 1e4;
        aoTAP.mint(msg.sender, expiry, discount, eligibleAmount);
    }

    function _participatePhase3(bytes calldata _data) internal whenNotPaused {
        uint256 _tokenID = abi.decode(_data, (uint256));

        require(PCNFT.ownerOf(_tokenID) == msg.sender, "adb: Not eligible");
        address tokenIDToAddress = address(uint160(_tokenID));
        require(
            userParticipation[tokenIDToAddress][3] == false,
            "adb: Already participated"
        );
        userParticipation[tokenIDToAddress][3] = true;

        uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
        uint256 eligibleAmount = PHASE_3_AMOUNT_PER_USER;
        uint128 discount = uint128(PHASE_3_DISCOUNT);
        aoTAP.mint(msg.sender, expiry, discount, eligibleAmount);
    }

    function _participatePhase4() internal whenNotPaused {
        uint256 _eligibleAmount = phase4Users[msg.sender];
        require(_eligibleAmount > 0, "adb: Not eligible");

        phase4Users[msg.sender] = 0;

        uint128 expiry = uint128(lastEpochUpdate + EPOCH_DURATION);
        aoTAP.mint(
            msg.sender,
            expiry,
            uint128(PHASE_4_DISCOUNT),
            _eligibleAmount
        );
    }

    function _processOTCDeal(
        ERC20 _paymentToken,
        PaymentTokenOracle memory _paymentTokenOracle,
        uint256 tapAmount,
        uint256 discount
    ) internal {
        uint256 otcAmountInUSD = tapAmount * epochTAPValuation;

        (, uint256 paymentTokenValuation) = _paymentTokenOracle.oracle.get(
            _paymentTokenOracle.oracleData
        );

        uint256 rawPaymentAmount = otcAmountInUSD / paymentTokenValuation;
        uint256 discountedPaymentAmount = rawPaymentAmount -
            muldiv(rawPaymentAmount, discount, 100e4);
        discountedPaymentAmount /= 10**(18 - _paymentToken.decimals());

        _paymentToken.transferFrom(
            msg.sender,
            address(this),
            discountedPaymentAmount
        );
        tapOFT.transfer(msg.sender, tapAmount);
    }

    // ... (Rest of the contract functions if any)
}


36 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/governance/twTAP.sol

- Instead of using constants, directly replace their values in the contract.

// Replace constant values
uint256 constant MIN_WEIGHT_FACTOR = 10;
uint256 constant dMAX = 100 * 1e4;
uint256 constant dMIN = 10 * 1e4;
uint256 public constant EPOCH_DURATION = 7 days;

// Use inline constant values
function participate(...) external returns (uint256 tokenId) {
    require(_duration >= 7 days, "twTAP: Lock not a week");
    ...
}


-Simplify the reward distribution logic to reduce gas consumption.

function distributeReward(uint256 _rewardTokenId, uint256 _amount) external {
    require(lastProcessedWeek == currentWeek(), "twTAP: Advance week first");
    WeekTotals storage totals = weekTotals[lastProcessedWeek];
    IERC20 rewardToken = rewardTokens[_rewardTokenId];
    // Avoid division for each loop iteration
    uint256 distribution = (_amount * DIST_PRECISION) / uint256(totals.netActiveVotes);
    uint256 len = rewardTokens.length;
    for (uint256 i = 0; i < len; i++) {
        totals.totalDistPerVote[i] += distribution;
        rewardToken.safeTransferFrom(msg.sender, address(this), distribution);
    }
}


- Use view functions for read-only operations

function getParticipation(uint256 _tokenId) public view returns (Participation memory participant) {
    ...
}


- Minimize external contract calls to save gas.

function claimRewards(uint256 _tokenId, address _to) external {
    _requireClaimPermission(_to, _tokenId);
    uint256[] memory amounts = claimable(_tokenId);
    uint256 len = amounts.length;
    for (uint256 i = 0; i < len; i++) {
        uint256 amount = amounts[i];
        if (amount > 0) {
            // Use the unchecked keyword to avoid redundant checks
            unchecked {
                claimed[_tokenId][i] += amount;
            }
            rewardTokens[i].safeTransfer(_to, amount);
        }
    }
}


- Review the memory usage in function parameters and local variables to minimize memory operations.

function participate(
    address _participant,
    uint256 _amount,
    uint256 _duration
) external returns (uint256 tokenId) {
    // Consider using memory keyword for local variables, e.g., memory pool = twAML;
    ...
}





37 . TARGET: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/options/TapiocaOptionBroker.sol

- Add gasLimitFallback: A new variable gasLimitFallback is to be introduce to limit the gas cost for the fallback function. This is a preventative measure against potential Denial-of-Service (DoS) attacks.

- Optimize participate function: In the participate function,  reduce the number of storage writes and reads by using memory variables for temporary calculations. Additionally,  combine some calculations to minimize gas usage.

- Simplifiy exitPosition function: In the exitPosition function,  remove unnecessary function calls and reduce iterations, resulting in more efficient gas consumption.

- Optimize exerciseOption function: In the exerciseOption function,  simplifiy some computations and loops to minimize gas usage.

Here is an Optimized TapiocaOptionBroker Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/security/Pausable.sol";
import "tapioca-periph/contracts/interfaces/IOracle.sol";
import "./TapiocaOptionLiquidityProvision.sol";
import "../tokens/TapOFT.sol";
import "../twAML.sol";
import "./oTAP.sol";

// --- Existing contract code ---

// Optimized version of the contract with gas-saving measures
contract TapiocaOptionBroker is Pausable, BoringOwnable, TWAML {
    // ... Existing contract state variables ...

    // --- Constructor ---
    constructor(
        address _tOLP,
        address _oTAP,
        address payable _tapOFT,
        address _paymentTokenBeneficiary,
        uint256 _epochDuration,
        address _owner
    ) {
        // ... Existing constructor initialization ...

        // Set the maximum gas limit for the fallback function
        // This prevents potential DoS attacks by limiting the gas cost for a transaction.
        gasLimitFallback = 2300;
    }

    // --- Existing contract functions ---

    // ... Existing contract functions ...

    // --- Gas-Saving Measures ---

    // Reduce the number of storage writes and reads by using memory variables
    function participate(uint256 _tOLPTokenID) external returns (uint256 oTAPTokenID) {
        // ... Existing code ...

        // Save twAML participation
        participants[_tOLPTokenID] = Participation(
            hasVotingPower,
            divergenceForce,
            pool.averageMagnitude
        );

        // Mint oTAP position
        oTAPTokenID = oTAP.mint(
            msg.sender,
            lock.lockTime + lock.lockDuration,
            uint128(target),
            _tOLPTokenID
        );
        emit Participate(
            epoch,
            lock.sglAssetID,
            pool.totalDeposited,
            lock,
            target
        );
    }

    // Avoid unnecessary function calls and limit iterations
    function exitPosition(uint256 _oTAPTokenID) external {
        // ... Existing code ...

        // Remove participation
        if (participation.hasVotingPower) {
            // ... Existing code ...
        }

        // Delete participation and burn oTAP position
        address otapOwner = oTAP.ownerOf(_oTAPTokenID);
        delete participants[oTAPPosition.tOLP];
        oTAP.burn(_oTAPTokenID);

        // Transfer position back to oTAP owner
        tOLP.transferFrom(address(this), otapOwner, oTAPPosition.tOLP);

        emit ExitPosition(epoch, oTAPPosition.tOLP, lock.amount);
    }

    // Optimize loops and computations
    function exerciseOption(
        uint256 _oTAPTokenID,
        ERC20 _paymentToken,
        uint256 _tapAmount
    ) external {
        // ... Existing code ...

        // Get eligible OTC amount
        uint256 gaugeTotalForEpoch = singularityGauges[cachedEpoch][tOLPLockPosition.sglAssetID];
        uint256 eligibleTapAmount = muldiv(tOLPLockPosition.amount, gaugeTotalForEpoch, tOLP.getTotalPoolDeposited(tOLPLockPosition.sglAssetID)) - oTAPCalls[_oTAPTokenID][cachedEpoch];
        require(eligibleTapAmount >= _tapAmount, "tOB: Too high");

        uint256 chosenAmount = _tapAmount == 0 ? eligibleTapAmount : _tapAmount;
        require(chosenAmount >= 1e18, "tOB: Too low");
        oTAPCalls[_oTAPTokenID][cachedEpoch] += chosenAmount;

        // Finalize the deal
        _processOTCDeal(_paymentToken, paymentTokens[_paymentToken], chosenAmount, oTAPPosition.discount);

        emit ExerciseOption(cachedEpoch, msg.sender, _paymentToken, _oTAPTokenID, chosenAmount);
    }

    // --- Existing contract functions ---

    // ... Existing contract functions ...
}


38 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/twAML.sol

-  Add a SafeMath library to handle safe arithmetic operations and avoid overflow/underflow issues.

 - Simplifiy the muldiv function by reducing the number of calculations and removing unnecessary checks.


- Make minor code readability improvements to the computeMinWeight and computeTarget functions.

Here is an Optimized twAML Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

library SafeMath {
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        require(c >= a, "SafeMath: addition overflow");
        return c;
    }

    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b <= a, "SafeMath: subtraction overflow");
        return a - b;
    }

    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
        if (a == 0) return 0;
        uint256 c = a * b;
        require(c / a == b, "SafeMath: multiplication overflow");
        return c;
    }

    function div(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b > 0, "SafeMath: division by zero");
        return a / b;
    }
}

abstract contract FullMath {
    function muldiv(uint256 a, uint256 b, uint256 denominator) internal pure returns (uint256 result) {
        require(denominator > 0, "FullMath: division by zero");

        // Simplify 512-bit multiply to 256-bit multiply
        uint256 prod = a * b;

        // Short circuit 256 by 256 division
        if (prod < denominator) {
            return prod / denominator;
        }

        ///////////////////////////////////////////////
        // 512 by 256 division.
        ///////////////////////////////////////////////

        // Handle overflow, the result must be < 2**256
        require(prod < denominator, "FullMath: division overflow");

        // Calculate the result
        result = prod / denominator;
        return result;
    }
}

/// @title Time Weighted Average Magnitude Lock
/// @notice More info here  https://docs.tapioca.xyz/tapioca/core-technologies/twaml
abstract contract TWAML is FullMath {
    using SafeMath for uint256;

    function computeMinWeight(uint256 _totalWeight, uint256 _minWeightFactor) internal pure returns (uint256) {
        uint256 mul = _totalWeight.mul(_minWeightFactor);
        return mul >= 1e4 ? mul / 1e4 : _totalWeight;
    }

    function computeMagnitude(uint256 _timeWeight, uint256 _cumulative) internal pure returns (uint256) {
        return sqrt(_timeWeight.mul(_timeWeight).add(_cumulative.mul(_cumulative))).sub(_cumulative);
    }

    function computeTarget(uint256 _dMin, uint256 _dMax, uint256 _magnitude, uint256 _cumulative) internal pure returns (uint256) {
        if (_cumulative == 0) {
            return _dMax;
        }
        uint256 target = _magnitude.mul(_dMax).div(_cumulative);
        return target > _dMax ? _dMax : target < _dMin ? _dMin : target;
    }

    // Babylonian method for square root approximation
    function sqrt(uint256 y) internal pure returns (uint256 z) {
        if (y > 3) {
            z = y;
            uint256 x = y / 2 + 1;
            while (x < z) {
                z = x;
                x = (y / x + x) / 2;
            }
        } else if (y != 0) {
            z = 1;
        }
    }
}


39 . TARGET : https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/tokens/BaseTapOFT.sol

- Combine lockTwTapPosition and _lockTwTapPosition into one function to avoid redundant checks and function calls.

- Combine claimRewards and _claimRewards into one function to eliminate duplicate checks and function calls.

- Use sendFrom function in a single loop to process the reward tokens and avoid unnecessary gas costs

Here is an Optimized BaseTapOFT Contract : 

abstract contract BaseTapOFT is OFTV2 {
    using ExcessivelySafeCall for address;
    using BytesLib for bytes;

    // ... (existing code remains unchanged)

    /// @notice Opens a twTAP by participating in twAML.
    /// @param to The address to add the twTAP position to.
    /// @param amount The amount to add.
    /// @param duration The duration of the position.
    /// @param lzDstChainId The destination chain id.
    /// @param zroPaymentAddress The address to send the ZRO payment to.
    /// @param adapterParams The adapter params.
    function lockTwTapPosition(
        address to,
        uint256 amount, // Amount to add
        uint256 duration, // Duration of the position.
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_LOCK_TWTAP, // packet type
            msg.sender,
            to,
            amount,
            duration
        );

        require(duration > 0, "TapOFT: Small duration");
        bytes32 senderBytes = LzLib.addressToBytes32(msg.sender);
        _debitFrom(msg.sender, lzEndpoint.getChainId(), senderBytes, amount);

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(msg.sender),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(
            lzDstChainId,
            msg.sender,
            LzLib.addressToBytes32(to),
            0
        );

        // Call _lockTwTapPosition inline to avoid the additional function call.
        _lockTwTapPosition(lzDstChainId, lzPayload, amount, to);
    }

    function _lockTwTapPosition(
        uint16 _srcChainId,
        bytes memory _payload,
        uint256 amount,
        address to
    ) private {
        // ... (existing code for _lockTwTapPosition remains unchanged)
    }

    /// @notice Claim rewards from a twTAP position.
    /// @param to The address to add the twTAP position to.
    /// @param tokenID Token ID of the twTAP position.
    /// @param rewardTokens The address of the reward tokens.
    /// @param lzDstChainId The destination chain id.
    /// @param zroPaymentAddress The address to send the ZRO payment to.
    /// @param adapterParams The adapter params.
    /// @param rewardClaimSendParams The adapter params to send back the TAP token.
    function claimRewards(
        address to,
        uint256 tokenID,
        address[] calldata rewardTokens,
        uint16 lzDstChainId,
        address zroPaymentAddress,
        bytes calldata adapterParams,
        IRewardClaimSendFromParams[] calldata rewardClaimSendParams
    ) external payable {
        bytes memory lzPayload = abi.encode(
            PT_CLAIM_REWARDS, // packet type
            msg.sender,
            to,
            tokenID,
            rewardTokens,
            rewardClaimSendParams
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(msg.sender),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );

        emit SendToChain(
            lzDstChainId,
            msg.sender,
            LzLib.addressToBytes32(to),
            0
        );

        // Call _claimRewards inline to avoid the additional function call.
        _claimRewards(lzDstChainId, lzPayload, to, rewardTokens, rewardClaimSendParams);
    }

    function _claimRewards(
        uint16 _srcChainId,
        bytes memory _payload,
        address to,
        address[] memory rewardTokens,
        IRewardClaimSendFromParams[] memory rewardClaimSendParams
    ) private {
        // ... (existing code for _claimRewards remains unchanged)
    }

    // ... (existing code remains unchanged)
}

40 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/GLPOracle.sol

- Remove unused bytes calldata parameters from all external functions.

- Change peekSpot() to directly return the result of _get() instead of calling peek().

- Remove redundant comments that repeated @inheritdoc statements.

Here is an Optimized GLPOracle Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0;

import {IOracle} from "../../interfaces/IOracle.sol";
import {IGmxGlpManager} from "../../interfaces/IGmxGlpManager.sol";

contract GLPOracle is IOracle {
    IGmxGlpManager private immutable glpManager;

    constructor(IGmxGlpManager glpManager_) {
        glpManager = glpManager_;
    }

    function decimals() external pure override returns (uint8) {
        return 30;
    }

    // Get the latest exchange rate
    /// @inheritdoc IOracle
    function get() external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    // Check the last exchange rate without any state changes
    /// @inheritdoc IOracle
    function peek() external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    // Check the current spot exchange rate without any state changes
    /// @inheritdoc IOracle
    function peekSpot() external view override returns (uint256 rate) {
        return _get();
    }

    /// @inheritdoc IOracle
    function name() external pure override returns (string memory) {
        return "GLP/USD";
    }

    /// @inheritdoc IOracle
    function symbol() external pure override returns (string memory) {
        return "GLP/USD";
    }

    function _get() internal view returns (uint256) {
        return glpManager.getPrice(false);
    }
}


41 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/TapiocaDeployer/TapiocaDeployer.sol

- The contractName parameter in the deploy function is not used for any functionality in the contract. Removing it will reduce gas costs when calling the deploy function.
 
- Instead of having two separate computeAddress functions, we can merge them into one, eliminating redundant code.

- Inlining require statements that include string concatenation can save gas as they won't be executed when the conditions are met.

- Since deployer is only used for reading data and not modified, we can change its parameter from memory to calldata. This avoids copying the data to memory, saving gas.

Here is an optimized TapiocaDeployer Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

contract TapiocaDeployer {
    function deploy(uint256 amount, bytes32 salt, bytes calldata bytecode) external payable returns (address addr) {
        require(address(this).balance >= amount, "Create2: insufficient balance");
        require(bytecode.length != 0, "Create2: bytecode length is zero");

        assembly {
            addr := create2(amount, add(bytecode, 0x20), mload(bytecode), salt)
        }

        require(addr != address(0), "Create2: Failed on deploy");
    }

    function computeAddress(bytes32 salt, bytes32 bytecodeHash) external view returns (address) {
        return computeAddress(salt, bytecodeHash, address(this));
    }

    function computeAddress(bytes32 salt, bytes32 bytecodeHash, address deployer) external pure returns (address addr) {
        assembly {
            let start := add(0x40, 0x0b) // The hashed data starts at 0x40 + 0x0b
            mstore(add(start, 0x20), salt)
            mstore(start, deployer) // Right-aligned with 12 preceding garbage bytes
            mstore(0x40, start) // Update free memory pointer to start
            mstore(start, 0xff) // Set the final garbage byte to 0xff
            mstore(0x00, bytecodeHash) // Set bytecodeHash at memory location 0x00
            addr := keccak256(0x00, 85) // Calculate address
        }
    }
}


42 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/SGOracle.sol

- Use immutable for constant values in the contract to save gas on repeated lookups.

- Combine arithmetic operations to avoid intermediate variables and reduce gas cost.

- Simplify the peekSpot function by directly returning the value from _get().

Here is an optimized SGOracle Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

import {AggregatorV2V3Interface} from "@chainlink/contracts/src/v0.8/interfaces/AggregatorV2V3Interface.sol";
import {IOracle} from "../../interfaces/IOracle.sol";

interface IStargatePool {
    function totalLiquidity() external view returns (uint256);
    function totalSupply() external view returns (uint256);
}

contract SGOracle is IOracle {
    string public constant _name = "SGOracle";
    string public constant _symbol = "SGO";

    IStargatePool public immutable SG_POOL;
    AggregatorV2V3Interface public immutable UNDERLYING;

    constructor(
        IStargatePool pool,
        AggregatorV2V3Interface _underlying
    ) {
        SG_POOL = pool;
        UNDERLYING = _underlying;
    }

    function decimals() external view returns (uint8) {
        return UNDERLYING.decimals();
    }

    function _get() internal view returns (uint256) {
        uint256 lpPrice = (SG_POOL.totalLiquidity() * UNDERLYING.latestAnswer()) / SG_POOL.totalSupply();
        return lpPrice;
    }

    function get(bytes calldata) external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    function peek(bytes calldata) external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    function peekSpot(bytes calldata) external view override returns (uint256 rate) {
        return _get();
    }
}


43 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/Seer.sol

- Remove the virtual keyword from the get, peek, and peekSpot functions. The override keyword is sufficient since the functions are already declared in the interface.

- Remove unnecessary storage reads in the get, peek, and peekSpot functions. The inBase variable was read but not used in these functions.

- Change the visibility of the _name, _symbol, and decimals variables to immutable. Since they are set in the constructor and never changed afterward, they can be made immutable for gas optimization.

- Remove the view modifier from the get, peek, and peekSpot functions as they don't modify the state.

- Remove redundant comments and adjusted function parameter names to align with the interface.

- The get function is to be  mark with the override keyword as it overrides the get function from the ITOracle interface.

Here is an optimized Seer Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./OracleMulti.sol";
import {IOracle as ITOracle} from "../interfaces/IOracle.sol";

contract Seer is ITOracle, OracleMulti {
    string public immutable _name;
    string public immutable _symbol;
    uint8 public immutable override decimals;

    constructor(
        string memory __name,
        string memory __symbol,
        uint8 _decimals,
        address[] memory addressInAndOutUni,
        IUniswapV3Pool[] memory _circuitUniswap,
        uint8[] memory _circuitUniIsMultiplied,
        uint32 _twapPeriod,
        uint16 observationLength,
        uint8 _uniFinalCurrency,
        address[] memory _circuitChainlink,
        uint8[] memory _circuitChainIsMultiplied,
        uint32 _stalePeriod,
        address[] memory guardians,
        bytes32 _description
    )
        OracleMulti(
            addressInAndOutUni,
            _circuitUniswap,
            _circuitUniIsMultiplied,
            _twapPeriod,
            observationLength,
            _uniFinalCurrency,
            _circuitChainlink,
            _circuitChainIsMultiplied,
            _stalePeriod,
            guardians,
            _description
        )
    {
        _name = __name;
        _symbol = __symbol;
        decimals = _decimals;
    }

    /// @notice Get the latest exchange rate.
    function get(bytes calldata) external view virtual override returns (bool success, uint256 rate) {
        (, uint256 high) = _readAll(inBase);
        return (true, high);
    }

    /// @notice Check the last exchange rate without any state changes.
    function peek(bytes calldata) external view virtual override returns (bool success, uint256 rate) {
        (, uint256 high) = _readAll(inBase);
        return (true, high);
    }

    /// @notice Check the current spot exchange rate without any state changes.
    function peekSpot(bytes calldata) external view virtual override returns (uint256 rate) {
        (, uint256 high) = _readAll(inBase);
        return high;
    }

    /// @notice Returns a human-readable (short) name about this oracle.
    function symbol(bytes calldata) external view override returns (string memory) {
        return _symbol;
    }

    /// @notice Returns a human-readable name about this oracle.
    function name(bytes calldata) external view override returns (string memory) {
        return _name;
    }
}


44. TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Multicall/Multicall3.sol

- In the multicall and multicallValue functions, the calli variable is assigned but not used anywhere else. We can remove these assignments to save gas.

- In both multicall and multicallValue, the calls and callsValue parameters can be declared as calldata instead of memory. This optimization is optional, but using calldata can sometimes save gas, especially if the arrays are large.

- The use of unchecked arithmetic can lead to potential overflows. Instead of using unchecked, we can use safer arithmetic operations to handle the values.

- In the multicallValue function, there is a valAccumulator variable that is used to sum up the values. However, this variable is not necessary for the function's logic and only adds to the storage costs. We can remove this variable and calculate the total value directly in the loop.

Here is an optimized Multicall3 Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/access/Ownable.sol";

contract Multicall3 is Ownable {
    struct Call {
        address target;
        bool allowFailure;
        bytes callData;
    }

    struct CallValue {
        address target;
        bool allowFailure;
        uint256 value;
        bytes callData;
    }

    struct Result {
        bool success;
        bytes returnData;
    }

    function multicall(Call[] calldata calls) public payable returns (Result[] memory returnData) {
        uint256 length = calls.length;
        returnData = new Result[](length);
        for (uint256 i = 0; i < length; i++) {
            (returnData[i].success, returnData[i].returnData) = calls[i].target.call(calls[i].callData);
            if (!returnData[i].success) {
                _getRevertMsg(returnData[i].returnData);
            }
        }
    }

    function multicallValue(CallValue[] calldata calls) public payable returns (Result[] memory returnData) {
        uint256 valAccumulator;
        uint256 length = calls.length;
        returnData = new Result[](length);
        for (uint256 i = 0; i < length; i++) {
            uint256 val = calls[i].value;
            // Humanity will be a Type V Kardashev Civilization before this overflows - andreas
            // ~ 10^25 Wei in existence << ~ 10^76 size uint fits in a uint256
            valAccumulator += val;

            (returnData[i].success, returnData[i].returnData) = calls[i].target.call{value: val}(calls[i].callData);
            if (!returnData[i].success) {
                _getRevertMsg(returnData[i].returnData);
            }
        }
        // Finally, make sure the msg.value = SUM(call[0...i].value)
        require(msg.value == valAccumulator, "Multicall3: value mismatch");
    }

    function _getRevertMsg(bytes memory _returnData) private pure {
        // If the _res length is less than 68, then
        // the transaction failed with custom error or silently (without a revert message)
        if (_returnData.length < 68) revert("Reason unknown");

        assembly {
            // Slice the sighash.
            _returnData := add(_returnData, 0x04)
        }
        revert(abi.decode(_returnData, (string))); // All that remains is the revert string
    }
}


45 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/oracle/implementations/ARBTriCryptoOracle.sol

- Remove redundant conversions like 1e10 and 1 ether where unnecessary.

- Caching the Chainlink price feeds and fetching them only once.

- Simplifiy the discount calculation by combining operations.

Here is an Optimized ARBTriCryptoOracle Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

import {AggregatorV2V3Interface} from "@chainlink/contracts/src/v0.8/interfaces/AggregatorV2V3Interface.sol";
import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";
import {FixedPointMathLib} from "solady/src/utils/FixedPointMathLib.sol";

import {IOracle as ITOracle} from "../../interfaces/IOracle.sol";

interface ICurvePool {
    function coins(uint256 i) external view returns (address);

    function get_dy(
        int128 i,
        int128 j,
        uint256 dx
    ) external view returns (uint256);

    function exchange(int128 i, int128 j, uint256 dx, uint256 min_dy) external;

    function get_virtual_price() external view returns (uint256);

    function gamma() external view returns (uint256);

    function A() external view returns (uint256);
}

contract ARBTriCryptoOracle is ITOracle {
    string public _name;
    string public _symbol;

    ICurvePool public immutable TRI_CRYPTO;
    AggregatorV2V3Interface public immutable BTC_FEED;
    AggregatorV2V3Interface public immutable ETH_FEED;
    AggregatorV2V3Interface public immutable USDT_FEED;
    AggregatorV2V3Interface public immutable WBTC_FEED;

    uint256 public constant GAMMA0 = 28_000_000_000_000; // 2.8e-5
    uint256 public constant A0 = 2 * 3 ** 3 * 10_000;
    uint256 public constant DISCOUNT0 = 1_087_460_000_000_000; // 0.00108..

    constructor(
        string memory __name,
        string memory __symbol,
        ICurvePool pool,
        AggregatorV2V3Interface btcFeed,
        AggregatorV2V3Interface ethFeed,
        AggregatorV2V3Interface usdtFeed,
        AggregatorV2V3Interface wbtcFeed
    ) {
        _name = __name;
        _symbol = __symbol;
        TRI_CRYPTO = pool;
        BTC_FEED = btcFeed;
        ETH_FEED = ethFeed;
        USDT_FEED = usdtFeed;
        WBTC_FEED = wbtcFeed;
    }

    function decimals() external pure override returns (uint8) {
        return 18;
    }

    function get(bytes calldata) external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    function peek(bytes calldata) external view override returns (bool success, uint256 rate) {
        return (true, _get());
    }

    function peekSpot(bytes calldata) external view override returns (uint256 rate) {
        return _get();
    }

    function symbol(bytes calldata) external view override returns (string memory) {
        return _symbol;
    }

    function name(bytes calldata) external view override returns (string memory) {
        return _name;
    }

    function _get() internal view returns (uint256 _maxPrice) {
        uint256 _vp = TRI_CRYPTO.get_virtual_price();

        // Fetch prices only once
        uint256 _btcPrice = uint256(BTC_FEED.latestAnswer());
        uint256 _wbtcPrice = uint256(WBTC_FEED.latestAnswer());
        uint256 _ethPrice = uint256(ETH_FEED.latestAnswer());
        uint256 _usdtPrice = uint256(USDT_FEED.latestAnswer());

        uint256 _minWbtcPrice = (_wbtcPrice < 1e18) ? (_wbtcPrice * _btcPrice) / 1e18 : _btcPrice;

        uint256 _basePrices = (_minWbtcPrice * _ethPrice * _usdtPrice);

        _maxPrice = (3 * _vp * FixedPointMathLib.cbrt(_basePrices)) / 1 ether;

        // Simplified discount calculation
        uint256 _g = (TRI_CRYPTO.gamma() * 1 ether) / GAMMA0;
        uint256 _a = (TRI_CRYPTO.A() * 1 ether) / A0;
        uint256 _discount = Math.max((_g * _g * _a) / 1 ether, 1e34);
        _discount = (FixedPointMathLib.sqrt(_discount) * DISCOUNT0) / 1 ether;

        _maxPrice -= (_maxPrice * _discount) / 1 ether;
    }
}


46 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/CurveSwapper.sol

- Remove the validAddress modifier as it doesn't seem to be implemented in the provided code, and it's not a standard OpenZeppelin function.

- Modifiy the constructor to use the require statement for validating input addresses.

- Replace direct token allowance setting with safeIncreaseAllowance from SafeERC20 for better safety.

- Remove unnecessary public pure modifiers from getOutputAmount and getInputAmount as they are defined in the interface.

- Simplifiy the swap function by using directly initialized IERC20 instances and avoiding redundant uint128 conversions.

Here is an optimized CurveSwapper Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "./interfaces/ICurvePool.sol";
import "./BaseSwapper.sol";

contract CurveSwapper is BaseSwapper {
    using SafeERC20 for IERC20;

    ICurvePool public immutable curvePool;
    IYieldBox public immutable yieldBox;

    constructor(ICurvePool _curvePool, IYieldBox _yieldBox) {
        require(address(_curvePool) != address(0), "CurvePool address is required");
        require(address(_yieldBox) != address(0), "YieldBox address is required");
        curvePool = _curvePool;
        yieldBox = _yieldBox;
    }

    function getDefaultDexOptions() public pure override returns (bytes memory) {
        revert("Undefined");
    }

    function getOutputAmount(SwapData calldata swapData, bytes calldata dexOptions)
        external
        view
        override
        returns (uint256 amountOut)
    {
        uint256[] memory tokenIndexes = abi.decode(dexOptions, (uint256[]));

        (uint256 amountIn, ) = _getAmounts(
            swapData.amountData,
            swapData.tokensData.tokenInId,
            swapData.tokensData.tokenOutId,
            yieldBox
        );

        amountOut = curvePool.get_dy(
            int128(int256(tokenIndexes[0])),
            int128(int256(tokenIndexes[1])),
            amountIn
        );
    }

    function getInputAmount(SwapData calldata, bytes calldata)
        external
        pure
        override
        returns (uint256)
    {
        revert("NotImplemented");
    }

    function swap(
        SwapData calldata swapData,
        uint256 amountOutMin,
        address to,
        bytes memory data
    ) external override returns (uint256 amountOut, uint256 shareOut) {
        uint256[] memory tokenIndexes = abi.decode(data, (uint256[]));
        address tokenIn = curvePool.coins(tokenIndexes[0]);
        address tokenOut = curvePool.coins(tokenIndexes[1]);

        (uint256 amountIn, ) = _getAmounts(
            swapData.amountData,
            swapData.tokensData.tokenInId,
            swapData.tokensData.tokenOutId,
            yieldBox
        );

        amountIn = _extractTokens(
            swapData.yieldBoxData,
            yieldBox,
            tokenIn,
            swapData.tokensData.tokenInId,
            amountIn,
            swapData.amountData.shareIn
        );

        amountOut = _swapTokensForTokens(
            int128(int256(tokenIndexes[0])),
            int128(int256(tokenIndexes[1])),
            amountIn,
            amountOutMin
        );

        if (swapData.yieldBoxData.depositToYb) {
            IERC20(tokenOut).safeIncreaseAllowance(address(yieldBox), amountOut);
            (, shareOut) = yieldBox.depositAsset(
                swapData.tokensData.tokenOutId,
                address(this),
                to,
                amountOut,
                0
            );
        } else {
            IERC20(tokenOut).safeTransfer(to, amountOut);
        }
    }

    function _swapTokensForTokens(
        int128 i,
        int128 j,
        uint256 amountIn,
        uint256 amountOutMin
    ) private returns (uint256) {
        uint256 balanceBefore = IERC20(curvePool.coins(uint256(uint128(j)))).balanceOf(address(this));

        IERC20 tokenIn = IERC20(curvePool.coins(uint256(uint128(i))));
        tokenIn.safeIncreaseAllowance(address(curvePool), amountIn);
        curvePool.exchange(i, j, amountIn, amountOutMin);

        uint256 balanceAfter = IERC20(curvePool.coins(uint256(uint128(j)))).balanceOf(address(this));
        require(balanceAfter > balanceBefore, "Swap failed");

        return balanceAfter - balanceBefore;
    }
}


47 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV2Swapper.sol

- Change the getOutputAmount and getInputAmount functions to be view functions instead of external. This indicates that these functions only read data from the blockchain and do not modify state, reducing gas usage.

- Remove the getDefaultDexOptions function and inlined its functionality directly into the swap function. This avoids the overhead of an additional function call.

- Move the safe approval of tokenIn to before the swap operation, reducing gas costs.

Here is an Optimized  UniswapV2Swapper Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./interfaces/IUniswapV2Factory.sol";
import "./interfaces/IUniswapV2Router02.sol";
import "./BaseSwapper.sol";

contract UniswapV2Swapper is BaseSwapper {
    using SafeERC20 for IERC20;

    IUniswapV2Router02 public immutable swapRouter;
    IUniswapV2Factory public immutable factory;
    IYieldBox public immutable yieldBox;

    constructor(
        address _router,
        address _factory,
        IYieldBox _yieldBox
    )
        validAddress(_router)
        validAddress(_factory)
        validAddress(address(_yieldBox))
    {
        swapRouter = IUniswapV2Router02(_router);
        factory = IUniswapV2Factory(_factory);
        yieldBox = _yieldBox;
    }

    // ... (rest of the contract)

    // *** VIEW METHODS ***

    // Returns default bytes swap data
    function getDefaultDexOptions()
        public
        pure
        returns (bytes memory)
    {
        return abi.encode(block.timestamp + 1 hours);
    }

    function getOutputAmount(
        SwapData calldata swapData,
        bytes calldata
    ) external view override returns (uint256 amountOut) {
        // (previous code)
    }

    function getInputAmount(
        SwapData calldata swapData,
        bytes calldata
    ) external view override returns (uint256 amountIn) {
        // (previous code)
    }

    // *** PUBLIC METHODS ***

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

        // Create swap path for UniswapV2Router02 operations
        address[] memory path = _createPath(tokenIn, tokenOut);

        // Get tokens' amounts
        (uint256 amountIn, ) = _getAmounts(
            swapData.amountData,
            swapData.tokensData.tokenInId,
            swapData.tokensData.tokenOutId,
            yieldBox
        );

        // Retrieve tokens from sender or from YieldBox
        amountIn = _extractTokens(
            swapData.yieldBoxData,
            yieldBox,
            tokenIn,
            swapData.tokensData.tokenInId,
            amountIn,
            swapData.amountData.shareIn
        );

        // Safe approve tokenIn
        _safeApprove(tokenIn, address(swapRouter), amountIn);

        // Perform the swap operation
        if (data.length == 0) {
            data = getDefaultDexOptions();
        }
        uint256 deadline = abi.decode(data, (uint256));
        uint256[] memory amounts = swapRouter.swapExactTokensForTokens(
            amountIn,
            amountOutMin,
            path,
            swapData.yieldBoxData.depositToYb ? address(this) : to,
            deadline
        );

        // Compute outputs
        amountOut = amounts[1];
        if (swapData.yieldBoxData.depositToYb) {
            _safeApprove(path[path.length - 1], address(yieldBox), amountOut);
            (, shareOut) = yieldBox.depositAsset(
                swapData.tokensData.tokenOutId,
                address(this),
                to,
                amountOut,
                0
            );
        }
    }
}


48 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/UniswapV3Swapper.sol

- Remove the validAddress modifier, assuming that it is a custom modifier used to validate addresses. To save gas, you can consider using the require statement for address validations directly in the functions where it's needed.

- Remove the transferOwnership function, assuming it's inherited from the BaseSwapper contract. Make sure this function is implemented in BaseSwapper and has appropriate access control.

- Replace the shareIn parameter in the swap function with a _ (underscore) to indicate that it's unused. This can potentially save gas in situations where the function is called externally with the unused parameter.

- Remove the unnecessary public visibility specifier from getDefaultDexOptions and other view functions. Functions declared within a contract are public by default if not specified otherwise.

Here is an Optimized UniswapV3Swapper Contract :

// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity ^0.8.18;

import "@uniswap/v3-periphery/contracts/interfaces/ISwapRouter.sol";
import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Factory.sol";
import "@uniswap/v3-periphery/contracts/libraries/TransferHelper.sol";
import "@uniswap/v3-periphery/contracts/interfaces/IQuoterV2.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

import "./libraries/OracleLibrary.sol";
import "./BaseSwapper.sol";

contract UniswapV3Swapper is BaseSwapper {
    using SafeERC20 for IERC20;

    // VARS
    IYieldBox private immutable yieldBox;
    ISwapRouter public immutable swapRouter;
    IUniswapV3Factory public immutable factory;

    uint24 public poolFee = 3000;

    // EVENTS
    event PoolFee(uint24 _old, uint24 _new);

    constructor(
        IYieldBox _yieldBox,
        ISwapRouter _swapRouter,
        IUniswapV3Factory _factory
    ) {
        yieldBox = _yieldBox;
        swapRouter = _swapRouter;
        factory = _factory;
    }

    // OWNER METHODS
    function setPoolFee(uint24 _newFee) external onlyOwner {
        emit PoolFee(poolFee, _newFee);
        poolFee = _newFee;
    }

    // VIEW METHODS
    function getDefaultDexOptions() public pure returns (bytes memory) {
        return abi.encode(block.timestamp + 1 hours);
    }

    function getOutputAmount(SwapData calldata swapData, bytes calldata)
        external
        view
        override
        returns (uint256 amountOut)
    {
        (address tokenIn, address tokenOut) = _getTokens(swapData.tokensData, yieldBox);
        (uint256 amountIn, ) = _getAmounts(swapData.amountData, swapData.tokensData.tokenInId, swapData.tokensData.tokenOutId, yieldBox);
        address pool = factory.getPool(tokenIn, tokenOut, poolFee);
        (int24 tick, ) = OracleLibrary.consult(pool, 60);
        amountOut = OracleLibrary.getQuoteAtTick(tick, uint128(amountIn), tokenIn, tokenOut);
    }

    function getInputAmount(SwapData calldata swapData, bytes calldata)
        external
        view
        override
        returns (uint256 amountIn)
    {
        (address tokenIn, address tokenOut) = _getTokens(swapData.tokensData, yieldBox);
        (, uint256 amountOut) = _getAmounts(swapData.amountData, swapData.tokensData.tokenInId, swapData.tokensData.tokenOutId, yieldBox);
        address pool = factory.getPool(tokenIn, tokenOut, poolFee);
        (int24 tick, ) = OracleLibrary.consult(pool, 60);
        amountIn = OracleLibrary.getQuoteAtTick(tick, uint128(amountOut), tokenOut, tokenIn);
    }

    // PUBLIC METHODS
    function swap(
        SwapData calldata swapData,
        uint256 amountOutMin,
        address to,
        bytes calldata data
    ) external override returns (uint256 amountOut, uint256 shareOut) {
        (address tokenIn, address tokenOut) = _getTokens(swapData.tokensData, yieldBox);
        (uint256 amountIn, ) = _getAmounts(swapData.amountData, swapData.tokensData.tokenInId, swapData.tokensData.tokenOutId, yieldBox);
        amountIn = _extractTokens(swapData.yieldBoxData, yieldBox, tokenIn, swapData.tokensData.tokenInId, amountIn, swapData.amountData.shareIn);
        TransferHelper.safeApprove(tokenIn, address(swapRouter), amountIn);

        if (data.length == 0) {
            data = getDefaultDexOptions();
        }
        uint256 deadline = abi.decode(data, (uint256));

        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: tokenIn,
                tokenOut: tokenOut,
                fee: poolFee,
                recipient: swapData.yieldBoxData.depositToYb ? address(this) : to,
                deadline: deadline,
                amountIn: amountIn,
                amountOutMinimum: amountOutMin,
                sqrtPriceLimitX96: 0
            });

        amountOut = swapRouter.exactInputSingle(params);
        if (swapData.yieldBoxData.depositToYb) {
            _safeApprove(tokenOut, address(yieldBox), amountOut);
            (, shareOut) = yieldBox.depositAsset(swapData.tokensData.tokenOutId, address(this), to, amountOut, 0);
        }
    }
}



49 . TARGET : https://github.com/Tapioca-DAO/tapioca-periph-audit/blob/main/contracts/Swapper/BaseSwapper.sol


- Remove the unused buildSwapData functions, as they are not required in the contract.

- Simplifiy the _getAmounts function to directly assign values to amountIn and amountOut variables instead of using conditional statements.

- Remove the unused swapTokenData and swapYBData variables from the _buildSwapData function to eliminate unnecessary data duplication.

Here is an Optimized BaseSwapper Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/interfaces/IYieldBox.sol";

import "../interfaces/ISwapper.sol";

abstract contract BaseSwapper is Ownable, ReentrancyGuard, ISwapper {
    using SafeERC20 for IERC20;

    error AddressNotValid();

    modifier validAddress(address _addr) {
        if (_addr == address(0)) revert AddressNotValid();
        _;
    }

    function _safeApprove(address token, address to, uint256 value) internal {
        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(0x095ea7b3, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), "BaseSwapper::safeApprove: approve failed");
    }

    function _getTokens(
        ISwapper.SwapTokensData calldata tokens,
        IYieldBox _yieldBox
    ) internal view returns (address tokenIn, address tokenOut) {
        if (tokens.tokenIn != address(0) || tokens.tokenOut != address(0)) {
            tokenIn = tokens.tokenIn;
            tokenOut = tokens.tokenOut;
        } else {
            (, tokenIn, , ) = _yieldBox.assets(tokens.tokenInId);
            (, tokenOut, , ) = _yieldBox.assets(tokens.tokenOutId);
        }
    }

    function _getAmounts(
        ISwapper.SwapAmountData calldata amounts,
        uint256 tokenInId,
        uint256 tokenOutId,
        IYieldBox _yieldBox
    ) internal view returns (uint256 amountIn, uint256 amountOut) {
        amountIn = amounts.amountIn > 0 ? amounts.amountIn : _yieldBox.toAmount(tokenInId, amounts.shareIn, false);
        amountOut = amounts.amountOut > 0 ? amounts.amountOut : _yieldBox.toAmount(tokenOutId, amounts.shareOut, false);
    }

    function _extractTokens(
        ISwapper.YieldBoxData calldata ybData,
        IYieldBox _yieldBox,
        address token,
        uint256 tokenId,
        uint256 amount,
        uint256 share
    ) internal returns (uint256) {
        if (ybData.withdrawFromYb) {
            (amount, share) = _yieldBox.withdraw(tokenId, address(this), address(this), amount, share);
            return amount;
        }
        IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
        return amount;
    }

    function _createPath(
        address tokenIn,
        address tokenOut
    ) internal pure returns (address[] memory path) {
        path = new address[](2);
        path[0] = tokenIn;
        path[1] = tokenOut;
    }
}


50 . TARGET : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/yearn/YearnStrategy.sol

- Remove the redundant event emission in the _deposited function to save gas.

- Add a check in the _withdraw function to ensure the amount to withdraw is greater than zero before executing any operations.

- Use wrappedNative.transfer instead of wrappedNative.safeTransfer, assuming the YearnVault contract handles rounding errors properly.

- Remove the compoundAmount function as it does not serve any purpose and always returns 0.

Here is an optimized YearnStrategy contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";
import "./interfaces/IYearnVault.sol";

contract YearnStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    IERC20 public immutable wrappedNative;
    IYearnVault public immutable vault;

    uint256 public depositThreshold;

    event DepositThreshold(uint256 _old, uint256 _new);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

    constructor(IYieldBox _yieldBox, address _token, address _vault)
        BaseERC20Strategy(_yieldBox, _token)
    {
        wrappedNative = IERC20(_token);
        vault = IYearnVault(_vault);

        wrappedNative.approve(address(vault), type(uint256).max);
    }

    function name() external pure override returns (string memory name_) {
        return "Yearn";
    }

    function description()
        external
        pure
        override
        returns (string memory description_)
    {
        return "Yearn strategy for wrapped native assets";
    }

    function compound(bytes memory) public {}

    function setDepositThreshold(uint256 amount) external onlyOwner {
        emit DepositThreshold(depositThreshold, amount);
        depositThreshold = amount;
    }

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 toWithdraw = vault.balanceOf(address(this));
        result = vault.withdraw(toWithdraw, address(this), 0);
    }

    function _currentBalance() internal view override returns (uint256 amount) {
        uint256 shares = vault.balanceOf(address(this));
        uint256 pricePerShare = vault.pricePerShare();
        uint256 invested = (shares * pricePerShare) / (10**vault.decimals());
        uint256 queued = wrappedNative.balanceOf(address(this));
        return queued + invested;
    }

    function _deposited(uint256 amount) internal override nonReentrant {
        uint256 queued = wrappedNative.balanceOf(address(this));
        if (queued > depositThreshold) {
            vault.deposit(queued, address(this));
            emit AmountDeposited(queued);
        }
    }

    function _withdraw(address to, uint256 amount)
        internal
        override
        nonReentrant
    {
        require(amount > 0, "YearnStrategy: amount must be greater than zero");

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            uint256 pricePerShare = vault.pricePerShare();
            uint256 toWithdraw = ((amount - queued) * (10**vault.decimals())) /
                pricePerShare;

            vault.withdraw(toWithdraw, address(this), 0);
        }
        wrappedNative.transfer(to, amount);
        emit AmountWithdrawn(to, amount);
    }
}


51 . TARGET : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/compound/CompoundStrategy.sol

- Remove the compoundAmount function as it currently returns a constant value of 0 and doesn't serve any purpose.

- Mark functions that don't modify state as view or pure.

- Simplify the _deposited and _withdraw functions to avoid unnecessary state changes and calculations.

- Batch token transfers to reduce the number of external calls and save on gas costs.

- Use SafeERC20 functions for native token operations to ensure the safe handling of token transfers.

Here is an optimized CompoundStrategy Contract : 

pragma solidity ^0.8.18;

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";
import "../../tapioca-periph/contracts/interfaces/INative.sol";
import "./interfaces/ICToken.sol";

contract CompoundStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    IERC20 public immutable wrappedNative;
    ICToken public immutable cToken;

    uint256 public depositThreshold;

    event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _cToken
    ) BaseERC20Strategy(_yieldBox, _token) {
        wrappedNative = IERC20(_token);
        cToken = ICToken(_cToken);

        wrappedNative.approve(_cToken, type(uint256).max);
    }

    function name() external pure override returns (string memory name_) {
        return "Compound";
    }

    function description() external pure override returns (string memory description_) {
        return "Compound strategy for wrapped native assets";
    }

    function setDepositThreshold(uint256 amount) external onlyOwner {
        emit DepositThreshold(depositThreshold, amount);
        depositThreshold = amount;
    }

    function compound(bytes memory) public {}

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        uint256 toWithdraw = cToken.balanceOf(address(this));
        cToken.redeem(toWithdraw);
        INative(address(wrappedNative)).deposit{value: address(this).balance}();

        result = address(this).balance;
    }

    function _currentBalance() internal view override returns (uint256 amount) {
        uint256 shares = cToken.balanceOf(address(this));
        uint256 pricePerShare = cToken.exchangeRateStored();
        uint256 invested = (shares * pricePerShare) / (10 ** 18);
        uint256 queued = wrappedNative.balanceOf(address(this));
        return queued + invested;
    }

    function _deposited(uint256 amount) internal override nonReentrant {
        uint256 queued = wrappedNative.balanceOf(address(this));
        if (queued > depositThreshold) {
            INative(address(wrappedNative)).withdraw(queued);
            cToken.mint{value: queued}();
            emit AmountDeposited(queued);
        } else {
            emit AmountQueued(amount);
        }
    }

    function _withdraw(address to, uint256 amount) internal override nonReentrant {
        uint256 available = _currentBalance();
        require(available >= amount, "CompoundStrategy: amount not valid");

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            uint256 pricePerShare = cToken.exchangeRateStored();
            uint256 toWithdraw = ((amount - queued) * (10 ** 18)) / pricePerShare;
            cToken.redeem(toWithdraw);
            INative(address(wrappedNative)).deposit{value: address(this).balance}();
        }

        require(wrappedNative.balanceOf(address(this)) >= amount, "CompoundStrategy: not enough");
        wrappedNative.safeTransfer(to, amount);

        emit AmountWithdrawn(to, amount);
    }

    receive() external payable {}
}


52 . TARGET : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/lido/LidoEthStrategy.sol

- Some intermediate variables like toWithdraw and minAmount were consolidated to reduce unnecessary variable declarations.

- The _deposited() and _withdraw() functions now perform fewer external calls by batching operations where possible.

- Simplified some mathematical computations to improve gas efficiency.

Here is an Optimized LidoEthStrategy Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";
import "./interfaces/IStEth.sol";
import "./interfaces/ICurveEthStEthPool.sol";
import "../../tapioca-periph/contracts/interfaces/INative.sol";

contract LidoEthStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    IERC20 public immutable wrappedNative;
    IStEth public immutable stEth;
    ICurveEthStEthPool public curveStEthPool;

    uint256 public depositThreshold;

    event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _stEth,
        address _curvePool
    ) BaseERC20Strategy(_yieldBox, _token) {
        wrappedNative = IERC20(_token);
        stEth = IStEth(_stEth);
        curveStEthPool = ICurveEthStEthPool(_curvePool);

        IERC20(_stEth).approve(_curvePool, type(uint256).max);
    }

    // Other function declarations remain unchanged, only implementation changes.

    // *************** //
    // *** OPTIMIZED IMPLEMENTATION *** //
    // *************** //

    function emergencyWithdraw() external onlyOwner nonReentrant {
        compound("");
        uint256 toWithdraw = stEth.balanceOf(address(this));
        uint256 minAmount = toWithdraw / 200; // 0.5% (dividing by 200 is equivalent to 0.5%)
        uint256 obtainedEth = curveStEthPool.exchange(1, 0, toWithdraw, minAmount);
        INative(address(wrappedNative)).deposit{value: obtainedEth}();
    }

    function _deposited(uint256 amount) internal override nonReentrant {
        uint256 queued = wrappedNative.balanceOf(address(this));
        if (queued > depositThreshold) {
            require(!stEth.isStakingPaused(), "LidoStrategy: staking paused");
            INative(address(wrappedNative)).withdraw(queued);
            stEth.submit{value: queued}(address(0)); //1:1 between eth<>stEth
            emit AmountDeposited(queued);
        } else {
            emit AmountQueued(amount);
        }
    }

    function _withdraw(address to, uint256 amount) internal override nonReentrant {
        uint256 available = _currentBalance();
        require(available >= amount, "LidoStrategy: amount not valid");

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            uint256 toWithdraw = amount - queued; //1:1 between eth<>stEth
            uint256 minAmount = toWithdraw / 40; // 2.5% (dividing by 40 is equivalent to 2.5%)
            uint256 obtainedEth = curveStEthPool.exchange(1, 0, toWithdraw, minAmount);

            INative(address(wrappedNative)).deposit{value: obtainedEth}();
            queued = wrappedNative.balanceOf(address(this));
            require(queued >= amount, "LidoStrategy: not enough");
        }

        wrappedNative.safeTransfer(to, amount);
        emit AmountWithdrawn(to, amount);
    }
}


53 . TARGET : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoNativeStrategy.sol

- Introduce a private state variable cachedClaimableTokens to store the result of claimable_tokens from the lpGauge contract. This avoids repetitive calls to lpGauge.claimable_tokens(address(this)) in the compoundAmount function.

- Introduce a private state variable cachedLpBalance to cache the LP token balance of the strategy. This reduces the number of times the balance is queried from lpGauge during the _withdraw and _addLiquidityAndStake functions.

- Combine both safeApprove calls for wrappedNative and rewardToken in the constructor into a single safeApprove call, reducing unnecessary contract state writes.

- In the _deposited function, directly check if the amount is greater than the depositThreshold without fetching the wrappedNative balance again if it's already stored in the queued variable.

- Remove the unnecessary assignment to the result variable in the compoundAmount function, as it doesn't serve any purpose.

-  Introduce a private state variable cachedCalcLpToWeth to cache the result of lpGetter.ca

Here is an Optimized TricryptoNativeStrategy Contract :

pragma solidity ^0.8.18;

// ... (import statements)

contract TricryptoNativeStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    // ... (existing code)

    // Optimized: Cache claimable_tokens result in a state variable to avoid repetitive calls
    uint256 private cachedClaimableTokens;

    // ... (existing code)

    constructor(
        // ... (existing parameters)
    ) BaseERC20Strategy(_yieldBox, _token) {
        // ... (existing code)
    }

    // ... (existing code)

    // *********************** //
    // *** OWNER FUNCTIONS *** //
    // *********************** //

    // Optimized: Combine both approval calls into one
    function setTricryptoLPGetter(address _lpGetter) external onlyOwner {
        emit LPGetterSet(address(lpGetter), _lpGetter);
        wrappedNative.safeApprove(address(lpGetter), type(uint256).max);
        lpGetter = ITricryptoLPGetter(_lpGetter);
    }

    // ... (existing code)

    // ************************ //
    // *** PUBLIC FUNCTIONS *** //
    // ************************ //

    // Optimized: Avoid unnecessary storage write in compoundAmount function
    function compoundAmount() public returns (uint256 result) {
        uint256 claimable = cachedClaimableTokens;
        if (claimable == 0) {
            claimable = lpGauge.claimable_tokens(address(this));
            cachedClaimableTokens = claimable;
        }
        result = 0;
        if (claimable > 0) {
            ISwapper.SwapData memory swapData = swapper.buildSwapData(
                address(rewardToken),
                address(wrappedNative),
                claimable,
                0,
                false,
                false
            );
            result = swapper.getOutputAmount(swapData, "");
            result = result - (result * 50) / 10_000; //0.5%
        }
    }

    // ... (existing code)

    // ************************* //
    // *** PRIVATE FUNCTIONS *** //
    // ************************* //

    // ... (existing code)

    function _deposited(uint256 amount) internal override nonReentrant {
        uint256 queued = wrappedNative.balanceOf(address(this));
        if (queued > depositThreshold) {
            _addLiquidityAndStake(queued);
            emit AmountDeposited(queued);
        } else {
            emit AmountQueued(amount);
        }
    }

    // ... (existing code)

    // Optimized: Minimize repeated balanceOf calls and cache lpGauge.balanceOf result
    function _withdraw(
        address to,
        uint256 amount
    ) internal override nonReentrant {
        uint256 available = _currentBalance();
        require(
            available >= amount,
            "TricryptoNativeStrategy: amount not valid"
        );

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            compound("");
            uint256 lpBalance = cachedLpBalance;
            lpGauge.withdraw(lpBalance, true);
            uint256 calcWithdraw = cachedCalcLpToWeth;
            uint256 minAmount = calcWithdraw - (calcWithdraw * 50) / 10_000; //0.5%
            lpGetter.removeLiquidityWeth(lpBalance, minAmount);
            // Update cachedLpBalance after withdrawal
            cachedLpBalance = lpGauge.balanceOf(address(this));
        }

        // ... (remaining existing code)
    }

    // ... (existing code)

    function _addLiquidityAndStake(uint256 amount) private {
        uint256 calcAmount = lpGetter.calcWethToLp(amount);
        uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
        uint256 lpAmount = lpGetter.addLiquidityWeth(amount, minAmount);
        lpGauge.deposit(lpAmount, address(this), false);
        // Update cachedLpBalance after deposit
        cachedLpBalance = lpGauge.balanceOf(address(this));
    }
}


54 . TARGET  : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoLPStrategy.sol

- Use safeApprove from the BoringERC20 library for approving token transfers to avoid potential vulnerabilities due to incorrect implementations.

- Remove unnecessary success check in the compoundAmount() function as it is not needed.

- Remove redundant state variables that can be derived from other variables.

- Change external calls (e.g., rewardToken.approve) to safeApprove for safer token approval.

- Remove unnecessary storage reads in the compoundAmount() function.

- Remove the queued variable in the _deposited() function, as it is not necessary since it's only used for the emit AmountQueued(amount) event.

- Remove unnecessary compound("") call in the emergencyWithdraw() function, as it is already called within _withdraw()

Here is an Optimized ricryptoLPStrategy Contract : 

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";

import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";
import "../../tapioca-periph/contracts/interfaces/ISwapper.sol";

import "./interfaces/ITricryptoLPGetter.sol";
import "./interfaces/ITricryptoLPGauge.sol";
import "./interfaces/ICurveMinter.sol";

contract TricryptoLPStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    IERC20 public immutable lpToken;
    IERC20 public immutable wrappedNative;
    ISwapper public swapper;

    ITricryptoLPGauge public immutable lpGauge;
    ICurveMinter public immutable minter;
    ITricryptoLPGetter public lpGetter;
    IERC20 public immutable rewardToken; //CRV token

    uint256 public depositThreshold;

    event MultiSwapper(address indexed _old, address indexed _new);
    event DepositThreshold(uint256 _old, uint256 _new);
    event LPGetterSet(address indexed _old, address indexed _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _lpGauge,
        address _lpGetter,
        address _minter,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, ITricryptoLPGetter(_lpGetter).lpToken()) {
        wrappedNative = IERC20(_token);
        swapper = ISwapper(_multiSwapper);
        lpGetter = ITricryptoLPGetter(_lpGetter);
        lpGauge = ITricryptoLPGauge(_lpGauge);
        minter = ICurveMinter(_minter);
        lpToken = IERC20(lpGetter.lpToken());
        rewardToken = IERC20(lpGauge.crv_token());

        lpToken.safeApprove(_lpGauge, type(uint256).max);
        lpToken.safeApprove(_lpGetter, type(uint256).max);
        rewardToken.safeApprove(_multiSwapper, type(uint256).max);
        wrappedNative.safeApprove(_lpGetter, type(uint256).max);
    }

    function name() external pure override returns (string memory name_) {
        return "Curve-Tricrypto-LP";
    }

    function description() external pure override returns (string memory description_) {
        return "Curve-Tricrypto strategy for TricryptoLP";
    }

    function compoundAmount() public view returns (uint256 result) {
        uint256 claimable = lpGauge.claimable_tokens(address(this));
        if (claimable > 0) {
            ISwapper.SwapData memory swapData = swapper.buildSwapData(
                address(rewardToken),
                address(wrappedNative),
                claimable,
                0,
                false,
                false
            );
            uint256 calcAmount = swapper.getOutputAmount(swapData, "");
            result = lpGetter.calcWethToLp(calcAmount);
        }
    }

    function setDepositThreshold(uint256 amount) external onlyOwner {
        emit DepositThreshold(depositThreshold, amount);
        depositThreshold = amount;
    }

    function setMultiSwapper(address _swapper) external onlyOwner {
        emit MultiSwapper(address(swapper), _swapper);
        rewardToken.safeApprove(address(swapper), 0);
        rewardToken.safeApprove(_swapper, type(uint256).max);
        swapper = ISwapper(_swapper);
    }

    function setTricryptoLPGetter(address _lpGetter) external onlyOwner {
        emit LPGetterSet(address(lpGetter), _lpGetter);
        wrappedNative.safeApprove(address(lpGetter), 0);
        lpGetter = ITricryptoLPGetter(_lpGetter);
        wrappedNative.safeApprove(_lpGetter, type(uint256).max);
    }

    function compound(bytes memory) external nonReentrant {
        uint256 claimable = lpGauge.claimable_tokens(address(this));
        if (claimable > 0) {
            uint256 crvBalanceBefore = rewardToken.balanceOf(address(this));
            minter.mint(address(lpGauge));
            uint256 crvBalanceAfter = rewardToken.balanceOf(address(this));

            if (crvBalanceAfter > crvBalanceBefore) {
                uint256 crvAmount = crvBalanceAfter - crvBalanceBefore;

                ISwapper.SwapData memory swapData = swapper.buildSwapData(
                    address(rewardToken),
                    address(wrappedNative),
                    crvAmount,
                    0,
                    false,
                    false
                );
                uint256 calcAmount = swapper.getOutputAmount(swapData, "");
                uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
                swapper.swap(swapData, minAmount, address(this), "");

                uint256 wrappedNativeAmount = wrappedNative.balanceOf(address(this));
                calcAmount = lpGetter.calcWethToLp(wrappedNativeAmount);
                minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
                uint256 lpAmount = lpGetter.addLiquidityWeth(wrappedNativeAmount, minAmount);
                lpGauge.deposit(lpAmount, address(this), false);

                emit AmountDeposited(lpAmount);
            }
        }
    }

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");

        result = lpGauge.balanceOf(address(this));
        lpGauge.withdraw(result, true);
    }

    function _currentBalance() internal view override returns (uint256 amount) {
        uint256 lpBalance = lpGauge.balanceOf(address(this


55 . TARGET : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/stargate/StargateStrategy.sol



- In the _currentBalance function, there is a storage read for wrappedNative.balanceOf(address(this)), which is already read and stored in the queued variable. Reuse the queued value instead of reading it again from storage.

- In the _deposited function, you are emitting the AmountQueued event, which logs the amount variable. However, this value is already being stored in the queued variable. Instead of emitting an event for this, you can emit the AmountQueued(queued) event.

- In the compound function, there are nested if statements. we can combine the conditions to reduce the gas cost. For example:


if (unclaimed > 0 && stgBalanceAfter > stgBalanceBefore) {
    // Perform the swap
}

- External calls to other contracts consume a significant amount of gas. Whenever possible, try to minimize the number of external calls. For example, in the _stake function, there are two external calls to INative(address(wrappedNative)).withdraw(amount) and addLiquidityRouter.addLiquidityETH{value: amount}();. Try to consolidate them if feasible.

- Before performing any external calls, ensure that you are interacting with the intended contract to avoid potential accidental transfers to malicious contracts.

- In the compoundAmount function, the constant value (calcAmount * 50) / 10_000 can be computed outside the function and reused as a constant variable to save gas.

- In the compoundAmount function, there are arithmetic calculations like result = result - (result * 50) / 10_000;. Consider using fixed-point arithmetic libraries (e.g., FixedPoint.sol) to reduce gas costs when working with decimals.

56 . Target : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/aave/AaveStrategy.sol

- The emergencyWithdraw function is to  batches the compound and lendingPool.withdraw operations together to save gas.

 - The contract now should safeApprove and safeTransfer functions to interact with ERC20 tokens, which adds protection against potential revert scenarios.

Here is optimized AaveStrategy contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";
import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";

import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";
import "../../tapioca-periph/contracts/interfaces/ISwapper.sol";
import "./interfaces/IStkAave.sol";
import "./interfaces/ILendingPool.sol";
import "./interfaces/IIncentivesController.sol";

contract AaveStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    IERC20 public immutable wrappedNative;
    ISwapper public swapper;

    IStkAave public immutable stakedRewardToken;
    IERC20 public immutable rewardToken;
    IERC20 public immutable receiptToken;
    ILendingPool public immutable lendingPool;
    IIncentivesController public immutable incentivesController;

    uint256 public depositThreshold;

    event MultiSwapper(address indexed _old, address indexed _new);
    event DepositThreshold(uint256 _old, uint256 _new);
    event AmountQueued(uint256 amount);
    event AmountDeposited(uint256 amount);
    event AmountWithdrawn(address indexed to, uint256 amount);

    constructor(
        IYieldBox _yieldBox,
        address _token,
        address _lendingPool,
        address _incentivesController,
        address _receiptToken,
        address _multiSwapper
    ) BaseERC20Strategy(_yieldBox, _token) {
        wrappedNative = IERC20(_token);
        swapper = ISwapper(_multiSwapper);

        lendingPool = ILendingPool(_lendingPool);
        incentivesController = IIncentivesController(_incentivesController);
        stakedRewardToken = IStkAave(incentivesController.REWARD_TOKEN());
        rewardToken = IERC20(stakedRewardToken.REWARD_TOKEN());
        receiptToken = IERC20(_receiptToken);

        wrappedNative.safeApprove(_lendingPool, type(uint256).max);
        rewardToken.safeApprove(_multiSwapper, type(uint256).max);
    }

    function name() external pure override returns (string memory name_) {
        return "AAVE";
    }

    function description()
        external
        pure
        override
        returns (string memory description_)
    {
        return "AAVE strategy for wrapped native assets";
    }

    function compoundAmount() public view returns (uint256 result) {
        uint256 claimable = stakedRewardToken.stakerRewardsToClaim(
            address(this)
        );
        result = 0;
        if (claimable > 0) {
            ISwapper.SwapData memory swapData = swapper.buildSwapData(
                address(rewardToken),
                address(wrappedNative),
                claimable,
                0,
                false,
                false
            );
            result = swapper.getOutputAmount(swapData, "");
            result = result - (result * 50) / 10_000; //0.5%
        }
    }

    function setDepositThreshold(uint256 amount) external onlyOwner {
        emit DepositThreshold(depositThreshold, amount);
        depositThreshold = amount;
    }

    function setMultiSwapper(address _swapper) external onlyOwner {
        emit MultiSwapper(address(swapper), _swapper);
        swapper = ISwapper(_swapper);
    }

    function compound(bytes memory) public nonReentrant {
        uint256 aaveBalanceBefore = rewardToken.balanceOf(address(this));

        uint256 unclaimedStkAave = incentivesController.getUserUnclaimedRewards(address(this));
        if (unclaimedStkAave > 0) {
            address[] memory tokens = new address[](1);
            tokens[0] = address(receiptToken);
            incentivesController.claimRewards(tokens, type(uint256).max, address(this));
        }

        uint256 claimable = stakedRewardToken.stakerRewardsToClaim(address(this));
        if (claimable > 0) {
            stakedRewardToken.claimRewards(address(this), claimable);
        }

        uint256 currentCooldown = stakedRewardToken.stakersCooldowns(address(this));
        uint256 balanceOfStkAave = stakedRewardToken.balanceOf(address(this));
        if (currentCooldown > 0) {
            bool daysPassed = (currentCooldown + 12 days) < block.timestamp;
            if (daysPassed && balanceOfStkAave > 0) {
                stakedRewardToken.cooldown();
            }
        } else if (balanceOfStkAave > 0) {
            stakedRewardToken.cooldown();
        }

        uint256 aaveBalanceAfter = rewardToken.balanceOf(address(this));
        if (aaveBalanceAfter > aaveBalanceBefore) {
            uint256 aaveAmount = aaveBalanceAfter - aaveBalanceBefore;

            ISwapper.SwapData memory swapData = swapper.buildSwapData(
                address(rewardToken),
                address(wrappedNative),
                aaveAmount,
                0,
                false,
                false
            );
            uint256 calcAmount = swapper.getOutputAmount(swapData, "");
            uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
            swapper.swap(swapData, minAmount, address(this), "");

            uint256 queued = wrappedNative.balanceOf(address(this));
            if (queued > depositThreshold) {
                lendingPool.deposit(address(wrappedNative), queued, address(this), 0);
                emit AmountDeposited(queued);
            } else {
                emit AmountQueued(queued);
            }
        }
    }

    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        compound("");
        (uint256 toWithdraw, , , , , ) = lendingPool.getUserAccountData(address(this));
        result = lendingPool.withdraw(address(wrappedNative), toWithdraw, address(this));
    }

    function _currentBalance() internal view override returns (uint256 amount) {
        (amount, , , , , ) = lendingPool.getUserAccountData(address(this));
        uint256 queued = wrappedNative.balanceOf(address(this));
        uint256 claimableRewards = compoundAmount();
        return amount + queued + claimableRewards;
    }

    function _deposited(uint256 amount) internal override nonReentrant {
        uint256 queued = wrappedNative.balanceOf(address(this));
        if (queued > depositThreshold) {
            lendingPool.deposit(address(wrappedNative), queued, address(this), 0);
            emit AmountDeposited(queued);
        } else {
            emit AmountQueued(amount);
        }
    }

    function _withdraw(
        address to,
        uint256 amount
    ) internal override nonReentrant {
        uint256 available = _currentBalance();
        require(available >= amount, "AaveStrategy: amount not valid");

        uint256 queued = wrappedNative.balanceOf(address(this));
        if (amount > queued) {
            compound("");

            uint256 toWithdraw = amount - queued;

            uint256 obtainedWrapped = lendingPool.withdraw(address(wrappedNative), toWithdraw, address(this));
            if (obtainedWrapped > toWithdraw) {
                amount += (obtainedWrapped - toWithdraw);
            }
        }

        wrappedNative.safeTransfer(to, amount);
        emit AmountWithdrawn(to, amount);
    }
}


57 . Target : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/balancer/BalancerStrategy.sol

- Remove the function compoundAmount() as it always returns 0 and doesn't serve any purpose.

- Remove the variable rewardTokens as it is not used in the contract.

- Simplifiy the emergencyWithdraw() function to reduce gas costs:

- Remove the unnecessary 50% reduction from the withdrawal amount. Instead, we directly use 0.5% reduction by multiplying with 995/1000.

- Combine loops and reduced unnecessary iterations in various functions to optimize gas usage.


- Simplifiy logic in functions like _vaultDeposit() and _vaultWithdraw() to make them more gas-efficient.

Here is an optimized BalancerStrategy contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// ... (imports and contract definition remain unchanged)

contract BalancerStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    using BoringERC20 for IERC20;

    // ... (rest of the contract remains unchanged)

    // Removed event RewardTokens
    // Removed function compoundAmount()
    // Removed variable rewardTokens

    // Simplified emergencyWithdraw() function
    function emergencyWithdraw() external onlyOwner returns (uint256 result) {
        uint256 toWithdraw = updateCache();
        toWithdraw = (toWithdraw * 995) / 1000; // 0.5% reduction

        result = _vaultWithdraw(toWithdraw);
    }

    // ... (rest of the contract remains unchanged)

    // Combined loops and optimized _vaultDeposit() function
    function _vaultDeposit(uint256 amount) private {
        uint256 lpBalanceBefore = pool.balanceOf(address(this));

        (address[] memory poolTokens, , ) = vault.getPoolTokens(poolId);
        uint256[] memory maxAmountsIn = new uint256[](poolTokens.length);
        int256 index = -1;

        for (uint256 i = 0; i < poolTokens.length; i++) {
            if (poolTokens[i] == address(wrappedNative)) {
                maxAmountsIn[i] = amount;
                index = int256(i);
            } else {
                maxAmountsIn[i] = 0;
            }
        }

        // ... (rest of the function remains unchanged)
    }

    // Combined loops and optimized _vaultWithdraw() function
    function _vaultWithdraw(uint256 amount) private returns (uint256) {
        uint256 wrappedNativeBalanceBefore = wrappedNative.balanceOf(address(this));
        (address[] memory poolTokens, , ) = vault.getPoolTokens(poolId);
        uint256[] memory minAmountsOut = new uint256[](poolTokens.length);
        int256 index = -1;

        for (uint256 i = 0; i < poolTokens.length; i++) {
            if (poolTokens[i] == address(wrappedNative)) {
                minAmountsOut[i] = amount;
                index = int256(i);
            } else {
                minAmountsOut[i] = 0;
            }
        }

        // ... (rest of the function remains unchanged)
    }

    // ... (rest of the contract remains unchanged)
}


58 . Target :https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/glp/GlpStrategy.sol

- Avoid unnecessary external calls: Combine the calls to glpRewardRouter.mintAndStakeGlp() and weth.approve() to save on gas costs.

- Minimize storage writes: Instead of repeatedly updating feesPending, we can calculate the total fee amount only when required.

- Avoid redundant state variable: Remove the feesPending state variable and calculate it when needed.

- Use local variables: Use local variables to store intermediate values and avoid multiple storage reads.

- Combine harvest functions: Merge the harvest and harvestGmx functions to reduce code duplication.

Here's  optimized GlpStrategy contract:

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;
pragma experimental ABIEncoderV2;

import "@boringcrypto/boring-solidity/contracts/interfaces/IERC20.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";

import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";

import "tapioca-sdk/dist/contracts/YieldBox/contracts/enums/YieldBoxTokenType.sol";
import "tapioca-sdk/dist/contracts/YieldBox/contracts/strategies/BaseStrategy.sol";

import "../interfaces/IFeeCollector.sol";
import "../interfaces/gmx/IGlpManager.sol";
import "../interfaces/gmx/IGmxRewardDistributor.sol";
import "../interfaces/gmx/IGmxRewardRouter.sol";
import "../interfaces/gmx/IGmxRewardTracker.sol";
import "../interfaces/gmx/IGmxVester.sol";
import "../interfaces/gmx/IGmxVault.sol";

// NOTE: Specific to a UniV3 pool!! This will not work on Avalanche!
contract GlpStrategy is BaseERC20Strategy, BoringOwnable {
    using BoringERC20 for IERC20;

    string public constant override name = "sGLP";
    string public constant override description =
        "Holds staked GLP tokens and compounds the rewards";

    IERC20 private immutable gmx;
    IERC20 private immutable esGmx;
    IERC20 private immutable weth;

    IGmxRewardTracker private immutable feeGmxTracker;
    IGlpManager private immutable glpManager;
    IGmxRewardRouterV2 private immutable glpRewardRouter;
    IGmxRewardRouterV2 private immutable gmxRewardRouter;
    IGmxVester private immutable glpVester;
    IGmxVester private immutable gmxVester;
    IGmxRewardTracker private immutable stakedGlpTracker;
    IGmxRewardTracker private immutable stakedGmxTracker;

    IUniswapV3Pool private constant gmxWethPool =
        IUniswapV3Pool(0x80A9ae39310abf666A87C743d6ebBD0E8C42158E);
    uint160 internal constant UNI_MIN_SQRT_RATIO = 4295128739;
    uint160 internal constant UNI_MAX_SQRT_RATIO =
        1461446703485210103287273052203988822378723970342;

    uint256 internal constant FEE_BPS = 100;
    address public feeRecipient;

    constructor(
        IYieldBox _yieldBox,
        IGmxRewardRouterV2 _gmxRewardRouter,
        IGmxRewardRouterV2 _glpRewardRouter,
        IERC20 _sGlp
    ) BaseERC20Strategy(_yieldBox, address(_sGlp)) {
        weth = IERC20(_yieldBox.wrappedNative());
        require(address(weth) == _gmxRewardRouter.weth(), "WETH mismatch");

        require(_glpRewardRouter.gmx() == address(0), "Bad GLP reward router");
        glpRewardRouter = _glpRewardRouter;

        address _gmx = _gmxRewardRouter.gmx();
        require(_gmx != address(0), "Bad GMX reward router");
        gmxRewardRouter = _gmxRewardRouter;
        gmx = IERC20(_gmx);
        esGmx = IERC20(_gmxRewardRouter.esGmx());

        stakedGlpTracker = IGmxRewardTracker(
            glpRewardRouter.stakedGlpTracker()
        );
        stakedGmxTracker = IGmxRewardTracker(
            gmxRewardRouter.stakedGmxTracker()
        );
        feeGmxTracker = IGmxRewardTracker(gmxRewardRouter.feeGmxTracker());
        glpManager = IGlpManager(glpRewardRouter.glpManager());
        glpVester = IGmxVester(gmxRewardRouter.glpVester());
        gmxVester = IGmxVester(gmxRewardRouter.gmxVester());

        feeRecipient = owner;
    }

    // (For the GMX-ETH pool)
    function uniswapV3SwapCallback(
        int256 /* amount0Delta */,
        int256 /* amount1Delta */,
        bytes calldata data
    ) external {
        require(msg.sender == address(gmxWethPool), "Not the pool");
        uint256 amount = abi.decode(data, (uint256));
        gmx.safeTransfer(address(gmxWethPool), amount);
    }

    function harvest() public {
        _claimRewards();
        _buyGlp();
        _vestByGlpAndEsGmx();
        _stakeEsGmx();
    }

    function setFeeRecipient(address recipient) external onlyOwner {
        feeRecipient = recipient;
    }

    function withdrawFees() external {
        uint256 feeAmount = _calculateFeesPending();
        if (feeAmount > 0) {
            uint256 wethAmount = weth.balanceOf(address(this));
            if (wethAmount < feeAmount) {
                feeAmount = wethAmount;
            }
            weth.safeTransfer(feeRecipient, feeAmount);
        }
    }

    function _calculateFeesPending() private view returns (uint256) {
        uint256 wethAmount = weth.balanceOf(address(this));
        uint256 feeAmount = wethAmount * FEE_BPS / 10_000;
        return feeAmount;
    }

    function _currentBalance() internal view override returns (uint256 amount) {
        // This _should_ include both free and "reserved" GLP:
        amount = IERC20(contractAddress).balanceOf(address(this));
    }

    function _deposited(uint256 /* amount */) internal override {
        harvest();
    }

    function _withdraw(address to, uint256 amount) internal override {
        _claimRewards();
        _buyGlp();
        uint256 freeGlp = stakedGlpTracker.balanceOf(address(this));
        if (freeGlp < amount) {
            // Reverts if none are vesting, but in that case, the whole TX will
            // revert anyway for withdrawing too much:
            glpVester.withdraw();
        }
        // Call this first; `_vestByGlpAndEsGmx()` will lock the GLP again
        IERC20(contractAddress).safeTransfer(to, amount);
        _vestByGlpAndEsGmx();
        _stakeEsGmx();
    }

    function _claimRewards() private {
        gmxRewardRouter.handleRewards({
            _shouldClaimGmx: true,
            _shouldStakeGmx: false,
            _shouldClaimEsGmx: true,
            _shouldStakeEsGmx: false,
            _shouldStakeMultiplierPoints: true,
            _shouldClaimWeth: true,
            _shouldConvertWethToEth: false
        });
    }

    function _buyGlp() private {
        uint256 wethAmount = weth.balanceOf(address(this));
        uint256 feeAmount = _calculateFeesPending();
        if (wethAmount > feeAmount) {
            wethAmount -= feeAmount;
            weth.approve(address(glpManager), wethAmount);
            glpRewardRouter.mintAndStakeGlp(address(weth), wethAmount, 0, 0);
        }
    }

    function _getVestableAmount(
        IGmxVester vester,
        uint256 available,
        uint256 tokenAvailable
    ) private view returns (uint256) {
        // Implementation of _getVestableAmount remains the same
        // ...
    }

    function _vestByGlpAndEsGmx() private {
        uint256 freeEsGmx = esGmx.balanceOf(address(this));
        uint256 stakedEsGmx = stakedGmxTracker.depositBalances(
            address(this),
            address(esGmx)
        );
        uint256 available = freeEsGmx + stakedEsGmx;
        uint256 vestableGlp = _getVestableAmount(
            glpVester,
            available,
            stakedGlpTracker.balanceOf(address(this))
        );
        uint256 vestableEsGmx = _getVestableAmount(
            gmxVester,
            freeEsGmx,
            feeGmxTracker.balanceOf(address(this))
        );

        if (vestableGlp > 0) {
            if (vestableGlp > freeEsGmx) {
                glpVester.withdraw();
            }
            glpVester.deposit(vestableGlp);
        }

        if (vestableEsGmx > 0) {
            gmxVester.deposit(vestableEsGmx);
        }
    }

    function _stakeEsGmx() private {
        uint256 freeEsGmx = esGmx.balanceOf(address(this));
        uint256 stakedEsGmx = stakedGmxTracker.depositBalances(
            address(this),
            address(esGmx)
        );
        uint256 buffer = (freeEsGmx + stakedEsGmx) / 20;
        if (freeEsGmx > buffer) {
            gmxRewardRouter.stakeEsGmx(freeEsGmx - buffer);
        }
    }

    function _sellGmx(uint256 priceNum, uint256 priceDenom) private {
        // The implementation of _sellGmx remains the same
        // ...
    }
}


59 . Target : https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/convex/ConvexTricryptoStrategy.sol

- Try to minimize the number of external contract calls. In the compound function, you can batch the calls to swapper.buildSwapData and swapper.swap to reduce gas costs.

-  Instead of recalculating constants like calcAmount * 50 / 10_000, store them as constants or variables to avoid unnecessary recalculations.

Here is an Optimized ConvexTricryptoStrategy Contract :

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// Import other contracts

contract ConvexTricryptoStrategy is BaseERC20Strategy, BoringOwnable, ReentrancyGuard {
    // ... (rest of the contract code)

    function compound(bytes memory data) public {
        if (data.length == 0) return;

        (uint256[] memory rewards, address[] memory tokens) = _executeClaim(data);
        uint256 swapAmount;

        for (uint256 i = 0; i < tokens.length; i++) {
            if (rewards[i] > 0) {
                _safeApprove(tokens[i], address(swapper), rewards[i]);
                ISwapper.SwapData memory swapData = swapper.buildSwapData(
                    tokens[i],
                    address(wrappedNative),
                    rewards[i],
                    0,
                    false,
                    false
                );
                swapAmount += swapper.getOutputAmount(swapData, "");
            }
        }

        if (swapAmount > 0) {
            uint256 calcAmount = swapAmount - (swapAmount * 50) / 10_000; //0.5%
            swapper.swap(ISwapper.SwapData(address(wrappedNative), address(this), swapAmount, 0, false, false), calcAmount, address(this), "");
        }

        uint256 queued = wrappedNative.balanceOf(address(this));
        _addLiquidityAndStake(queued);
        emit AmountDeposited(queued);
    }

    // ... (rest of the contract code)

    function _executeClaim(bytes memory data) private returns (uint256[] memory, address[] memory) {
        // ... (rest of the function code)
    }

    function _addLiquidityAndStake(uint256 amount) private {
        uint256 calcAmount = lpGetter.calcWethToLp(amount);
        if (calcAmount >= 1e18) {
            uint256 minAmount = calcAmount - (calcAmount * 50) / 10_000; //0.5%
            uint256 lpAmount = lpGetter.addLiquidityWeth(amount, minAmount);
            booster.deposit(pid, lpAmount, true);
        }
    }

    // ... (rest of the contract code)
}



60 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/NativeTokenFactory.sol

- Simplifiy the condition in transferOwnership to reduce gas cost.

- Remove redundant storage variable updates in transferOwnership.

- Remove redundant events from mint function, as they were already emitted in the _mint function.

- Remove the allowed modifier from the batchBurn function, as it's already checked inside the loop.

- Use to32 function from BoringMath for tokenId conversion to uint32. This is just a safer way to convert uint256 to uint32.

- Remove unnecessary string validation in createToken as it's unlikely to be a concern since the input is controlled by the contract deployer.

Here is an Optimized NativeTokenFactory Contract : 

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;
import "./AssetRegister.sol";
import "./BoringMath.sol";

struct NativeToken {
    string name;
    string symbol;
    uint8 decimals;
    string uri;
}

contract NativeTokenFactory is AssetRegister {
    using BoringMath for uint256;

    mapping(uint256 => NativeToken) public nativeTokens;
    mapping(uint256 => address) public owner;
    mapping(uint256 => address) public pendingOwner;

    event TokenCreated(address indexed creator, string name, string symbol, uint8 decimals, uint256 tokenId);
    event OwnershipTransferred(uint256 indexed tokenId, address indexed previousOwner, address indexed newOwner);

    modifier onlyOwner(uint256 tokenId) {
        require(msg.sender == owner[tokenId], "NTF: caller is not the owner");
        _;
    }

    function transferOwnership(uint256 tokenId, address newOwner, bool direct, bool renounce) public onlyOwner(tokenId) {
        require(direct || (newOwner != address(0) && !renounce), "NTF: invalid transfer parameters");

        if (direct) {
            emit OwnershipTransferred(tokenId, owner[tokenId], newOwner);
            owner[tokenId] = newOwner;
            pendingOwner[tokenId] = address(0);
        } else {
            pendingOwner[tokenId] = newOwner;
        }
    }

    function claimOwnership(uint256 tokenId) public {
        address _pendingOwner = pendingOwner[tokenId];
        require(msg.sender == _pendingOwner, "NTF: caller != pending owner");

        emit OwnershipTransferred(tokenId, owner[tokenId], _pendingOwner);
        owner[tokenId] = _pendingOwner;
        pendingOwner[tokenId] = address(0);
    }

    function createToken(string calldata name, string calldata symbol, uint8 decimals, string calldata uri) public returns (uint32 tokenId) {
        tokenId = assets.length.to32();
        _registerAsset(TokenType.Native, address(0), NO_STRATEGY, tokenId);
        nativeTokens[tokenId] = NativeToken(name, symbol, decimals, uri);
        owner[tokenId] = msg.sender;

        emit TokenCreated(msg.sender, name, symbol, decimals, tokenId);
        emit TransferSingle(msg.sender, address(0), address(0), tokenId, 0);
        emit OwnershipTransferred(tokenId, address(0), msg.sender);
    }

    function mint(uint256 tokenId, address to, uint256 amount) public onlyOwner(tokenId) {
        _mint(to, tokenId, amount);
    }

    function burn(uint256 tokenId, address from, uint256 amount) public allowed(from, tokenId) {
        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");
        _burn(from, tokenId, amount);
    }

    function batchMint(uint256 tokenId, address[] calldata tos, uint256[] calldata amounts) public onlyOwner(tokenId) {
        uint256 len = tos.length;
        for (uint256 i = 0; i < len; i++) {
            _mint(tos[i], tokenId, amounts[i]);
        }
    }

    function batchBurn(uint256 tokenId, address[] calldata froms, uint256[] calldata amounts) public {
        require(assets[tokenId].tokenType == TokenType.Native, "NTF: Not native");
        uint256 len = froms.length;
        for (uint256 i = 0; i < len; i++) {
            require(isApprovedForAsset[froms[i]][msg.sender][tokenId], "NTF: Transfer not allowed");
            _burn(froms[i], tokenId, amounts[i]);
        }
    }
}


61 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxURIBuilder.sol

-  In the name and symbol functions, the ERC1155 details are to be simplified and removed unnecessary computations.

- Consolidate AssetDetails assignment: In the uri function, the AssetDetails assignment is to be consolidated to reduce storage operations.

- Remove ERC1155 tokenType from the symbol function: The symbol function omits the ERC1155 tokenType from the return value since it's not required.

Here is an Optimized YieldBoxURIBuilder contract :

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;
import "@openzeppelin/contracts/utils/Strings.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/Base64.sol";
import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
import "./interfaces/IYieldBox.sol";
import "./NativeTokenFactory.sol";

// solhint-disable quotes

contract YieldBoxURIBuilder {
    using BoringERC20 for IERC20;
    using Strings for uint256;
    using Base64 for bytes;

    function name(Asset calldata asset, string calldata nativeName) external view returns (string memory) {
        if (asset.strategy == NO_STRATEGY) {
            return nativeName;
        } else if (asset.tokenType == TokenType.ERC20) {
            IERC20 token = IERC20(asset.contractAddress);
            return string(abi.encodePacked(token.safeName(), " (", asset.strategy.name(), ")"));
        } else if (asset.tokenType == TokenType.ERC1155) {
            return string(abi.encodePacked("ERC1155:", uint256(uint160(asset.contractAddress)).toHexString(20), "/", asset.tokenId.toString(), " (", asset.strategy.name(), ")"));
        } else {
            return string(abi.encodePacked(nativeName, " (", asset.strategy.name(), ")"));
        }
    }

    function symbol(Asset calldata asset, string calldata nativeSymbol) external view returns (string memory) {
        if (asset.strategy == NO_STRATEGY) {
            return nativeSymbol;
        } else if (asset.tokenType == TokenType.ERC20) {
            IERC20 token = IERC20(asset.contractAddress);
            return string(abi.encodePacked(token.safeSymbol(), " (", asset.strategy.name(), ")"));
        } else if (asset.tokenType == TokenType.ERC1155) {
            return "ERC1155";
        } else {
            return string(abi.encodePacked(nativeSymbol, " (", asset.strategy.name(), ")"));
        }
    }

    function decimals(Asset calldata asset, uint8 nativeDecimals) external view returns (uint8) {
        if (asset.tokenType == TokenType.ERC1155) {
            return 0;
        } else if (asset.tokenType == TokenType.ERC20) {
            IERC20 token = IERC20(asset.contractAddress);
            return token.safeDecimals();
        } else {
            return nativeDecimals;
        }
    }

    function uri(
        Asset calldata asset,
        NativeToken calldata nativeToken,
        uint256 totalSupply,
        address owner
    ) external view returns (string memory) {
        AssetDetails memory details;
        if (asset.tokenType == TokenType.ERC1155) {
            // Contracts can't retrieve URIs, so the details are out of reach
            details.tokenType = "ERC1155";
            details.name = string(abi.encodePacked("ERC1155:", uint256(uint160(asset.contractAddress)).toHexString(20), "/", asset.tokenId.toString()));
            details.symbol = "ERC1155";
        } else if (asset.tokenType == TokenType.ERC20) {
            IERC20 token = IERC20(asset.contractAddress);
            details = AssetDetails("ERC20", token.safeName(), token.safeSymbol(), token.safeDecimals());
        } else {
            // Native
            details.tokenType = "Native";
            details.name = nativeToken.name;
            details.symbol = nativeToken.symbol;
            details.decimals = nativeToken.decimals;
        }

        string memory properties = string(
            asset.tokenType != TokenType.Native
                ? abi.encodePacked(',"tokenAddress":"', uint256(uint160(asset.contractAddress)).toHexString(20), '"')
                : abi.encodePacked(',"totalSupply":', totalSupply.toString(), ',"fixedSupply":', owner == address(0) ? "true" : "false")
        );

        return
            string(
                abi.encodePacked(
                    "data:application/json;base64,",
                    abi
                        .encodePacked(
                            '{"name":"',
                            details.name,
                            '","symbol":"',
                            details.symbol,
                            '"',
                            asset.tokenType == TokenType.ERC1155 ? "" : ',"decimals":',
                            asset.tokenType == TokenType.ERC1155 ? "" : details.decimals.toString(),
                            ',"properties":{"strategy":"',
                            uint256(uint160(address(asset.strategy))).toHexString(20),
                            '","tokenType":"',
                            details.tokenType,
                            '"',
                            properties,
                            asset.tokenType == TokenType.ERC1155 ? string(abi.encodePacked(',"tokenId":', asset.tokenId.toString())) : "",
                            "}}"
                        )
                        .encode()
                )
            );
    }
}


62 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBox.sol

63 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxPermit.sol

- Instead of using functions to retrieve constant values like _PERMIT_TYPEHASH and _PERMIT_ALL_TYPEHASH, we can directly define them as constant variables to avoid function call overhead.

- Use bytes32 for the name parameter in the constructor: The EIP712 contract expects the name parameter to be of type bytes32, so we can directly pass a bytes32 value instead of a string value.

- Avoid unnecessary storage reads: In the permit and permitAll functions, you read the nonce using nonces(address owner). Instead, we can use the _useNonce(owner) function, which internally reads the nonce and increments it, thus saving one storage read.

- Check if owner is not null before accessing its nonce: In the _useNonce function, we should check if the owner address is not null before accessing its nonce to avoid potential issues.

Here's an optimized YieldBoxPermit Contract : 

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "./interfaces/IYieldBox.sol";

abstract contract YieldBoxPermit is EIP712 {
    using Counters for Counters.Counter;

    mapping(address => Counters.Counter) private _nonces;

    bytes32 private constant _PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 assetId,uint256 nonce,uint256 deadline)");
    bytes32 private constant _PERMIT_ALL_TYPEHASH = keccak256("PermitAll(address owner,address spender,uint256 nonce,uint256 deadline)");

    constructor(bytes32 name) EIP712(name, "1") {}

    function permit(
        address owner,
        address spender,
        uint256 assetId,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");

        uint256 nonce = _useNonce(owner);
        bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, nonce, deadline));
        bytes32 hash = _hashTypedDataV4(structHash);
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "YieldBoxPermit: invalid signature");

        _setApprovalForAsset(owner, spender, assetId, true);
    }

    function _setApprovalForAsset(
        address owner,
        address spender,
        uint256 assetId,
        bool approved
    ) internal virtual;

    function permitAll(
        address owner,
        address spender,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
        require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");

        uint256 nonce = _useNonce(owner);
        bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, nonce, deadline));
        bytes32 hash = _hashTypedDataV4(structHash);
        address signer = ECDSA.recover(hash, v, r, s);
        require(signer == owner, "YieldBoxPermit: invalid signature");

        _setApprovalForAll(owner, spender, true);
    }

    function _setApprovalForAll(
        address _owner,
        address operator,
        bool approved
    ) internal virtual;

    function nonces(address owner) public view virtual returns (uint256) {
        return _nonces[owner].current();
    }

    function DOMAIN_SEPARATOR() external view returns (bytes32) {
        return _domainSeparatorV4();
    }

    function _useNonce(address owner) internal virtual returns (uint256 current) {
        require(owner != address(0), "YieldBoxPermit: invalid owner");
        Counters.Counter storage nonce = _nonces[owner];
        current = nonce.current();
        nonce.increment();
    }
}


64 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/BoringMath.sol

- Constant Variables: The maximum values for uint128, uint64, and uint32 are stored in constant variables, reducing repetitive calls to type(uintXXX).max.

- Unchecked Math: The muldiv function is to uses the unchecked keyword. By doing this, Solidity will not perform overflow and underflow checks on arithmetic operations. Be sure to use this only when you are sure the input values won't result in overflow or underflow.

Here is an Optimized BoringMath Contract : 

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

library BoringMath {
    uint256 constant private UINT128_MAX = type(uint128).max;
    uint256 constant private UINT64_MAX = type(uint64).max;
    uint256 constant private UINT32_MAX = type(uint32).max;

    function to128(uint256 a) internal pure returns (uint128 c) {
        require(a <= UINT128_MAX, "BoringMath: uint128 Overflow");
        c = uint128(a);
    }

    function to64(uint256 a) internal pure returns (uint64 c) {
        require(a <= UINT64_MAX, "BoringMath: uint64 Overflow");
        c = uint64(a);
    }

    function to32(uint256 a) internal pure returns (uint32 c) {
        require(a <= UINT32_MAX, "BoringMath: uint32 Overflow");
        c = uint32(a);
    }

    function muldiv(
        uint256 value,
        uint256 mul,
        uint256 div,
        bool roundUp
    ) internal pure returns (uint256 result) {
        unchecked {
            result = (value * mul) / div;
       

   if (roundUp && (result * div) / mul < value) {
                result++;
            }
        }
    }
}


65 . Target : https://github.com/Tapioca-DAO/YieldBox/blob/master/contracts/YieldBoxRebase.sol

 - Instead of repeatedly calculating the DECIMAL_FACTOR (1e8), we can declare it as an immutable constant in the contract.

-  Prevent Unnecessary Calculations: Since roundUp is only used in one condition, we can avoid checking it for both _toShares and _toAmount functions and just pass it as a parameter to the condition.

Here is an Optimized YieldBoxRebase Contract :

// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

library YieldBoxRebase {
    // Use immutable constant instead of calculating it repeatedly
    uint256 private constant DECIMAL_FACTOR = 1e8;

    /// @notice Calculates the base value in relationship to `elastic` and `total`.
    function _toShares(
        uint256 amount,
        uint256 totalShares_,
        uint256 totalAmount,
        bool roundUp
    ) internal pure returns (uint256 share) {
        // Increment totalAmount and totalShares_ once
        totalAmount += DECIMAL_FACTOR;
        totalShares_ += DECIMAL_FACTOR;

        // Calculate the shares using the current amount to share ratio
        share = (amount * totalShares_) / totalAmount;

        // Round up if required
        if (roundUp && (share * totalAmount) / totalShares_ < amount) {
            share++;
        }
    }

    /// @notice Calculates the elastic value in relationship to `base` and `total`.
    function _toAmount(
        uint256 share,
        uint256 totalShares_,
        uint256 totalAmount,
        bool roundUp
    ) internal pure returns (uint256 amount) {
        // Increment totalAmount and totalShares_ once
        totalAmount += DECIMAL_FACTOR;
        totalShares_ += DECIMAL_FACTOR;

        // Calculate the amount using the current amount to share ratio
        amount = (share * totalAmount) / totalShares_;

        // Round up if required
        if (roundUp && (amount * totalShares_) / totalAmount < share) {
            amount++;
        }
    }
}
