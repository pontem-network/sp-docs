# UI

The easiest way to iterate with Polkadot based blockchains is to use the Polkadot JS App UI. 

You can use the UI to create new accounts, send transactions, do RPC calls, and much more. 

## Deployed Polkadot App.

Use following link to connect to the Pontem hosted node using our UI - [PONTEM UI](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/accounts).

Otherwise you can configure the UI manually:

* Navigate to the [Polkadot JS App](https://polkadot.js.org/apps).
* Click on top left Polkadot logo to see available network custom endpoints
* Click on `TEST NETWORKS`.
* Click on `Pontem`.
* Click on the `Switch` button back on the top of the list. 

## Account creation

{% hint style="info" %}
🧙‍♂️ We recommend you use the [Polkadot JS browser extension](https://polkadot.js.org/extension/) to create an account, as it supports hardware wallets and has an integration with Polkadot App UI, otherwise use the documentation below to create an account. 
{% endhint %}

![Account](/assets/account.png "Account")

* Navigate to **"Accounts"** in the header menu.
* Click on **"Add account"**.
* **Write down your seed and store it safely!**
* Put in a name and password.

## Transactions

In the current documentation we describe how to send and execute scripts and publish module transactions.

{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to build first modules and scripts.
{% endhint %}

You need an account with a balance (in the case of using a local network, use the default accounts), in case of using the Pontem hosted network, create a new account and fund it using the [FAUCET](https://t.me/pontem_faucet_bot).

These next steps require having the [Dove](../move_vm/compiler_&_toolset.md) tool installed. Therefore, these next steps assume you already compiled your modules/scripts

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
8. Submit a new signed transaction.
9. Wait until the transaction is confirmed.

### Script

![Execute script](/assets/script.png "Execute script")

{% hint style="info" %}
🧙‍♂️ Use Dove to compile a script with arguments using the `tx` command. Use this on files with `.mvt` extension.
{% endhint %}

To deploy a compiled script transaction perform the following steps:

1. Navigate to **Developer -> Extrinsics**.
2. Choose **mvm** module.
3. Choose the correct account.
4. Choose **execute** transaction.
5. Choose **script_bc** field and enable file upload.
6. Upload compiled transaction file (with `.mvt` extension). 
7. Configure the gas limit parameter (usually 100000 will be enough).
8. Submit a new signed transaction.
9. Wait until the transaction is confirmed.

## Tips

To speed up transactions you can provide an additional tip in PONT to the transaction. When you click on the **Submit Transaction** button it will show a modal where you can configure tips:

![Tips](/assets/tips.png "Tips")

See [gas, weight and tips](../move_vm/gas.md) documentation to learn more.

## RPC

You can execute RPC calls using the UI:

* Navigate to **Developer -> RPC calls**.
* Choose the **mvm** module.
* Choose the method you want to execute:
  * **gasToWeight(gas)** - converts gas to weight.
  * **weightToGas(weight)** - converts weight to gas.
  * **estimateGasExecute(account, tx_bc, gas_limit)** - estimates gas for an `execute` transaction (accepts account, transaction, and gas limit as arguments).
  * **estimateGasPublish(account, module_bc, gas_limit)** - estimates gas for a `publishModule` transaction (accepts account, module, and gas limit as arguments).

Please reference the [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC using other methods like cURL or CLI.
