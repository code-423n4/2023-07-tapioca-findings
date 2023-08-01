tapioca-bar was my main focus. It appears to be the center or core aspect of the protocol. I spent some time on Yieldbox, strategy and periph--ordered by attention spent--because they were very integrated with Markets and USD0 in tapioca-bar.

It was an interesting project and my initial target was liquidation. It appeared to be simple and not a lot of opportunity for attack, but on the quest to understand the protocol, I had to understand what each unit means.

There are shares, fraction, amount, base, elastic and sometimes, the same name mean different things. And that was also where I started noticing irregularities in their usage and most of the bug I found stem from it.

Another thing I wish the protocol did better is documentation. Most internal functions are crucial, they are meant to do one thing, accept a particular type of unit, but these are not documented enough. I had to do a lot of tracing and I suspect if emphasis were made on documentation, there might have been less bug.

One major example is the [_getCallerReward](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/2286f80f928f41c8bc189d0657d74ba83286c668/contracts/markets/Market.sol#L442-L462) where `borrowed`, `startTVLInAsset`, `maxTVLInAsset` are supposed to be in the same unit, but the calls to `_getCallerReward` didn't do that right. `borrowed` could also be changed to `borrowedAmount` cause it would be more clear whether it is `borrowed amount` or `borrowed share`.

Some suggestion to improve the documentations include
* Write simple math formula for what you're trying to achieve
* Write the behavior you expect. Some behavior are like curves that change based on input
* Be more specific about the unit such as base vs elastic, shares vs amount

Although I didn't find high or mediums related to liquidations, I have some opinion about the design which are highlighted in QA report.


### Time spent:
58 hours