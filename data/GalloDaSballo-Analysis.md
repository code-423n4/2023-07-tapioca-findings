# Executive Summary

The Tapioca system is comprised of multiple interdependent smart-contract systems

At the highest level we can separate the system into:

Core:
  - Market -> Lending Logic
  (Includes yield box as it's underlying accounting system)
  (Assumes using Yield Box strategies that do nothing to mitigate composability risks)

Extra:
  - Tokenomics

Periphery:
  - Yield Strategies
  - Oracle
  - Swappers

The interrelation between Market, Tokenomics and Periphery has mostly to do with determining value and exchanging it, this creates additional risks at these "edges"


That said, from a thorough analysis it seems to me like the system has grown to be quite complex, with different levels of risk and attacks that can be performed

My analysis focuses on the additional risk that comes from the composability within the in-scope systems

The goal of the analysis is to show my adversarial thought process and to suggest a robust security process to ship the codebase in a state that is maintainable and safe for people to use

## Re-Audit the Market, separately as the Core Primitive

For this reason, it seems reasonable to suggest the following:

- Re-audit Market, with Oracles and Swappers separately
Iterate on the Market logic until it's extremely solid and battle tested

Once the Market logic is fully ready, adding additional pieces, such as Yield Generation becomes achievable.

Most importantly, the Market will require:
- Monitoring
- Periphery Contract Creation (optimal swaps, liquidations, MEV, arbitrage)
- Documentation and Evangelizing to ensure enough MEV actors are present to allow efficient markets

All of this to suggest that the Market aspect of the system is already "risky enough" on it's own, it would be beyond rekless for me to suggest deploying the system as a whole as of today due to how likely it is for some periphery aspects to add further attack vectors or break invariant at the core level.

## Gradually Introduce Risk, in a segregated manner

After the Market is lindy, each new token, oracle and strategy could be added separately with a capped amount to reduce risk
This addition should be looked at as a separate security exercise, which could be lead by a separate team

The extra pieces should be looked very thoroughly as to avoid adding vulnerabilities to the core.

As of today, due to the interrelation between:
- Market, Yield Box, Strategy and Pricing being so uniquely and tightly coupled means that a vulnerability at the Strategy Level (e.g. mispricing), will leak value to a degree that allows bankrupting the market

### Separate the Pricing from the Harvesting

Reducing this could be performed by separating the pricing aspect of the underlying yield strategy from the pricing of the underlying asset

An example would be spinning up an oracle for the TriCrypto Strategy while allowing the redemption of rewards as pro-rata via a SNX like contract

Another consideration is pricing of about to be harvested collateral, which is a very low hanging fruit for arbitrage and MEV


## Analysis on Test Coverage, not by line but by scenario

From skimming the tests it seems like they do cover most happy paths, but they don't seem to test for adversarial situations

For example: Loss to the strategy, DOS of the Strategy

## Systemic Risks and Privileges

The Core System does require governance for managing of collaterals, and debtRates

These seem to be part a requirement for most Lending Protocols

That said, the separation of each Collateral into separate Singularities does seem to offer a reduced risk as each pool has to be actively opted in by participants of the marketplace

When it comes to strategies on the other hand, a higher degree of trust and risks are involved

While there seems to be direct protection for the principal, the Admin Privilege of triggering Emergency Withdrawals could potentially be used to leak value, especially for strategies that are Single Sided (from Token to LP back to Token), for which Sandwiching is always possible, but FlashLoan imbalance attacks become possible mostly only to the Admin.

## Risks to end users

Main risks beside invariants being broken (Why I recommend a second audit of Core and then continous security efforts on periphery), are going to be related to composability.

The strategies integrating into different protocols create an ever moving attack surface that will require active risk management, some of which will also require understanding the tradeoff between risking the invested funds and gaining Yield.

## Concerns around the complexity of the system

The main threat I can see is how the system is being "sold as one", in the idea that all of these components will be launched together, which creates exponentially more complexity and risk than if we were dealing with each component separately.

A great example of the Sponsor understanding this was in Formally Verifying YieldBox:
- YieldBox is done
- We know the risk is at the Strategy Level
- We can focus on the rest

I believe a similar exercise could be done for BigBang and Singularity and would give a very different level of confidence in the Core of the system.

From my years of experience in DeFi I believe I have identified gotchas and risks for all the strategies, some of which just come with the territory, however, building on non fully battle-tested core, adds further uncertainty that we wouldn't have to explore if BigBang and Singularity were audited and verified independently


## Low hanging fruits for attackers and gotchas

I hope I made a clear case that the biggest area of attack is in the Periphery and in how it relates to the rest of the system

Being able to manipulate a single price feed can cause extreme damage, even when the system fully works

Collateral separation is definitely a positive there, however, some collaterals are more popular than others, which may still allow attackers to severely damage the system

On one hand this can be mitigated via having tiered amounts of capital (raise interest rates if capital goes too high)

On the other hand this is part of the bigger challenge in Decentralized Money Markets

## On Formal Verification

The fact that Formal Verification was done for YieldBox is laudable, however, it's important to keep in mind this is not a silver bullet.

Per the Certora Report, the safety and consistency of YieldBox is reliant on the correct accounting of Strategies, which were Out Of Scope at the time of Formal Verification

This further highlights how additional code doesn't just add new risks to itself, but also to more foundational code

From my perspective it's extremely important that each piece is looked at individually first and then the composability of each part is further explored.


## Process Risks

The process that has been followed seems very risky and I wouldn't be surprised if a lot of valid findings were found in the CodeArena Contest

From my experience, any contest with even one High Severity, should be followed by another contest, to make sure that the mitigations have been addressed and that no second-order consequences have been created due to minor changes.

## Ideal Process

Due to the inherently high surface area, the first set of exercises should be focused exclusively in securing the BigBang, USD0 and Singularity System

These contracts would need to be audited to ensure all invariants are addressed, and external risks should be properly modelled (e.g. Oracle as single point of failure).

### Realized Process

Only YieldBox has been Formally Verified by Certora, this means that BigBang, USD0 and Singularity haven't

This to me indicates a lower level of maturity in terms of the security of the system.

Only once these core contracts have been secured, by performing multiple audits and security contests, you should opt to perform similar level of security reviews for the Periphery and Oracle Contracts


## Balancing growth and risk

On one hand guarded launches can offer a way to reduce total value at risk, at the same time, we all know that any exploit can severely undermine the reputation of a project.

From my experience in DeFi there is no such thing as a "small review", any minor change (e.g. the Euler Change for Dust Amounts), can have dramatic second, third or even higher order impacts.

For this reason new types of oracles, and strategies should be introduced after serious scrutiny and reviews are performed, this means that no "small tweak" should ever be added.

At the same time, live monitoring, live fuzzing and a rapid response bug bounty can help mitigate actual value loss while allowing for faster experimentation


## In Summary

Unless no High Severity was found, do not limit yourself to a Mitigation Review and then launch.

The downside and the risks are too high.

Instead, consider doing an additional contest for all to participate in, and then proceed with a Guarded Launch and a Bug Bounty

Allow ample time for SRs to check your code, do not rush these phases as the downside massively outweighs the benefits of rushing

## Suggested Next Steps

Based on the findings, determine the weaker areas (periphery most likely)

Harden the Core first, as a separate security exercise

Then develop a plan to progressively stress test the periphery, while allowing safety

Due to the variability of the Strategies, you should have Active Monitoring Setup with the goal of Pausing and Deprecating Strategies as fast as possible.

### Time spent:
50 hours