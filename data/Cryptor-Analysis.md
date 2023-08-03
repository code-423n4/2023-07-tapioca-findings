# Tapioca DAO Analysis Report

# 1. Broader concerns

**Singularity:** There is a possibility that the Penrose team, which is responsible for managing the Big Bang, may be compromised 

**Tap-Token:** There is a possibility that there is little to no liquidity for tap token, rendering the option feature to be pointless 

# 2. Review approach

The code was reviewed by two auditors in a team effort.

First, the team worked through the documentation at https://docs.tapioca.xyz/ (top to bottom) after initially jumping into the code base, realizing that critical context knowledge was missing. Additional resources like the “[birth of twAML](https://mirror.xyz/tapiocada0.eth/aZanJAiDJOqC0dZWiM_L1Mg2mg-yHz2Nl82ALMUpxC0)” article, a [YouTube video reviewing the protocol](https://www.youtube.com/watch?v=2VSjCnjDW4Y), and the [community-developed flow-chart](https://cdn.discordapp.com/attachments/1125494082375000244/1126474249771696190/IMG_0497.jpg) were also consulted.

The individual repositories in which the audited codebase was split were approached one by one.

# 3. Obstacles during the review

## Split of the codebase

The split of the whole code base into multiple repositories made the project significantly harder to review than others. Stepping into dependencies that are imported but not in the reviewed repository was not possible using an IDE (e.g., Visual Studio Code). Having all repositories open in one IDE to enable that was also not practical, as searches would reveal the same matches multiple times across the repositories, which clutters the view.

In the end, both were combined. Having one IDE instance open for the repository in review and another one with all repositories open to jump to and step into dependencies that could not be resolved in the other IDE when required.

## Documentation out of sync with code and vice-versa

Documentation and code are out of sync concerning major concepts. To find out what the truth is to be audited, the Tapioca team had to be consulted multiple times, which impacted the audit progress due to delays until an answer was received and the necessary mental context switches to proceed with other aspects of the audit while waiting for responses.

Here are some examples:

- It is documented that for borrowing and lending, NFTs would be issued. This is no more the case.
- Liquidations feature order-book and closed liquidations. This is documented and also implemented. Order book liquidations are no more used.
- Certain YieldBox strategy contracts that are in scope for this audit were discovered to not be used after consulting with the Tapioca team.

## Missing documentation, ambiguous variable/parameter naming, and semantics

We will detail these findings in our QA report, but to give a high-level overview, here are a few examples:

- The important concept of “Rebasing” (also see “Insights and learnings”), which affects a major part of Tapioca's accounting logic, is not explained. It utilizes the terms “amount” and “share”. When naming parameters in functions, values passed as “shares” are often labeled as “amount,” which requires reverse engineering to identify what it is.
- Typos in variable names, e.g., mistyping “TVL” (total value locked) and “LTV” (loan-to-value), make code harder to understand.
- Using a modifier named “_allowedBorrow”, which is used widely in the codebase outside of a borrowing allowance context, makes code harder to comprehend.

# 4. Insights and learnings

## LayerZero

With LayerZero (which Tapioca builds on for cross-chain communication), the auditors got to know a new cross-chain infrastructure project.

Previously the team had audited Maia DAO, which is also a cross-chain protocol but which utilizes another cross-chain communication mechanism.

As LayerZero is the transport layer for Tapioca, it was important to also check for related issues. LayerZero’s documentation was studied, and https://solodit.xyz/ was consulted with the keyword “LayerZero” to find relevant issues, which returned some results.

Solodit results showed that the majority of previously found issues stem from the fact that LayerZero allows for two modes of communication, a blocking and a non-blocking one. The blocking one can be exploited (stuffed).

Since Tapioca seems to only use the non-blocking mechanism, faulty messages cannot block communication channels between individual chains.

## Singularity Strategies

Strategies are used to increase the yield for the Tapioca protocol and its users. To achieve this, parts of Singularity deposits are invested into these strategies. Many strategies were implemented (AAVEStrategy, CompoundStrategy, BalancerStrategy, …). After a thorough review of the strategies, the impression remains that this is still an immature part of the overall codebase that needs an overhaul. The code shows indicators of copy/pasting between strategies, and some strategies are not 100% correctly implemented. Please see the issue submissions and QA report for details.

## Reasons for and challenges with Rebasing

The protocol makes heavy use of the [BoringRebase](https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/libraries/BoringRebase.sol) library of [BoringCrypto](https://github.com/boringcrypto). Throughout the application, values involved in accounting are therefore either expressed as “value” or “share”. Using that library, shares and amounts could be stored in the same storage slot. According to team feedback, the rebasing approach was used to account for scenarios where tokens get airdropped or funds get directly sent to a contract. The YieldBox.sol contains helper functions to deal with conversions between amounts and shares. A downside of this approach is that amounts and shares can easily get mixed up, especially when variable and function parameter naming across the project is not consistent (e.g., a function parameter called amount, although a share was expected). [Audits of other protocols using rebasing have shown that this can lead to issues](https://github.com/sigp/public-audits/blob/master/sushi/sushi-swap-stable-pool/review.pdf) (”amountOut Not Converted to shares”, “Amount Conversion Done Twice”, “amount in _complexPath() is a Value in Shares”).

## No free Airdrops

The protocol team found an interesting answer for kickstarting protocol liquidity using airdrops without getting into a corner where they would need to constantly give away “free money”. As their research has shown, airdrops, as done by most protocols, never create long-term incentives. People would get their tokens airdropped and then sell them quickly. No long-term incentives and no loyalty to the platform are “skin in the game” is missing.

Instead of giving away tokens for free, the protocol uses its already existing oTap mechanism, where tokens can be purchased at a discount via American Style Call Options. The difference to oTap is just that the token is called aoTAP instead, and a fixed discount as well as a 48 hour time window is used to exercise the call options.

This was a very interesting learning from auditing this protocol.

# 5. Codebase Quality

| Codebase Quality Categories  | Comments  |
| --- | --- |
| Unit Testing  | Protocol was extensively tested. Each repo had a full hardhat testing suite. Also, additional testing on alternative chains was very appreciated. |
| Organization | Codebase was organized. However, core parts of the codebase were needlessly separated into separate repositories. This made the auditing process a lot more cumbersome than it needed to be and made it more difficult to achieve a high-level understanding of the protocol.  |
| Code Comments  | The comments overall were solid. However, there were a few sections where it was outdated or misleading.  |
| Documentation | The documentation on the website did not match the codebase. There were many sections in the docs that were vaguely explained or not explained at all (e.g., Yieldbox strategies). It should also be noted that the docs inside the repos that are supposed to give a more detailed explanation of contract functions are mostly incomplete. With a codebase this large, we believe that proper documentation is key to receiving higher-quality audits and also facilitating more 3rd party integration from other developers.  |
| Static Analysis  | Protocol has been tested with Slither  |

# 6. Architecture

**Singularity** 
![Singularity](https://cdn.discordapp.com/attachments/1126328619208286238/1136370098488213544/image.png) 
Link: https://media.discordapp.net/attachments/1126328619208286238/1136370098488213544/image.png?width=2080&height=1028

**Tap-Token**
![Tap-Token](https://cdn.discordapp.com/attachments/1126328619208286238/1136370280902705213/image.png) 
Link: https://media.discordapp.net/attachments/1126328619208286238/1136370280902705213/image.png?width=1176&height=1020


### Time spent:
250 hours