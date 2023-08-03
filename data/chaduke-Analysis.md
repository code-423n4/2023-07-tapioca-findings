Any comments for the judge to contextualize your findings
1) My strength is math and logic. This includes slippage control, rounding erros, and the conditions for if-statement and require statement.
2) I am also interested in findings that lead to DOS or malfunction of some contracts.
3) For stealing/losing funds, I will usually provide the POC code for demonstration.
4) I have focused on the interactions between various strategies and YieldBox.



Approach taken in evaluating the codebase

I have taken three approaches for evaluating the codebase:
1) Organize all files into parent and child contract diagrams so that related contracts can be studied together;
2) Study various flows, and for each flow, understand step by step how the logic works. Piece together flow by flow to form a global view of the codebase.
3) Start to write test, mock code and experiment with the execution of different flows. Use console2.log alot fo print out intermediate and final variable values to study the execution of various flows.
4) Write down any red flags for more careful study.




Architecture recommendations
    1) I would suggest to have a architecture diagram for the project. For example, all the strategies and how they relate the YieldBox contract can be visualized more clearly.
 
    2) There are some conflicts in definitions of IERC20, IERC721, and IERC1155 between @boringcrypto and @zeppelin. I renamed them to IMyERC20, IMyERC721, and IMyERC1155 for @boringcrypto.

    3) Currently, most strategies are not fault-tolerant in the sense that if the underlying vault/pool is paused, then the whole contract will not work. Nobody can depoit the asset into the strategy. (for example, cether was frozen for one week here [https://www.coindesk.com/markets/2022/08/30/market-for-compound-ether-token-frozen-after-code-bug-kills-price-feed/](https://www.coindesk.com/markets/2022/08/30/market-for-compound-ether-token-frozen-after-code-bug-kills-price-feed/)). Ideally, a user should still be able to deposit/withdraw even though the underderlying vault/pool is paused.


Codebase quality analysis
    1) The quality for strategy is high as there is a good abstraction of BaseERC20Strategy, BaseERC1155Strategy, etc.

 


Centralization risks
    1) Many strategy methods have the access control of onlyOwner, for example, setMultiSwapper() and setTricryptoLPGetter(), when set improperly, can lead to the malfunction of the contract or loss of funds. It is recommended that these swapper, and lpGetter should be fixed after deployment. If upgrade is necessary, use the proxy upgrade patterns.
    2) emergency withdrawal for strategies only withdraw the funds back to the strategy contract from underlying vaults/pools. In this case, it should allow a multi-sig contract to withdraw the funds for safety reasons.

    


Mechanism review

Systemic risks
