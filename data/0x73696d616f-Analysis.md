## Context
Smart contract auditor for 1 year, have worked with LayerZero before and know their code very well. Have also audited cross chain protocols with similar functionalities to Tapioca.

## Approach taken in evaluating the codebase
Skimmed through the documentation to get an overall overview of the protocol. Then, took a deep dive on one of the modules, `tapiocaz-audit`, as I did not have time to audit the full codebase.

## Architecture recommendations
Overall, the code is well modularized and the logic is split in correct modules. However, the modules itself are smart contracts which are then delegate called to. It would make more sense in this case to write libraries, as this is their exact purpose.

## Codebase quality analysis
The codebase in the `tapiocaz-audit` repository is very raw and requires a lot of testing. Some functionalities do not actually work and very little consideration was taken regarding security.

## Centralization risks
Tapioca mentions in the documentation that their oracle is Chainlink and they are building their own relayer, so in the cross chain messages at least Chainlink would have to collude, which is unlikely. 

However, the `mTapiocaOFT` rebalance mechanism is `owner` permissioned, and it allows the owner to extract the underlying asset at any time. The owner just has to set its address as a `balancer` and then  call `extractUnderlying()` directly.

## Mechanism Review
Again, only mentioning the `tapiocaz-audit` repository.

Tapioca controls the `TapiocaWrapper`, which does permissioned calls to the `(m)TapiocaOFT` contracts and creates new ones via `createTOFT()`.

`BaseTOFT` is inherited by both `(m)TapiocaOFT` contracts and has the logic for the `wrap()` and `unwrap` of the underlying asset and cross chain messaging functions. The cross chain functions are split into several modules to which the `BaseTOFT` delegate calls to.

`TapiocaOFT` only wraps and unwraps the underlying asset and enables the previously mentioned cross chain functionalities. `wrap()` and `unwrap()` are only possible in the host chain, which is the blockchain where the underlying asset is native.

`mTapiocaOFT` has the same functionalities as `TapiocaOFT`, with the addition that it can be unwrapped in more than only the host chain. This is due to the fact that some native tokens are native for several different blockchains, such as `ETH` and Ethereum and Optimism. This means that a rebalancing mechanism must be added to be able to have liquidity in all the chains. At the moment this poses the biggest centralization risk and should be complicated to correctly deal with.

## Systemic risks
1. Chainlink and Tapioca colluding would enable infinite mint of `(m)TapiocaOFT` tokens.
2. Rebalance mechanism allows `Tapioca` to pull the rug.
3. If one of the strategies liquidity is lost, so will be many users wrapped assets.
4. Leverage module is vulnerable to a `Swapper` malfunctioning.

### Time spent:
20 hours