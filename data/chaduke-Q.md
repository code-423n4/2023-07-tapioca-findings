QA1. MarketERC20.transfer() uses the wrong condition at L140. The correct condition should be ``amount != 0 && msg.sender != to``. In addition, the emit statement should be in the if-block so that false event will not be emitted. 

[https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135-L152](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/MarketERC20.sol#L135-L152)

Correction:
 
```diff
 function transfer(
        address to,
        uint256 amount
    ) public virtual returns (bool) {
        // If `amount` is 0, or `msg.sender` is `to` nothing happens
-        if (amount != 0 || msg.sender == to) {
+        if (amount != 0 && msg.sender != to) {

            uint256 srcBalance = balanceOf[msg.sender];
            require(srcBalance >= amount, "ERC20: balance too low");
-            if (msg.sender != to) {
                require(to != address(0), "ERC20: no zero address"); // Moved down so low balance calls safe some gas

                balanceOf[msg.sender] = srcBalance - amount; // Underflow is checked
                balanceOf[to] += amount;
-            }
+           emit Transfer(msg.sender, to, amount);
        }
-        emit Transfer(msg.sender, to, amount);
        return true;
    }
```