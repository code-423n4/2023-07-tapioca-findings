
### Approach

I read through each part of `Tapioca` one-by-one starting from the core building blocks until I had a comfortable understanding with each one: this probably took 3-5 reads of each part of each contract, I tracked my level of understanding for every contracts in Notion. I read out-of-scope files as well to broaden my understanding of the protocol: e.g. first I spent a lot of time understanding how `LayerZero` works. Often, but not always I read and run the hardhat tests of the protocol.

In the meanwhile when I spotted a potential issue, I marked it with a comment and also saved it externally so later I have a reference to it and doesn't need to find it in the codebase. I tried to find relevant audit reports and issues to `Tapioca` and read them all: e.g.: I read `Radiant v2` audit reports since it's a similar omnichain money market. I searched to find vulnerabilities that are similar to what I found in `Tapioca` in audit reports. 

Yieldbox strategies seemed like the most vulnerable part of the protocol so that's where I dedicated the most time to find vulnerabilities after I had a good understanding. I often read and traced an external protocols code and documentation to know how it works with Tapioca and if the integration is vulnerable. 

After I had a quite good understanding of a `Tapioca` and marked a lot of potential issues I started going through all of them and exploring each one individually in more detail. I set up Foundry test repositories and when I wasn't sure of a finding and tested it, especially in Yieldbox strategies I spent a lot of time with Foundry mainnet fork tests.

### Time spent
136 hours

Reading relevant reports or other forms of research related to `Tapioca` was counted as well. I worked from the contest's start at Jul 5 till the end on Aug 4. I usually worked in 90 minute work blocks with rests in between: most days I did 3 blocks, some days more. I would say my single focus for the month was the contest.


### Small notes & learnings
**LayerZero**
- how messages are sent and received: `_lzReceive()`, `send()`
- LZ endpoints, `trustedRemote`
- `LzApp`: blocking, non-blocking
- `OFTv2`: omnichain fungible tokens, `TAP` tokens and `USDO` is built on
- `ONFT721`: omnichain NFTs, `oTAP` and `twTAP` is bult on
- failed message are stored, default behaviour of relayer: e.g. reverts above 10_000 bytes sent data

**YieldBox**
- isolated yield strategies ->  isolated risk
- how `deposit` and `withdraw` works
- registering `NativeToken` and `Asset`
- `ERC1155` and `ERC721` functionality
- rebase: relationship between `elastic`, `base` and `total`
- difference between `amount` and `shares`

**BigBang**
- seperate market pairs to mint `USDO`
- creates and backs the peg of `USDO`
- collateral debt ratio (collateral assets debt ratio against eth)
- supports limited low risk assets

**Singularity**
- seperate market pairs that enable riskier assets 
- supports more high risk assets compared to `BigBang` with up to 5x leverage
- elastic interest rates!

**USDO**
- built on `OFTv2` - interoperable across deployed chains
- backed by network gas tokens (`ETH`, `AVAX`, etc.) and liquid staking derivatives
- allows flash mint up to 100_000 `USDO`
- over collateralized by 120%, maintains $1 peg

**TOFT**
- built on `OFTv2` - omnichain asset wrapper
- `tOFT` is collateral on Tapioca markets and backed by omnichain assets
- liquidity between chains

**Magnetar**
- helper contract that executes combined actions of `BigBang`, `Singularity`, `TOFT` and `USDO`
- learned while testing poc: abi.encode / decode array of structs & calldata - memory difference in input

**twAML**
- economic growth incentive model
- users compete with escrows commitments, more lockup -> more discount
- `twTAP` and `oTAP` is based on: `twAML` -> both are LayerZero `ONFT721`s
- `oTAP`: discount: min: 0%, max: 50%
- `twTAP`: multiplier: min: 1.00, max: 0.1 

**twTAP**
- based on `twAML`
- allows to participate in tapioca governance
- `twTAP` lockers receive `USDO` rewards every weekly epoch
- `twTAP` is minted to lockers of `TAP` and `twTAP`

**YieldBox strategies**
- learned a lot about external protocols:
- correct aave, balancer, compound, lido, stargate, glp, yearn, curve, convex integrations
- how `YieldBox` deposits to and withdraws from strategy contracts

### Time spent:
136 hours