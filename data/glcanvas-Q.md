# [L-01] Fix typos
A lot of typos, fix it, please, some examples:
```
/// @dev Like all oracle contracts, this contract is an instance of `OracleAstract` that contains some
//not costant, but can only be set in the 'init' method
/// @param swapData Swap necessar data
/// @notice withdraws everything from the strategy
```

# [L-02] Write comments and explanation for untypical functions
For example:
```
    function _getRatio(
        uint256 numerator,
        uint256 denominator,
        uint256 precision
    ) private pure returns (uint256) {
        if (numerator == 0 || denominator == 0) {
            return 0;
        }
        uint256 _numerator = numerator * 10 ** (precision + 1);
        uint256 _quotient = ((_numerator / denominator) + 5) / 10;
        return (_quotient);
    }
```
Is really unclear.

# [L-03]
Multicall3 doesn't support failed transactions.

In code defined, and can be passed flag -- `allowFailure` 
```
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

```

but in code this doesn't handled anyway. 
```
Result memory result = returnData[i];
            calli = calls[i]; // todo doesn't supported allowFailure

            (result.success, result.returnData) = calli.target.call(
                calli.callData
            );
            if (!result.success) {
                _getRevertMsg(result.returnData);
            }
```
Please, implement this feature

# [L-04] Confusing comment
In MarketERC20
```
function transfer(
        address to,
        uint256 amount
    ) public virtual returns (bool) {
        // If `amount` is 0, or `msg.sender` is `to` nothing happens
        if (amount != 0 || msg.sender == to) {
            uint256 srcBalance = balanceOf[msg.sender];
            require(srcBalance >= amount, "ERC20: balance too low");
            if (msg.sender != to) {
                require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas

                balanceOf[msg.sender] = srcBalance - amount; // Underflow is checked
                balanceOf[to] += amount;
            }
        }
        emit Transfer(msg.sender, to, amount);
        return true;
    }
```

here told:
```// If `amount` is 0, or `msg.sender` is `to` nothing happens```

But if `msg.sender == to` then internal checks happens, so this comment should be replaced with
```// If `amount` is 0, or `msg.sender` is not `to` nothing happens```

# [L-05] Add indexed
```
event Liquidated(
        address liquidator,
        address[] users,
        uint256 liquidatorReward,
        uint256 protocolReward,
        uint256 repayedAmount,
        uint256 collateralShareRemoved
    );
```
Add indexed to `liquidator`.

# [L-06] Add events when config changed

In Marker.sol in function `setMarketConfig` emit event every time when some value changed, like here:
```      
  if (_callerFee > 0) {
            require(_callerFee <= FEE_PRECISION, "Market: not valid");
            callerFee = _callerFee;
        }
```

# [L-07] Improve code readability

In Market.sol
```
        uint256 numerator = borrowPartScaled -
            ((collateralizationRate * collateralPartInAssetScaled) /
                (10 ** ratesPrecision));
``` 
Is the same as
 ```
        uint256 numerator = borrowPartScaled - liquidationStartsAt;
```

# [L-08] Use constants definition for constant 
Let's consider BigBang.sol:
```
_accrueInfo.debtRate = uint64(annumDebtRate / 31536000); //per second
```
use constant instead of `31536000` -- like `ONE_YEAR_IN_SECONDS`

```
        extraAmount =
            (uint256(_totalBorrow.elastic) *
                _accrueInfo.debtRate *
                elapsedTime) /
            1e18;
``` the same -- may use `ONE_TOKEN`;

# [L-09] Remove Test contract
In tapioca-bar-audit
remove:
```
contract Test
```

# [L-08] remove unused variable
In `Vesting.sol` exists `UserData` storage where defined `revoked` variable, this variable is unused, please remove it.


# [L-09] No need to Mint event
In `twTAP` contract exists a comment: `// TODO: Mint event?` -- Not need to Mint event, it's already exists -- this is a `Transfer` event which is emitted in `_safeMint(_participant, tokenId);`

# [L-10] Impossible to delete rewardTokens
In `twTAP` exists `rewardTokens` and already everything ready to implement token removing, please implement it

# [L-11] Use week definition
instead of
`uint256 public constant WEEK = 604800; ` use `7 days`

# [L-12] Use constant
```
            _mint(_contributors, 1e18 * 15_000_000);
            _mint(_earlySupporters, 1e18 * 3_686_595);
            _mint(_supporters, 1e18 * 12_500_000);
            _mint(_lbp, 1e18 * 5_000_000);
            _mint(_dao, 1e18 * 8_000_000);
            _mint(_airdrop, 1e18 * 2_500_000);
```
define 1e18 as constant like SINGLE_TOKEN

# [L-13] Change revert message
In `TapiocaOptionLiquidityProvision` in function `unlock` `require(_exists(_tokenId), "tOLP: Expired position");` This meaning -- `tokenId` DOES NOT EXISTS, not Expired, so change text message to "Token doesn't exists"

# [L-14] Change signature
In `TapiocaOptionLiquidityProvision` -- `setSGLPoolWEight` replace to `setSGLPoolWeight`

# [L-15] remove unused function 
Please remove -- `function _callApproval(ICommonData.IApproval[] memory approvals) private {`

# [L-16] Misleading comment
```
/// @notice Record of participation in phase 2 airdrop
    /// Only applicable for phase 2. To get subphases on phase 2 we do userParticipation[_user][20+roles]
    mapping(address => mapping(uint256 => bool)) public userParticipation; // user address => phase => participated
```
That's not true -- because userParticipation used in phase 2 and phase 3, please fix comment.

# [L-17] Misleading comment
`uint64 public epoch; // Represents the number of weeks since the start of the contract` -- but here epoch period is 2 days, not week. 