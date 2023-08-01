# [G-01] Move up require condition to save a bit of gas

In `TapiocaOptionBroker` In function `getOTCDealDetails` move `require(isPositionActive, "tOB: Option expired");` right after definition, to save gas, because if requires fails, we spend gas to load data from storage and so on. 

# [G-02] `registerUserForPhase` might cost a lot of gas

In function `registerUserForPhase` we need to manually fill users rewards. If users amount is huge -- this will cost a lot of gas, better use merkle tree to shift the gas expenses to the user. The user and the number of tokens must be stored in the tree.