Hello team,

## Summary :

I would like to share an issue in smart contract about unchecked Low Level Call , In Low Level Call on Failure the funds / Gas fee Never be Reverted back By Default, as we Know Low Level Calls never revered back that's why there is a Missing Require () check in Bool (success ) due to which there is a chance that Modifier condition is not 100 % accurate in terms of Low Level Calls


Link = https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/blob/master/contracts/curve/TricryptoLPStrategy.sol#L105C66-L105C76


At Line 105 ,

Here In the Code :

            (bool success, bytes memory response) = address(lpGauge).staticcall(
            abi.encodeWithSignature("claimable_tokens(address)", address(this))
        );

There is Missing require check for Bool( Success) Means on Failure the  Gas Fee will never be reverted back Automatically in Low level Call ( Static Call) ,  if the staticall() opcode is Failed then In Low Level Call transaction never be Reverted back By Default so there is a use of Require() checks once failure will occur

## What is Unchecked Returns ?

When the return value of a low level function call is not checked, the code continues execution irrespective of success or failure of the call. It can be exploited by deliberately making the function call fail and the rest of the code can be executed causing unexpected behavior.

## Low  level Contract Call issue ?

There is a Most Common Assumption in Smart contract Development that On failure we always saw a Return bit this Case Never fits on Low Level Calls

In Low Level Calls we use a Opcode() 

1. Send()

2. Call ()

3. delegatecall()

4. StaticCall()

Bydefault , Low Level Calls on Failure Never Reverted back , On Failure it return BOOLEAN ( False)


-------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------

1. Send() Opcode :

It uses a Max 2300 Gas Consumption , it not Recommended to Use it  and alwast return a Bool value 

    bool sent = payable(msg.sender).send(amount);
    require(sent , "Failure Occur");

Without Require Check it Never Reverted back 


2. Call() Opcode :

This Opcode forwards all the gas and it also set to Custom gas , This Opcode is used  to call external contract Functions 
This Opcode on Failure byDefault, return Boolean and Not revert Back

      (bool success , ) = msg.sender.call{ value : balance}(" " );
       require ( Success , " Withdraw Failed ");

If Not require check is Present the Fund will never be Reverted Back

3.  StaticCall()

This Opcode forwards all the gas and it also set to Custom gas , This Opcode is used  to call external contract Functions 
This Opcode on Failure byDefault, return Boolean and Not revert Back

      (bool success , ) = msg.sender.staticcall{ value : balance}(" " );
       require ( Success , " Withdraw Failed ");

If Not require check is Present the Fund will never be Reverted Back


## Impact :

1.  Due to not  reverting  ( byDefault on Failure )  user experience a Direct theft of  Gas Fee When  the transaction failure will occur 

2.  Loss of Gas fee due to insecure Low Level Call functionality


## References :

1. Official Ethereum = https://github.com/ethereum/solidity/issues/7455

2. Link = https://swcregistry.io/docs/SWC-104

## Recommendation :


I will definitely suggest developer to impose a require check in Low level Call once Failure of transaction will occur

  
       (bool success , ) = msg.sender.staticcall{ value : balance}(" " );
       require ( Success , " Withdraw Failed ");


Thank You



