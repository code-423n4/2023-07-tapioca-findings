## [NC-1] Implement a getter function for `Vesting.sol#_totalAmount` private state variable

The `_totalAmount` maybe fetched by the frontend or monitoring tools. Since the `_totalAmount` state variable is private, consider implementing a getter function for it.

File: https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/Vesting.sol#L40

#### Recommended Mitigation Steps
Implement a getter function for the `_totalAmount` private state variable
```
function getTotalAmount() external view returns(uint256 total) {
 total = _totalAmount;
}
```

## [NC-2] Avoid variable shadowing.

1. the `NativeTokenFactory.sol#createToken().uri` parameter shadows the `ERC1155.uri` state variable.
File: https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/NativeTokenFactory.sol#L96

2. The `OFTV2.sol#decimals` local variable in the constructor shadows `ERC20.decimals` state variable.

```
constructor(string memory _name, string memory _symbol, uint8 _sharedDecimals, address _lzEndpoint) ERC20(_name, _symbol) BaseOFTV2(_sharedDecimals, _lzEndpoint) {
        uint8 decimals = decimals(); //@audit shadowing decimals
        require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");
        ld2sdRate = 10 ** (decimals - _sharedDecimals);
    }
```

#### Recommended Mitigation Steps
Rename the variables to avoid variable shadowing.

## [NC-3] No validation for `_expiry` in `oTap.sol#mint()` function
The `_expiry` can be set to a past timestamp due to lack for input validation

File: https://github.com/Tapioca-DAO/tap-token-audit/blob/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f/contracts/options/oTAP.sol#L106

```
/// @notice mints an OTAP
    /// @param _to address to mint to
    /// @param _expiry timestamp
    /// @param _discount TAP discount in basis points
    /// @param _tOLP tOLP token ID
    function mint(
        address _to,
        uint128 _expiry,
        uint128 _discount,
        uint256 _tOLP
    ) external onlyBroker returns (uint256 tokenId) {
        tokenId = ++mintedOTAP;
        _safeMint(_to, tokenId);

        TapOption storage option = options[tokenId];
        //@audit no validation for _expiry
        option.expiry = _expiry;
        option.discount = _discount;
        option.tOLP = _tOLP;

        emit Mint(_to, tokenId, option);
    }
```
#### Recommended Mitigation Steps
Add validation to ensure that `_expiry` is atleast greater than block.timestamp.

## [NC-4] Transfer and the transferFrom functions of the BigBang.sol are not implemented.
File: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L426-L435

```
function transfer(
        address to,
        uint256 amount
    ) public override returns (bool) {}

    function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public override returns (bool) {} //@audit transfer not implemented.
```

#### Recommended Mitigation Step
IMplement the transfer and the transferFrom functions

## [NC-5] BigBang.sol#init() function can be frontrun.
File: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L101

## [NC-6] The `compund()` function in the `compoundStrategy.sol#emergencyWithdraw()` function does nothing
File: https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/05ba7108a83c66dada98bc5bc75cf18004f2a49b/contracts/compound/CompoundStrategy.sol#L101

#### Recommended Mitigation steps 
Refactor the compound function or remove it since it does nothing.