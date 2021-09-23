# Staking

Pontem is running as [Parachain](https://wiki.polkadot.network/docs/learn-parachains) and based on Proof of Stake staking mechanism.

Pontem collator's staking based on Nimbus consensus and Parachain staking built by [Moonbeam team](https://docs.moonbeam.network/learn/features/staking/).

Most important topics you should know:

* Pontem parachain secured by Relay Chain validators, means new blocks produced after approval from Relay Chain validators, e.g. Polkadot or Kusama.
* Yet Pontem has its own [collators](https://wiki.polkadot.network/docs/learn-collator): users who maintain nodes and collect transactions, produce state transitions for Relay Chain, and produce new blocks.
* Any network participant in the network who has the required amount of PONT coins can be registered as a collator.
* Only top collators at the current stage would be active and earn rewards.
* Collators can self-stake PONT coins.
* Other users who don't want to run their own node can nominate (delegate) their PONT coins for max 10 collators. Such user calling nominator, and earn part of rewards earned by collator.
* The top is sorted by amount of self-staked collator’s PONT coins and nominated coins.
* Collators list updating each round.
* Each collator has a maximum of nominators.
* As technology is very experimental, there's no slashing yet.
* Earned rewards for each block split between collators and nominators, and Parachain Bond Fund.
* Parachain Bond Fund needs to fund future parachain slots.
* Collators can be at the same time nominator, and nominator can't be a collator.

### Small reference

We prepared small reference contains general definitions and actual values:

**IMPORTANT:** all values configured only for testnet purposes currently, so it can be changed any time.

* **Blocks In Round** - amount of blocks per one round. Value: 300.
* **Top amount** - amount of collators top contains. Value: 8.
* **Maximum Nominators Per Collator** - amount of nominators each collator can have at the same time. Value: 10.
* **Maximum Collators Per Nominator** - amount of collators each nominator can vote for. Value: 25.
* **Default Collator Commission** - default commission that collator earns. Value: 20%.
* **Default Parachain Bond Reserve Percent** - which percent from inflation goes to Parachain Bond Fund. Value: 30%.
* **Reward Payment Delay** - delay in rounds before rewards will be distributed between collators, nominators. Value: 600 (2 rounds).
* **Minimum Collator Candidate Stake** - amount of PONT coins required to create and register a collator. Value: 100 PONT.
* **Minimum Collator Stake** - minimum amount of PONT coins staked for the collator to become active (active collator can produce if collator on top of collators). Value: 1000 PONT.
* **Minimum Nominator Stake** - minimum amount of PONT coins to be staked for collator. Value: 1 PONT.

## Inflation

**IMPORTANT**: Current configured inflation is just created for test purposes and will be changed in future.



## Rewards

Collators are rewarded at the end of every round for their work from 2 rounds ago.


