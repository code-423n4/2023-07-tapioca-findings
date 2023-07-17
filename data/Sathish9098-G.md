
## Calldata should be used instead of memory for external 

## Calldata pointer is used instead of memory pointer for loops or not modified 

## Don't emit state variable 

## remove multiple emit use combined emit 

## Don't initialize the variable 

## Use immutables for unchanged values instead of external calls 

## Cache state variables outside of loop to avoid reading/writing storage on every iteration

## Cache external calls outside the loop

## Optimize names to save gas

## if blocks should be separate loops 

## A mapping is more efficient than an array

## Using storage instead of memory for structs/arrays saves gas

## Structs can be packed in fewer slots 

## State variables can be packed in fewer slots 

## State variable only set in constructor can be immutable 

## State variable can be chached with stack varibale 

## Multiple access of mapping/array can be cached 

## Result of function calls should be cached 

## The condition check should be top of the functions 

## Use assembly to perform efficient back-to-back calls

## Check from protocol any ways to saves gas 

## Transfer of 0 should be checked 

## Private or modifiers only called once can be inlined 

Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard

Using true and false will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using 1 and 2 (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

## Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it)

