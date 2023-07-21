This is the first time i am doing analysis.

I would like to say that i have understood the whole protocol and how it works.
The heart of protocol is yieldbox which allows to register assets and allows to provide strategies to them.
The purpose of yieldbox is to handle all deposits/withdraws of assets. I found this module to be developed very good.

Each asset in the yielbox can have a strategy which allows to earn yields for the stakers.
There are a lot of strategies in a separate module. But i have found that a lot of them are buggy and looks like they were not very well tested. Some of them handle rewards and some of them doesn't. Some of them will simply not work at all for withdrawals, like Lido strategy.

Assets that will be registered in the yieldbox are tOFT and USDO. Toft are unique, because you can transfer them to any chain you want(if it's supported haha). Same we can say about USDO. User's can use tOFT in order to borrow USDO from BigBang or Singularity.
BigBang in the protocol works as USDO minter, so it should take care about solvency of USDO. I found that in case of liquidations, these USDO wasn't burnt, which creates insolvency.
Singularity contract allows users to lend their USDO and earn interests on it. 

Another thing that Singularity allows is to use it shares to receive oTap, which allows owner to mint Tap token with discount. Later, user can use that Tap token to have governance vote. I would say that tap module is well written.

This protocol relays hardly on LZ and it will be true to say that in case if LZ will have problems, then big part of protocol will stop working. However, ability to transfer funds through the chains, to trigger different market actions on another chain are really good features.

In general i found this protocol very interesting, because it uses new approach that i have not seen often. Also, i would say that code level is actually good, except of periphery(magnetar) and strategies module.

### Time spent:
40 hours