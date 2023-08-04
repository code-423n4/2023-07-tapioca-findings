
### [L-01] - the _getRevertMsg() implementation could potentially cause issues during decoding

The _getRevertMsg()  function manipulates the byte array _returnData by essentially ignoring the first four bytes. However, it doesn't adjust the reported length of the byte array, potentially causing issues during decoding due to length mismatch.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L87-L98

The revised function below improves this by accurately adjusting the length of the byte array after slicing off the first four bytes. This ensures that the abi.decode operation works correctly. It then restores the original length, preserving the integrity of the byte array for any further use. This makes the revised function safer and better designed than the original.

     function _getRevertMsg (bytes memory revertData) internal pure returns (string memory reason) {
         uint l = revertData.length;
         if (l < 68) return "";
         uint t;
         assembly {
             revertData := add (revertData, 4)
             t := mload (revertData)
              mstore (revertData, sub (l, 4))
         }
         reason = abi.decode (revertData, (string));
         assembly {
             mstore (revertData, t) 
         }
     }


### [L-02] - A change in allowance (approve()) must trigger an approve event.

The EIP20 standard requires the protocol to emit an event each time a successful 'transfer()' call takes place. It would be advisable to emit an approve event right after the 'allowance[from][msg.sender] = spenderAllowance - amount;' operation within the 'transferFrom()' function of the MarketERC20 contract.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L177


### [L-03] - Consider checking for a reasonable value when assigning value to the fees state variable.

When assigning values to the "fees" state variable, the only requirement is that the fee is < 100%. This could be refined to a more reasonable range to prevent potential mistakes or protocol errors.

For instance, within the market.sol contract, given a 'collateralizationRate' of 75% as stated in the documentation, any value of liquidationMultiplier >= 33% will break the computeClosingFactor() function. Indeed, the 'denominator' memory variable will be <= 0, causing the function to always revert.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L290-L295

Therefore, consider narrowing the value range for all these fee variables.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDO.sol#L96-L100

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L142-L146

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L171-L175

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L192-L195

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L197-L200

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L202-L208

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L210-L226

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L233-L239


### [L-04] - The init() function of the BigBang contract should check the "exchangeRate" value.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L146

When the BigBank contract is initialized, the ‘updateExchangeRate()’ is called. This function is supposed to correctly update the ERC20 token exchange rate. However, as we can see in the ‘updateExchangeRate()’ function, if the ‘updated’ parameter returns ‘false’, the function doesn’t revert.  

    function updateExchangeRate() public returns (bool updated, uint256 rate) {           // verifier l'oracle
        (updated, rate) = oracle.get("");

        if (updated) {
            require(rate > 0, "Market: invalid rate");
            exchangeRate = rate;
            emit LogExchangeRate(rate);
        } else {
            // Return the old rate if fetching wasn't successful
            rate = exchangeRate;
        }
    }

In this situation, the exchange rate will remain at zero after initialization. This might trigger unexpected protocol behavior. Consequently, adding a ‘require(exchangeRate > 0)’ statement would add an additional security layer.


### [L-05] - Init() of the BigBang contract should ensure that ‘maxDebtRate > MindebtRate’

The initialization function of the BigBang contract updates ‘maxDebtRate’ and ‘minDebtRate’ state variables without checking consistency between these two values. Since the getDebtRate() function depends on the difference of these two values, it is highly advisable to check the consistency of these two values at the contract initialization.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L159-L160

### [QA-01] - Consider declaring state variables at the beginning of the contract.

For clarity, avoid declaring state variables in the middle of the contract. Consider declaring all the state variables at the beginning of the contract instead.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L129

### [QA-02] - Allowances should not be updated when their value is type(uint256).max.

If the current allowance is 'type(uint256).max', the state variable representing "Allowances" should not be updated. Indeed, when the current allowance is the maximum value, it basically means that the spender has an "unlimited spending amount". Updating the allowance of the spender after a transaction would limit this "unlimited" spending amount.

 https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L84-L91
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L75-L82
