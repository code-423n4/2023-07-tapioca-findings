
### During audit I threw all my efforts to audit tap-token-audit repository. Then I considered tapioca-bar-audit.


# Codebase analysis 

First moment that I've noticed is -- `Vesting` is a simplified copy of openzeppelin tokenVesting: https://github.com/utgarda/openzeppelin-solidity/blob/master/contracts/drafts/TokenVesting.sol

Authors wanted keep only required functionality. From one side -- that's good solution, because deploy will be cheaper, but from the other side you need to cover code with tests, and consider all cases. 

So, my proposal -- reuse code, always when it is possible.

Next moment I noticed, and I think it's important to highlight -- excessive use `LayerZero`. For example `USDO` and modules always uses `LayerZero` to interact with. 
__Even if interaction happens with the same chain.__

I dived into `LayerZero` and figured out that even __if message wanted to be delivered at the same chain (i.e. source chain = dest chain) will be used whole process flow.__ Event will be emitted, processed by nodes, and called by `LayerZero` operator. This might be a point of failure. If `LayerZero` will be hacked and stopped -- your protocol will be stopped too.  

Another one moment to notice -- constant, use constant everywhere it possible.
For, example all 18 might be replaced with constant TOKEN_DECIMALS, and so on...


# Architecture proposals 

## Distinguish business logic from math logic  
Move out `FullMath` from parent contract to library. Because it's not a good way to composite functions which do some math stuff with functions which do business logic.
Same for `TWAML` it is better to be a library not contract which might be inherit.

## Write info comments for functions
Now using some functions not clear, what's purpose of them.

# Centralization risks

All functions which should be guarded -- already guarded, you as user may enter and exist in any position, and this won't be dependent on owner desire. 

 
 







### Time spent:
160 hours