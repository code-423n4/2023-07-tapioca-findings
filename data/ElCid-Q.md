## L01 - ``_yieldBoxShares`` state variable is wrongly increased when repaying.

In ``SGLCommon._addTokens`` function, the variable ``_yieldBoxShares``, which keeps track of the user's ``yieldBox.balance`` that is allocated to the protocol as Collateral, is increased with ``share`` amount.
This makes absolute sense when a user is buying collateral, adding collateral or adding an asset, because ``yieldBoxShares`` are actually increased. However, when the user repays a borrow through the ``Lending.repay()`` function, ``yieldBoxShares`` remains unchanged. Because of the flow of the ``repay`` function, the ``SGLCommon._addTokens`` is eventually called and ``yieldBoxShares`` wrongly increased.
