# Staking

Pontem is running as a [Parachain](https://wiki.polkadot.network/docs/learn-parachains) based on Proof of Stake staking.

The staking that the Pontem Network is using was initially implemented by the [Moonbeam Foundation team](https://moonbeam.network/), so we also refer to their [documentation](https://docs.moonbeam.network/learn/features/staking/) which is very useful.

**IMPORTANT:** all values are configured only for testnet purposes currently, so they could change in the future.

Key points to know:

* The Pontem parachain is secured by Relay Chain validators. This means new blocks are produced after approval from Relay Chain validators, e.g. Polkadot or Kusama.
* Pontem has its own [collators](https://wiki.polkadot.network/docs/learn-collator) which are user maintained nodes that collect transactions, produce state transitions for the Relay Chain, and produce new blocks.
* Any network participant in the network who has the minimum required amount of PONT tokens can be registered as a collator.
* Only the top collators at the current stage would be active and earn rewards.
* Collators can self-stake PONT tokens.
* Other users who don't want to run their own node can nominate (delegate) their PONT tokens to a maximum of 10 collators. These users are called nominators, and earn part of the rewards earned by the collator.
* The top collators are sorted by amount of self-staked collator’s PONT tokens plus the nominated tokens.
* The list of collators updates each round.
* Each collator has a maximum number of nominators.
* Because this technology is very experimental, there's no slashing yet.
* The earned rewards for each block is split between collators, nominators, and the Parachain Bond Fund.
* The Parachain Bond Fund is used to fund future Parachain slots or subsidize Parathread usage.
* Collators can't be nominators, and nominators can't be a collators.

### Reference sheet:

We prepared a short reference sheet containing general definitions and actual parameter values:

* **Blocks In Round** - number of blocks per one round. Value: 300.
* **Top amount** - number of top collators able to participate in staking. Value: 8.
* **Maximum Nominators Per Collator** - number of nominators each collator can have at a given time. Value: 10.
* **Maximum Collators Per Nominator** - number of collators each nominator can vote for. Value: 25.
* **Default Collator Commission** - default commission that a collator earns. Value: 20%. This value can only be changed by the root account currently.
* **Default Parachain Bond Reserve Percentage** - the percent from the staking inflation that goes to the Parachain Bond Fund. Value: 30%.
* **Reward Payment Delay** - delay in rounds before rewards will be distributed between collators, nominators. Value: 600 (2 rounds).
* **Minimum Collator Candidate Stake** - amount of PONT tokens required to create and register a collator. Value: 100 PONT.
* **Minimum Collator Stake** - minimum amount of PONT tokens required to be staked for the collator to become active (an active collator can contribute to producing blocks if the collator is one of the top collators). Value: 1000 PONT.
* **Minimum Nominator Stake** - minimum amount of PONT tokens to be staked by a nominator for a collator. Value: 1 PONT.

## Inflation

**IMPORTANT:** all inflation vaalues configured only for testnet purposes. These values can be changed in the future for the mainnet.

| Expected Staked (PONT)        | Annual Inflation |
| ----------------------------- | ---------------- |
| Min:   10 000 000 PONT        |  10%             |
| Ideal: 25 000 000 PONT        |  15%             |
| Max:   50 000 000 PONT        |  20%             |

Pontem inflation is based on the amount of staked PONT tokens, if staked amount is between 'Min' and 'Max' we would have ideal inflation, otherwise it will default to be either minimum inflation if below the 'Min' threshold or maximum inflation if above the 'Max' threshold.

## Rewards

Collators and their nominators are rewarded at the end of every round for their work from 2 rounds ago.

Rewards are distributed in following manner:

* Each two blocks the network produces new rewards in the form of variable X PONT tokens based on inflation parameters.
* 30% the PONT rewards go to the Parachain Bond Reserve for securing future parachain slots.
* The rest is split between collators and nominators:
  * Only collators which generated blocks receive a reward.
  * Collators get part of the reward, but is variable based on how many blocks the collator generated during the round (more blocks = more rewards).
  * If there are nominators contributing, the collator earns a commission (20%) and gets part of the rewards proportional to how much it is self-staking.
  * If there are no nominators staking for a collator, the collator earns all of the rewards which includes the commission and the remaining allocated for that collator.
  * The rest of rewards go to the nominators and is split between them proportionally to their stake for collator.

The logic & math for this is below:

```text
parachain_bond_fund = total_reward * 0.3 # Amount of PONT that goes to Parachain Bond Fund.

collators_reward = (total_reward) - parachain_bond_fund # Amount of rewards that would be shared between collators.  

collator_reward = collators_reward * generated_blocks_fraction # How much reward goes to the collator based on the amount of blocks generated by the collator per total blocks generated in that round. This calculation is done for each collator.

commission = collator_reward * 0.2 # How much commission collator earned.
reward = collator_reward - commission # How much reward is going to be distributed between collator and nominators.

pay_to_collator = commission + (reward * stake_fraction) # How much PONT to pay to the collator.
pay_to_nominator = (reward * stake_fraction) # How much PONT to pay to each collator nominator.
```

## Useful links

The staking mechanism that the Pontem Network uses was initially implemented by the [Moonbeam team](https://moonbeam.network/), so we also recommend reviewing their [documentation](https://docs.moonbeam.network/learn/features/staking/) that is very useful.
