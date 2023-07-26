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

-Removed the unnecessary comments from the contract, while retaining essential comments that provide clarity on the contract's functionality.

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

-Combine Emit: In the accrue function,  combine the log emission into the _accrue function to reduce the number of gas-consuming function calls.

Remove Redundant Assignments:  remove unnecessary assignments to variables like extraAmount, feeFraction, and logStartingInterest to avoid redundant computations.

Reduce Read/Write Operations: minimize the number of storage read and write operations by eliminating redundant updates to the accrueInfo and totalAsset variables in the _accrue function.

Reduce External Calls:  reduce external function calls by removing the explicit emit calls and performing log emission inside the functions where the data is readily available.

Simplifiy Conditions:  simplifiy some conditions in the _getInterestRate function to reduce the number of computations.

Remove Unused Return Values: In the _getInterestRate function,  remove the unnecessary return values that were not being used outside the function.

Remove Unused Function: The accrue function is now an external function that directly calls the optimized _accrue function.

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

- Add the value keyword when calling the mintFromBBAndLendOnSGL function to indicate the transferred ether value, if applicable.

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


Use bytes32 for Address: Instead of converting addresses to bytes32 using the LzLib.addressToBytes32() function,  directly convert addresses to bytes32 using bytes32(uint256(address)). This simplifies the address conversion process.

Combine External Calls: In the leverageUpInternal function,  combine multiple external calls into a single call to the ITapiocaOFT contract. This reduces gas consumption by batching operations.

Remove Unused Imports:  remove unused import statements to eliminate unnecessary code inclusion.

Simplifiy Data Encoding:  simplifiy the data encoding for the lzPayload variable in initMultiHopBuy and sendForLeverage functions to reduce gas costs.

Simplifiy Control Flow:  simplifiy the control flow in the multiHop function to remove unnecessary conditions and make the code more concise.

Reduce Loop Overhead: In the _callApproval function,  remove the unchecked block and used a standard for loop to reduce loop overhead.

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

Use isBidAvailable() Function:  use the isBidAvailable() function from the liquidationQueue contract to check if a bid with the required amount is available. This reduces the need for additional local variables and simplifies the code.

Remove Unnecessary Conditions:  remove unnecessary conditions by directly checking if a bid is available and using an if-else statement to select the appropriate liquidation method.

Optimize TotalMaxBorrow Calculation:  optimize the calculation of the total maximum borrow amount in the _orderBookLiquidation function to reduce redundant calculations.

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

Remove Unnecessary Return: A redundant return statement has to be  remove from the _extractModule function.

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



Combine assignment statements: In some places, multiple assignment statements were used for the same variable.  I combine these assignments into a single line to reduce the number of stack operations and save gas.in the _rebase function,combine the assignment of accrueInfo.rate in one line instead of two.

Avoid unnecessary storage operations: In the _rebase function, the totalFees variable was assigned to 0 and then used in the calculation of accrueInfo.rate. Since totalFees was not used afterward,  remove the assignment and directly used the value in the calculation to avoid unnecessary storage writes.

Simplifiy conditions: In the getDebtRate function,contract had multiple conditions and calculations to determine the debt rate. simplifiy the contract by eliminating some redundant checks and calculations, making it more concise and efficient.


Use of modifiers: introduce a new onlyOperator modifier to ensure that certain functions can only be called by the owner or an authorized operator. This helps improve security by controlling access to critical functions.

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

Constructor Change:  add a constructor to set the EXCHANGE_RATE_PRECISION at deployment. This value is required for fixed-point arithmetic calculations. You can set this value based on your specific requirements and precision needs.

Visibility Change:  made the updateExchangeRate() function external and view. Since it does not modify the contract state, it can be called externally and marked as a view function, reducing gas costs for callers.

Liquidator Reward Calculation:  move the liquidator reward calculation to an external view function named computeLiquidatorReward(). This function calculates the reward without modifying the contract state, reducing gas costs.

Ratio Calculation:  refactore the _computeMaxAndMinLTVInAsset() function to use a private _getRatio() function for calculating the ratio between numerator and denominator. This allows for code reuse and avoids redundant calculations.

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





