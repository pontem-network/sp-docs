# Polkadot JS CLI

If you don't want to use UI to operate with Pontem Network, we offer to use CLI.
 
Polkadot JS provides tools for Substrate blockchains, including CLI. Pontem team forked [tools](https://github.com/pontem-network/tools) repository to implement custom RPC calls and types.
 
{% hint style="info" %}
🧙‍♂️ Current fork can be installed only from repository and used via call `yarn run:command`. In the near time we are going to release `pontem-cli` that could be installed globally via NPM.
{% endhint %}

Required:

* [NodeJS](https://nodejs.org/en/download/) (since v12.x.x)
* [Yarn](https://yarnpkg.com/)

CLI installation:

```text
git clone git@github.com:pontem-network/tools.git pontem-tools
cd pontem-tools
yarn install
yarn build
```

After installation you can use API CLI inside directory `pontem-tools`, e.g.:

```text
yarn run:api --help
```

To configurate endpoint use parameter `--ws`, e.g.: `--ws ws://127.0.0.1:9944`.

## Account creation

You can create new account using [The Subkey Tool](https://substrate.dev/docs/en/knowledgebase/integrate/subkey).

Let's try to query account after creation:

```text
yarn run:api query.system.account <address>
```

Replace `<address>` with your account address, e.g. `5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY`.

### Transactions
 
{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to build first modules and scripts.
{% endhint %}

You can use the CLI to create new accounts, send transactions, and do RPC calls etc. In the current documentation we describe how to send `execute` script and `publish` module transactions.
 
You need an account with balance, in case of local network use default accounts, in case of Pontem demo network, use faucet.
 
Next steps required to have [dove](../move_vm/compiler_&_toolset.md) compiler installed, means you already compiled your modules/scripts.
 
## Module
 
To deploy compiled module run the following command:
 
```text
yarn run:api tx.mvm.publishModule @<module.mv> <gas> --seed <seed>
```
 
Replace parameters:
 
* `<module.mv>` - compiled module file (`.mv` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local nodes. 
 
You will see result of the transaction execution:

```json
{
  "publishModule": {
    "dispatchInfo": {
      "weight": "181,818",
      "class": "Normal",
      "paysFee": "Yes"
    },
    "events": [
      {
        "phase": {
          "ApplyExtrinsic": "1"
        },
        "event": {
          "method": "ModulePublished",
          "section": "mvm",
          "index": "0x0801",
          "data": [
            "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty"
          ]
        },
        "topics": []
      },
      {
        "phase": {
          "ApplyExtrinsic": "1"
        },
        "event": {
          "method": "ExtrinsicSuccess",
          "section": "system",
          "index": "0x0000",
          "data": [
            {
              "weight": "181,818",
              "class": "Normal",
              "paysFee": "Yes"
            }
          ]
        },
        "topics": []
      }
    ],
    "status": {
      "InBlock": "0xc0a26c9676e0f585bb1bde9a812a3f6e238bc41cadbc4204e3acd9b3244d28a6"
    }
  }
}
```

### Script

{% hint style="info" %}
🧙‍♂️ Compile a script using dove with arguments using `ct` command, use file with `.mvt` extension.
{% endhint %}

To deploy compiled script transaction run following command:

```text
yarn run:api tx.mvm.execute @<script.mvt> <gas> --seed <seed>
```

Replace parameters:

* `<script.mvt>` - compiled script transaction file (`.mvt` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local node.  


### Sudo

You can use `sudo` in Substrate to deploy [Standard Library](../move_vm/stdlib.md) to Move VM pallet. Standard library will be stored under the `0x01` address.
To deploy a standard library using `sudo` you need access to `sudo` account, in case of local network it's usually `Alice` account.

See how to [build](./local_node.md#standard-library) Standard Library.

Use following command to deploy package:

```text
yarn run:api tx.mvm.publishPackage @<package.mv> <gas> --seed <seed> --sudo
```

Replace parameters:

* `<script.mvt>` - compiled package file (`.pac` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local node.  


## RPC

So see [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC using CLI.

## More

See [tools](https://github.com/pontem-network/tools/tree/master/packages/api-cli) documentation and also help:

```text
yarn run:api --help
```

