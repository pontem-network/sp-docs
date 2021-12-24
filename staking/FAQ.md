# Staking FAQ

- ## **The number of peers has dropped to 0**
  
  Try restarting the node, if you are using a virtual machine, try restarting that as well.

- ## **parachainStaking.CandidateExists error**

  You are already a Nominator/Collator (You cannot be a Nominator and Collator at the same time)

- ## **parachainStaking.NominatorExists error**

  You are already a Nominator/Collator (You cannot be a Nominator and Collator at the same time)

- ## **parachainStaking.CandidateDNE (ParachainStaking -> candidateBondMore) error**
  
  You may not have enough tokens. Check your balance and change the amount.

- ## **parachainStaking.NominatorDNE (ParachainStaking -> candidateBondMore) error**

  You may not have enough tokens. Check your balance and change the amount.

- ## **Error in node logs: "No Nimbus keys available. We will not be able to author"**

  You need to add the Nimbus key: 
  ```
  # Replace <you_mnemonic> with your mnemonic phrase.
  docker-compose run pontem-node pontem key insert --suri "<you_mnemonic>" --keystore-path /opt/pontem/keys --key-type nmbs
  ```

- ## **parachainStaking.ToLowCandidateCount error**

  The number of candidates has changed since you made the request. Simply repeat the query:

  1. Navigate to [Pontem UI. Javascript](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/js)
  2. Execute there following code (remove pre-defined code before):

  ```js
  // Simple script to get candidate pool size
  const candidatePool = await api.query.parachainStaking.candidatePool();
  console.log(`Candidate pool size is: ${candidatePool.length}`);
  ```

  Copy printed number after `Candidate pool size is:`.

![Candidate Pool](/assets/candidate_pool.png "Candidate Pool")

- ## **In the node logs I see error:**

  ```log
  [Parachain] panicked at 'Storage root must match that calculated.', /root/.cargo/git/checkouts/substrate-7e08433d4c370a21/57346f6/frame/executive/src/lib.rs:503:9
  [Parachain] Block prepare storage changes error: ...
  [Parachain] 💔 Error importing block ...
  ```

  This relates to this [issue](https://github.com/Phala-Network/phala-blockchain/issues/551). The solution at this point is to add the `--state-cache-size 1` key to `docker-compose.yml`:

  ```bash
  vim docker-compose.yml
  ```

  In the `command` section add `--state-cache-size 1`. It should look something like this:

![Docker-compose.yml](/assets/storage-root-error.png "Docker-compose.yml")

  Restart Pontem:

  ```
  docker-compose restart pontem-node
  ```

- ## **I keep seeing a message in the logs: "Skipping candidate production because we are not eligible"**

  Your node is on the candidate pool, but not yet selected

- ## **I have successfully completed everything, but I am still on the candidate pool**

  Active status gets collators with the highest stake. Active collators are updated every round (1 round = 300 blocks, i.e. 300, 600, 900, and so on)

- ## **Where can I see the pool of collator candidates?**

  1. Navigate to [Chain State](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/chainstate)
  2. Choose `parachainStaking` pallet under 'selected state query'.
  3. Choose `candidatePool(): ParachainStakingSetOrderedSetBond`
  5. Press the "+" button.

![Candidate pool](/assets/faq_candidate_pool.png "Candidate pool")

- ## **Where can I find a list of active collators?**
  
  1. Navigate to [Chain State](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/chainstate)
  2. Choose `parachainStaking` pallet under 'selected state query'.
  3. Choose `selectedCandidates(): Vec<AccountId32>`
  5. Press the "+" button.

![Selected candidates](/assets/faq_selected_candidates.png "Selected candidates")

- ## **How do I see the current stakes?**

  1. Navigate to [Chain State](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/chainstate)
  2. Choose `parachainStaking` pallet under 'selected state query'.
  3. Choose `candidatePool(): ParachainStakingSetOrderedSetBond`
  5. Press the "+" button.

![Candidate pool](/assets/faq_candidate_pool.png "Candidate pool")

- ## **Where can I see the reward?**
  
  Rewards can be seen at the end of each round in the block information. For example, the information about block [72900](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/explorer/query/0xe61823a1904246f1120c8cf56e52277e49b571e68971eb4c9ea47d7f3500a078):

![Rewards](/assets/faq_rewards.png "Rewards")

 

- ## **Is it possible to be a nominator and a collator at the same time?**
  
  No, you can't.

- ## **How are collator rewards calculated?**

  Information about rewards can be found [here](https://docs.pontem.network/03.-staking/staking)

- ## **How to stake more as collator?**

  1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
  2. Choose `parachainStaking` pallet.
  3. Choose `candidateBondMore(more)` function.
  4. Specify the additional amount to be bonded in the `more` field (do not forget about 10 decimals!).
  5. Submit the transaction.

![Bond More](/assets/bond_more.png "Bond More")

- ## **How to stake less as collator?**

  1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
  2. Choose `parachainStaking` pallet.
  3. Choose `candidateBondLess(less)` function.
  4. Specify the amount by which you want to decrease your bond in the `less` field (do not forget about 10 decimals!).
  5. Submit the transaction.

![Bond Less](/assets/bond_less.png "Bond Less")

- ## **How to stake more as nominator?**

  1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
  2. Choose `parachainStaking` pallet.
  3. Choose `nominatorBondMore(candidate, more)` function.
  4. Specify the additional amount to be bonded in the `more` field (do not forget about 10 decimals!).
  5. Submit the transaction.

![Bond More](/assets/faq_nominator_more.png "Bond More")

- ## **How to stake less as nominator?**

  1. Navigate to [extrinsics](https://polkadot.js.org/apps/?rpc=wss://testnet.pontem.network/ws#/extrinsics).
  2. Choose `parachainStaking` pallet.
  3. Choose `nominatorBondLess(candidate, less)` function.
  4. Specify the amount by which you want to decrease your bond in the `less` field (do not forget about 10 decimals!).
  5. Submit the transaction.

![Bond Less](/assets/faq_nominator_less.png "Bond Less")

- ## **What number should I enter if I want to increase or decrease the amount of tokens?**

  Add 10 zeros to the desired amount. For example, if you want to increase the collator stake by 1234 PONT, you need to enter `12340000000000`.

- ## **How many active collators can there be?**

  At the moment - 61.

- ## **Where can I get PONT for Testnet?**

  From the [Pontem faucet bot](https://t.me/pontem_faucet_bot). You can get tokens once every 24 hours.

### If you can't find an answer, you can always ask a question in one of our chats:


[👾Announcement Channel](https://t.me/pontemnetworkchannel)

[🤖Discord](https://discord.gg/GkzU2qwV) 

[🤖Pontem Collators Channel](https://t.me/pontemnetworkcollators)

[👾Russian Community Telegram](https://t.me/PontemNetworkRU)

[👾Russian Community Collators Telegram](https://t.me/pontem_RU)

[👾Korean Community Telegram](https://t.me/Pontem_kor)

[👾Spanish Community Telegram](https://t.me/pontem_es)

[👾Turkish Community Telegram](https://t.me/pontem_tr)