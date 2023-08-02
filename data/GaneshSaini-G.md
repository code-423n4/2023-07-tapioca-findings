# [YieldBox.sol](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBox.sol)
-----
+ The optimization made by caching the `assets[assetId]` variable in the functions that use it multiple times leads to gas savings.
+ In the original contract, the `assets[assetId]` mapping is accessed multiple times within the same function, resulting in redundant storage reads. By caching the asset data in a local variable, the contract avoids repeated storage reads, which can lead to gas savings. This is particularly important in Ethereum smart contracts, where gas costs directly impact transaction fees and contract interactions.
+ Since mappings are essentially key-value stores, accessing a mapping directly `(assets[assetId])` involves an SLOAD operation, which can be relatively costly in terms of gas. By storing the mapping value in a local variable, eliminate the need to perform additional SLOAD operations when accessing the same mapping value multiple times within a function.

In function uri() assets[assetId] should be cached
-
      402: function uri(uint256 assetId) external view override returns (string memory) {
    +         Asset storage asset = assets[assetId];
    - 403:    return uriBuilder.uri(assets[assetId], nativeTokens[assetId], totalSupply[assetId], owner[assetId]);
    + 403:    return uriBuilder.uri(asset, nativeTokens[assetId], totalSupply[assetId], owner[assetId]);
      404: }


In function name() assets[assetId] should be cached
-
      406: function name(uint256 assetId) external view returns (string memory) {
    +        Asset storage asset = assets[assetId];
    - 405:    return uriBuilder.name(assets[assetId], nativeTokens[assetId].name);
    + 405:    return uriBuilder.name(asset, nativeTokens[assetId].name);
      406: }


In function symbol() assets[assetId] should be cached
-
      410: function symbol(uint256 assetId) external view returns (string memory) {
    +        Asset storage asset = assets[assetId];
    - 411:    return uriBuilder.symbol(assets[assetId], nativeTokens[assetId].symbol);
    + 411:    return uriBuilder.symbol(asset, nativeTokens[assetId].symbol);
      412: }

In function decimals() assets[assetId] should be cached
-
      414: function decimals(uint256 assetId) external view returns (uint8) {
    +         Asset storage asset = assets[assetId];
    - 415:    return uriBuilder.decimals(assets[assetId], nativeTokens[assetId].decimals);
    + 415:    return uriBuilder.decimals(asset, nativeTokens[assetId].decimals);
      416: }


In function assetTotals() assets[assetId] should be cached
-
       424: function assetTotals(uint256 assetId) external view returns (uint256 totalShare, uint256 totalAmount) {
     +        Asset storage asset = assets[assetId];
       425:   totalShare = totalSupply[assetId];
     - 426:   totalAmount = _tokenBalanceOf(assets[assetId]);
     + 426:   totalAmount = _tokenBalanceOf(asset);
       427: }

In function toShare() assets[assetId] should be cached
-
      334: function toShare(uint256 assetId, uint256 amount, bool roundUp) external view returns (uint256 share) {
    +         Asset storage asset = assets[assetId];
    - 335:    if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    + 335:    if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
      336:        share = amount;
      337:    } else {
    - 338:        share = amount._toShares(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), roundUp);
    + 338:        share = amount._toShares(totalSupply[assetId], _tokenBalanceOf(asset), roundUp);
      339:    }
      340: }

In function toAmount() assets[assetId] should be cached
-
      447: function toAmount(uint256 assetId, uint256 share, bool roundUp) external view returns (uint256 amount) {
    +         Asset storage asset = assets[assetId];
    - 448:    if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    + 448:    if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
      449:        amount = share;
      450:    } else {
    - 451:        amount = share._toAmount(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), roundUp);
    + 451:        amount = share._toAmount(totalSupply[assetId], _tokenBalanceOf(asset), roundUp);
      452:    }
      453: }

In function amountOf() assets[assetId] should be cached
-
      458: function amountOf(address user, uint256 assetId) external view returns (uint256 amount) {
    +          Asset storage asset = assets[assetId];
    - 459:     if (assets[assetId].tokenType == TokenType.Native || assets[assetId].tokenType == TokenType.ERC721) {
    + 459:     if (asset.tokenType == TokenType.Native || asset.tokenType == TokenType.ERC721) {
      460:         amount = balanceOf[user][assetId];
      461:     } else {
    - 462:         amount = balanceOf[user][assetId]._toAmount(totalSupply[assetId], _tokenBalanceOf(assets[assetId]), false);
    + 462:         amount = balanceOf[user][assetId]._toAmount(totalSupply[assetId], _tokenBalanceOf(asset), false);
      463:     }
      464:  }





# [YieldBoxPermit.sol](https://github.com/Tapioca-DAO/YieldBox/blob/f5ad271b2dcab8b643b7cf622c2d6a128e109999/contracts/YieldBoxPermit.sol)

In require statement try not to enter error reason strings greater than 32 bytes
-
You can (and should) attach error reason strings along with require statements to make it easier to understand why a contract call reverted. These strings, however, take space in the deployed bytecode. Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.

### In function permit()

      42: function permit(
      43:    address owner,
      44:    address spender,
      45:    uint256 assetId,
      46:    uint256 deadline,
      47:    uint8 v,
      48:    bytes32 r,
      49:    bytes32 s
      50: ) public virtual {
      51:    require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");
      52:
      53:    bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, assetId, _useNonce(owner), deadline));
      54:
      55:    bytes32 hash = _hashTypedDataV4(structHash);
      56:
      57:    address signer = ECDSA.recover(hash, v, r, s);
    - 58:    require(signer == owner, "YieldBoxPermit: invalid signature");   // error reason string 33 bytes
    + 58:    require(signer == owner, "YieldBoxPermit-invalid signature");    // error reason string 32 bytes
      59:
      60:    _setApprovalForAsset(owner, spender, assetId, true);
      61: }

### In function permitAll()

      72: function permitAll(
      73:     address owner,
      74:     address spender,
      75:     uint256 deadline,
      76:     uint8 v,
      77:     bytes32 r,
      78:     bytes32 s
      79: ) public virtual {
      80:     require(block.timestamp <= deadline, "YieldBoxPermit: expired deadline");
      81:
      82:     bytes32 structHash = keccak256(abi.encode(_PERMIT_ALL_TYPEHASH, owner, spender, _useNonce(owner), deadline));
      83: 
      84:     bytes32 hash = _hashTypedDataV4(structHash);
      85:
      86:     address signer = ECDSA.recover(hash, v, r, s);
    - 87:     require(signer == owner, "YieldBoxPermit: invalid signature");    // error reason string 33 bytes
    + 87:     require(signer == owner, "YieldBoxPermit-invalid signature");     // error reason string 32 bytes
      88:
      89:    _setApprovalForAll(owner, spender, true);
      90: }