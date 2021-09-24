# Nominator

Nominator can stake his PONT tokens for collator (nominate them).

Before you continue, check if you completed all steps before stake your PONT coins:

* Read introduction to [Pontem staking](./README.md).
* Account on Pontem network contains at least +1 PONT coins to stake and pay network fees:
  * Create account using [UI](../getting_started/ui.md#account-creation) or use [CLI](../getting_started/cli.md#account-creation).
  * You can use [FAUCET](https://t.me/pontem_faucet_bot) to get free tokens.

After you completed all previous required steps, you can start with the process of stake your PONT coins.

First of all we need to find collator you want to nominate, and also a few technical values.

## Query list of collators

There is two list of collators:

* `selectedCandidates` - contains active collators list (that one's produce blocks).
* `candidatePool` - contains all collators list (include active and candidates).
  
To query list of collators do next steps:

1. Navigate to [Chain State](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/chainstate)
2. Choose `parachainStaking` pallet.
3. Choose `candidatePool` or `selectedCandidates`.
4. Copy collator account address you want to nominate.

## Get collator nominators count

Query collator nominators count:

1. Navigate to [Pontem UI. Javascript](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/js)
2. Execute there following code (remove pre-defined code before):

```js
// Simple script to get collator_nominator_count
// Remember to replace COLLATOR_ADDRESS with the address of desired collator.
const collatorAccount = 'COLLATOR_ADDRESS'; 
const collatorInfo = await api.query.parachainStaking.collatorState2(collatorAccount);
console.log('Nominators count: ' + collatorInfo.toHuman()["nominators"].length);
```

Don't forget to replace `COLLATOR_ADDRESS` with an actual one.

Copy printed number after `Nominators count:`.

## Get count of your nominations

Now we need to get count of your nominations:

1. Navigate to [Pontem UI. Javascript](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/js)
2. Execute there following code (remove pre-defined code before):

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

## Stake PONT coins

To finally stake your PONT coins do following steps:

1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
2. Choose `parachainStaking` pallet.
3. Choose `nominate(collator, amount, collatorNominatorCount, nominationCount)` method.
4. In `collator` field put the address of the collator.
5. In `amount` field put PONT coins amount.
6. In `collatorNominatorCount` field put value from [Get collator nominator count](#get-collator-nominators-count) step.
7. In `nominationCount` field put value from [Get number of your nominations](#get-count-of-your-nominations) step.
8. Send transaction

![Stake PONT](/assets/author_mapping.png "Stake PONT")


It's all! We hope to improve process with our own wallet in the future.

## Stake More or Less.

Use `nominatorBondLess` and `nominatorBondMore` functions in the `parachainStaking` pallet.

## Revoke Nomination

To cancel nomitation for collator use `revokeNomination` function in the `parachainStaking` pallet. =

## More

1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
2. Choose `parachainStaking` pallet.
3. Scroll methods list and review methods descriptions.

## Useful links

Staking that Pontem network uses initially implemented by [Moonbeam team](https://moonbeam.network/), we also refer to their [documentation](https://docs.moonbeam.network/learn/features/staking/) that very useful.
