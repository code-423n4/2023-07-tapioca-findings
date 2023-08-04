# Tapioca DAO Analysis

## Cross-Chain Communication Analysis

TapiocaDao utilizes LayerZero to enable cross-chain messages.
When it comes to sending cross-chain messages there are a couple of things that matter:

1. UserA’s message shouldn’t be able to affect message delivery for other users.
2. Protocol should minimize the gas costs for users while ensuring that cross-chain transactions don’t revert.
3. In case a transaction reverts while being submitted by a Relayer there should be an easy way of retrying the transaction.

I believe the codebase has partially failed in addressing all three objectives. I will elaborate on all three points above with recommendations on how to improve.

### 1. Liveliness of the protocol

I’ve named the first point liveliness since I believe if a malicious user can disable the message pathway for longer or shorter periods of time this is a crucial property and should be treated with care.

As pointed out in my issue on [minimum gas](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1207), the [issue with 63/64 gas forwarded and variable costs of saving payload](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1220) the system can be easily manipulated and disabled.

The objective of the design should be that every message enforces some minimum gas which ensures that no one can block the pathway in any way.

What makes minimum gas hard to determine is branching in the logic or saving different sizes of payloads so these points should be identified and estimated.
The minimum gas configured for all packets should always be the maximum gas that can occur. 
And by design, the difference between the minimum and maximum gas costs should be as minimal as possible because users would be paying the max each time(it is enforced).

This should ensure the liveliness of the protocol.

### 2. User gas costs

Ensuring no one can affect the liveliness of the protocol mentioned above but at the same time-saving user's money through adequate gas restraints are two opposing forces.

With the current structure when a message is delivered everything is wrapped in [one big/try catch](https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/usd0/modules/USDOOptionsModule.sol#L174) inside the modules. This is the approach that was used for all the packetTypes in `TOFT` and `USDO`.

In many instances, the big `try/catch` includes:

- Logic to swap/leverage/mint or some other actions on YieldBox and Markets(BigBang/Singularity).
- A message sent to another chain.

Big `try/catch` is a convenient way of handling things but makes the usability of the app in question.

I’ve already mentioned this in my issue on [Loss of user funds](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1302) whereby the packet delivery should be at least split into lzReceive + any logic on chain + lzSend.

The same should apply to any logic which is highly likely to fail just because it takes some time for the Relayer to submit the transaction (1-5 min or more), and then that part of the logic should be in a `try/catch` so at least other changes go through.
Consider using something like this: https://github.com/weiroll/weiroll which would chain multiple operations, and maybe in some cases, you would want to revert the final action if the previous one failed, in other cases, you would want to continue.

Another part of the issue is that in many instances if the message delivered fails the [user losses all the funds it has airdropped on the remote chain](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1300).
A much better design would be that in case of a failure the airdropped amount on the remote is being refunded to the user’s address.

Another option would be that for each user the first time when their message lands on a particular remote chain the airdropped value and all the tokens are escrowed in his user-specific contract first and then all the subsequent actions are executed.
For this to be cost-effective something like [Minimal Proxy](https://eips.ethereum.org/EIPS/eip-1167) can be used, and a new clone can be deployed for each user, while the implementation would be something simple.

The flow can be the following:

1. User sends a message to the remote chain.
2. Inside the `lzReceive` first thing that happens is the minting of TOFT/USDO tokens to his user-specific contract.
3. This part should be extremely gas manipulation resistant and each and every transaction should at least reach this state.
4. After that minting/redeeming/leveraging/swapping etc. can occur.
5. Finally, the transfer of tokens or the final message is sent out to the remote chain if that is part of the flow.
6. Step 5 doesn't cause a revert of step 4, only in packets where it is necessary.
7. If step 5 fails the user can invoke it again.

### 3. Inheriting LayerZero contracts

I've named the third architectural issue as inheriting LayerZero contracts because a lot of issues stem from inheriting their contracts and taking the logic they have implemented as a given.

LayerZero is a generic message delivery protocol, and they have created these example contracts(`NonBlockingLzApp`, `LzApp`, `OFTV2`, etc.) for protocols to have some guidance when they are implementing the system on top of that.
If we take the example of [`failedMessages`](https://github.com/Tapioca-DAO/tapioca-sdk-audit/blob/90d1e8a16ebe278e86720bc9b69596f74320e749/src/contracts/lzApp/NonblockingLzApp.sol#L18) as it is implemented by LayerZero that logic allows the user to only retry the message with the exact same payload.
This might work in some cases where the logic is simple but in the case of TapiocaDAO's contracts the logic is complicated and I think that you should override that behavior and store messages differently. This was also elaborated in my issue on [loss of user funds](https://code4rena.com/contests/2023-07-tapioca-dao/submit?issue=1302).

In reality, to receive a message on the remote chain you only need to have a contract that implements the [`lzReceive`](https://github.com/LayerZero-Labs/LayerZero/blob/main/contracts/Endpoint.sol#L118) interface, everything else should be taken with a grain of salt and custom adapted to the specific needs of a project.


### Time spent:
180 hours