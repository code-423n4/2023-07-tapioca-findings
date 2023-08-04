### [G-01] "Indexing" variables inside an Event (up to 3 indexes) saves GAS.

Using the indexed keyword for value types such as uint, bool and address saves gas costs. Consequently, from a gas optimization point of view, it is always recommended to use the indexed keywords for up to three variables. However, this is only the case for value types, whereas indexing bytes and strings are more expensive than their unindexed version. The gas cost difference is on average -65gas per indexed keyword.


https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L52

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L54

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L56

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L58

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L60

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L62
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L64

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/BaseUSDOStorage.sol#L66

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L66-L70

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L90-L113

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L63-L96

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/singularity/SGLStorage.sol#L70-L138



### [G-02] - Checking twice the same condition costs gas

Instance 1 : 

Within the BaseUSDO.sol contract, the ‘require(token == address(this), "USDO: token not valid");’ statement inside the flashLoan() function can be removed since this is already checked indirectly when the function calls flashFee().

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/usd0/USDO.sol#L87

Instance 2 : 

Within the _liquidateUser() function of the BigBang contract, the following condition check can be removed : ‘if (_isSolvent(user, _exchangeRate)) return;’
Indeed, the _liquidateUser() function is a private function that is only called by the _closedLiquidation() that already check for this solvency condition.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L567



### [G-03] - Avoid ‘if’ condition that are already mutually exclusive 

Inside the ‘transfer()’ function of the MarketERC20.sol contract, the ‘if (msg.sender != to)
‘ can be removed since it will always be false given the parent if statement: if (amount != 0 || msg.sender == to) {}.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L140-L143

### [G-04] - To save deployment costs, use already existing function instead of rewriting code 

Inside the Market.sol contract, the ‘setMarketConfig()’ function could be simplified by directly calling existing functions : 

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L228-L231
—> could be replace by ‘setBorrowCap()’

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L171-L175
—> could be replace by ‘setBorrowOpeningFee()’

### [G-05] - Avoid performing multiple time the same computation inside the same function to save gas.

Withing the computeCLosingFactor() function inside the Market.sol contract, the following expression is computed twice : ‘(collateralPartInAssetScaled * collateralizationRate) / (10 ** ratesPrecision);

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L283-L284

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L288-L289
’

### [G-06] - Avoid useless computation and variable declaration inside function to save gas


Instance 1 ) 

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L730-L736

Within the _repay() function of the BingBang contract : 

      uint256 toWithdraw = (amount - part);
      uint256 toBurn = amount - toWithdraw;
       yieldBox.withdraw(assetId, from, address(this), amount, 0);
       //burn USDO
       if (toBurn > 0) {
            IUSDOBase(address(asset)).burn(address(this), toBurn);
        }

Given that ‘toWithdraw’ is only used to compute ‘toBurn’ and given that ‘toBurn = part’, This bunch of code could be simplified to: 
        
     if (part > 0) {
            IUSDOBase(address(asset)).burn(address(this), part);
     }


Instance 2) 

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/bigBang/BigBang.sol#L762-L766

Within the _borrow() function of the BingBang contract, the ‘share = yieldBox.toShare(assetId, amount, false);’ is necessary since it can be directly retrieved as output variable from the previous line : ‘yieldBox.depositAsset(assetId, address(this), to, amount, 0);
’



### [G-07] - Avoid updating ‘allowance’ state variables after each spending if current allowance is max of uint256.

When working with ‘allowance’ functions. There is no need to update the ‘allowance’ state variable if the current allowance is ‘type(uint256).max’.
This will avoid one sstore and reduce gas costs.

https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L84-L91
https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L75-L82

Consider adding the following if statement and caching the allowanceBorrow[from][msg.sender] value into memory to save gas.

     if(allowanceBorrow[from][msg.sender] != type(uint256).max) {
         allowanceBorrow[from][msg.sender] -= share; 
     }
