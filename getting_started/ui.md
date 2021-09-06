# UI

Easiest way to iterate with Polkadot based blockchain to use Polkadot JS App UI. 

You can use the UI to create new accounts, send transactions, do RPC calls, and much more. 

## Deployed Polkadot App.

Use following link to connect to Pontem node using UI - [PONTEM UI](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/accounts).

Otherwise you can configure UI manually:

* Navigate to [Polkadot App](https://polkadot.js.org/apps).
* Choose network (to see network settings click on the orange circle in the top right corner and change custom endpoint).
* Click on `TEST NETWORKS`.
* Click on `Pontem`.
* Click on the `Switch` button. 

## Account creation

{% hint style="info" %}
🧙‍♂️ We recommend to use [Polkadot Extension](https://polkadot.js.org/extension/) to create account, it supports hardware wallets and has integration with Polkadot App UI, otherwise use current documentation. 
{% endhint %}

![Account](/assets/account.png "Account")

* Navigate to **"Accounts"** in the header menu.
* Click on **"Add account"**.
* **Write down your seed and store it safely!**
* Put name and password.

Otherwise you can use [Polkadot JS Extension](https://polkadot.js.org/extension/), just follow instructions.

## Transactions

In the current documentation we describe how to send execute scripts and publish module transactions.

{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to build first modules and scripts.
{% endhint %}

You need an account with balance (in case of local network use default accounts), in case of Pontem testnet network create a new account and fund it using [FAUCET](https://t.me/pontem_faucet_bot).

Next steps required to have the [dove](../move_vm/compiler_&_toolset.md) tool installed, means you already compiled your modules/scripts.

### Module

![Deploy Module](/assets/module.png "Deploy Module")

To deploy compiled module do following steps:

1. Navigate to **Developer -> Extrinsics**.
2. Choose **mvm** module.
3. Choose the correct account.
4. Choose **publishModule** transaction.
5. Choose **module_bc** field and enable file upload.
6. Upload compiled module file (with `.mv` extension).
7. Configure the gas limit parameter (usually 100000 will be enough).
8. Submit a new signed transaction!
9. Wait until the transaction is confirmed.

### Script

![Execute script](/assets/script.png "Execute script")

{% hint style="info" %}
🧙‍♂️ Compile a script using dove with arguments using `tx` command, use file with `.mvt` extension.
{% endhint %}

To deploy compiled script transaction do following steps:

1. Navigate to **Developer -> Extrinsics**.
2. Choose **mvm** module.
3. Choose the correct account.
4. Choose **execute** transaction.
5. Choose **script_bc** field and enable file upload.
6. Upload complied transaction file (with `.mvt` extension). 
7. Configure the gas limit parameter (usually 100000 will be enough).
8. Submit a new signed transaction!
9. Wait until the transaction is confirmed.

## Tips

To speed up transaction you can provide additional tips to the transaction, when you click on **Submit Transaction** button it show modal where you can configure tip:

![Tips](/assets/tips.png "Tips")

See [gas, weight and tips](../move_vm/gas.md) documentation to learn more.

## RPC

You can execute RPC calls using UI:

* Navigate to **Developer -> RPC calls**.
* Choose **mvm** module.
* Choose method you want to execute:
  * **gasToWeight(gas)** - converts gas to weight.
  * **weightToGas(weight)** - converts weight to gas.
  * **estimateGasExecute(account, tx_bc, gas_limit)** - estimate gas for `execute` transaction (accepts account, transaction, and gas limit as arguments).
  * **estimateGasPublish(account, module_bc, gas_limit)** - estimate gas for `publishModule` transaction (accepts account, module, and gas limit as arguments).

See [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC using other methods like curl or CLI.
