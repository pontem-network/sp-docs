# UI

Easiest way to iterate with Polkadot based blockchain to use Polkadot JS App UI. 

// TODO: remote node address and in the link.

Before start you need to configure UI. 

{% hint style="info" %}
🧙‍♂️ If you want to use remote node by Pontem team use ip address: `testnet.pontem.network`, if you built node locally use local address `ws://127.0.0.1:9944`.
{% endhint %}

1. Navigate to [Polkadot JS App UI](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/settings/developer).
2. Setup UI to use an **IP address** if needed (to see network settings click on the orange circle in the top right corner and change custom endpoint).

Go to `Settings -> Developer` and put there next JSON:

// TODO: fix json for new version.

```json
{
  "Address": "AccountId",
  "LookupSource": "AccountId",
  "RawAccountAddress": "[u8;32]",
  "AccountAddress": "[u8;32]",
  "ModuleId": {
    "address": "AccountAddress",
    "name": "Text"
  },
  "TypeTag": {
    "_enum": [
      "Bool",
      "U8",
      "U64",
      "U128",
      "Address",
      "Signer",
      "Vector",
      "Struct"
    ],
    "Bool": null,
    "U8": null,
    "U64": null,
    "U128": null,
    "Address": null,
    "Signer": null,
    "Vector": "Vec<TypeTag>",
    "Struct": "StructTag"
  },
  "StructTag": {
    "address": "AccountAddress",
    "module": "Text",
    "name": "Text",
    "type_params": "Vec<TypeTag>"
  }
}
```

Save configuration.

## Account creation

![Account](/assets/module.png "Account")

* Navigate to **"Accounts"** in the header menu.
* Click on **"Add account"**.
* **Write down your seed and store it safely!**
* Put name and password.

Otherwise you can use [Polkadot JS Extension](https://polkadot.js.org/extension/), just follow instructions.

## Transactions

{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to build first modules and scripts.
{% endhint %}

You can use the UI to create new accounts, send transactions, and do RPC calls etc. In the current documentation we describe how to send execute script and publish module transactions, also how to deploy Standard Library using a sudo account.

// TODO: link to faucet.

You need an account with balance, in case of local network use default accounts, in case of Pontem demo network, use faucet.

Next steps required to have  [dove](../move_vm/compiler_&_toolset.md) compiler installed, means you already compiled your modules/scripts.

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
🧙‍♂️ Compile a script using dove with arguments using `ct` command, use file with `.mvt` extension.
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

### Sudo

![Sudo](/assets/sudo.png "Sudo")

You can use `sudo` in Substrate to deploy [Standard Library](../move_vm/stdlib.md) to Move VM pallet. Standard library will be stored under the `0x01` address.
To deploy a standard library using `sudo` you need access to `sudo` account, in case of local network it's usually `Alice` account.

Current Move VM pallet already contains the Standard Library by default, but if you want to deploy some additional library use next instruction.

To deploy standard module do following steps:

1. Navigate to **Developer -> Extrinsics**.
2. Choose the origin account (usually it's Alice in case of local network).
3. Choose the **sudo** module.
4. Choose **sudo(call)** transaction.
5. Choose the **mvm** module in the call: **Call** field.
6. Choose **publishStd** transaction.
7. Click **addItem**.
8. For the new field and enable file upload.
9. Upload modules step by step by submitting transactions.
10. Configure the gas limit parameter (usually 1000000 will be enough).
11. Wait until transactions are confirmed.

## Tips

To speed up transaction you can provide additional tips to the transaction, when you click on **Submit Transaction** button it show modal where you can configure tip:

![Tips](/assets/tips.png "Tips")

See [gas, weight and tips](../move_vm/gas.md) documentation to learn more.

## RPC

Currently, you can't call Move pallet RPC from the UI, but we are going to fix it soon. 

So see [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC using other methods.
