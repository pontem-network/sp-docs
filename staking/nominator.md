# Nominator

Nominators can stake PONT tokens for collators (nominate them).

Before you continue, check if you completed all the steps below before staking your PONT tokens:

* Read introduction to [Pontem staking](./README.md).
* Create account on Pontem network that contains at least +1 PONT tokens to stake and pay network fees:
  * Create account using [UI](../getting_started/ui.md#account-creation) or use [CLI](../getting_started/cli.md#account-creation).
  * You can use [FAUCET](https://t.me/pontem_faucet_bot) to get free testnet tokens.

After you completed all required steps, you can start with the process of staking your PONT tokens.

First we need to find a collator you want to nominate, and also a few technical values.

## Query list of collators

There is two lists of collators:

* `selectedCandidates` - contains active collators list (collators that are producing blocks).
* `candidatePool` - contains the entire collator list (includes active and candidates).
  
To query the list of collators:

1. Navigate to [Chain State](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/chainstate)
2. Choose `parachainStaking` pallet from 'selected state query'.
3. Choose `selectedCandidates` or `candidatePool` and press the '+' button You will get a comma separated list of addresses.
4. Copy the collator account address you want to nominate.

## Get collator nominators count

Query collator nominators count:

1. Navigate to [Pontem UI. Javascript](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/js)
2. Execute there following code including the collator address you just copied (replace place-holder code):

```js
// Simple script to get collator_nominator_count
// Remember to replace COLLATOR_ADDRESS with the address of desired collator.
const collatorAccount = 'COLLATOR_ADDRESS'; 
const collatorInfo = await api.query.parachainStaking.collatorState2(collatorAccount);
console.log('Nominators count: ' + collatorInfo.toHuman()["nominators"].length);
```

Don't forget to replace `COLLATOR_ADDRESS` with the one you just copied from the previous step.

Copy printed number after `Nominators count:`.

## Get count of your nominations

Now we need to get count of your nominations:

1. Navigate to [Pontem UI. Javascript](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/js)
2. Execute there following code (replace place-holder code):

```js
// Simple script to get your number of existing nominations.
// Remember to replace YOUR_ADDRESS_HERE with your nominator address.
const yourNominatorAccount = 'YOUR_ADDRESS_HERE'; 
const nominatorInfo = await api.query.parachainStaking.nominatorState2(yourNominatorAccount);
console.log('Nominations: ' + nominatorInfo.toHuman()["nominations"].length);
```

Don't forget to replace `YOUR_ADDRESS_HERE` with your address.

In case the query returns an error use 0.

Copy printed number after `Nominations:`.

## Stake PONT Tokens

To stake your PONT tokens do following steps:

1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
2. Choose `parachainStaking` pallet.
3. Choose `nominate(collator, amount, collatorNominatorCount, nominationCount)` method.
4. In `collator` field put the address of the collator.
5. In `amount` field put PONT token amount.
6. In `collatorNominatorCount` field put value from the [Get collator nominator count](#get-collator-nominators-count) step.
7. In `nominationCount` field put value from the [Get number of your nominations](#get-count-of-your-nominations) step.
8. Send transaction

![Stake PONT](/assets/author_mapping.png "Stake PONT")


Congratulations on staking your PONT! We hope to improve the process with our own wallet in the future.

## Stake More or Less.

1. In [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics) use `nominatorBondLess` and `nominatorBondMore` functions in the `parachainStaking` pallet.
2. Ensure collator address is in 'candidate: AccountId'
3. Add balance of PONT you want to add or subtract 

## Revoke Nomination

To cancel nomitation for collator in [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics) use `revokeNomination` function in the `parachainStaking` pallet. =

## More Methods

1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
2. Choose `parachainStaking` pallet.
3. Scroll methods list and review methods descriptions.

## Useful links

The staking mechanism that the Pontem Network uses was initially implemented by the [Moonbeam team](https://moonbeam.network/), so we also recommend reviewing their [documentation](https://docs.moonbeam.network/learn/features/staking/) that is very useful.
