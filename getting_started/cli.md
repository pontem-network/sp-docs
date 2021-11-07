# Polkadot JS CLI

If you don't want to use the Polkadot JS UI to interact with the Pontem Network, you can use the Polkadot JS Command Line Interface (CLI).
 
[Polkadot JS](https://polkadot.js.org/) provides tools for Substrate based blockchains, including a [CLI](https://github.com/polkadot-js/tools/tree/master/packages/api-cli).

1. Make sure you have Node.js installed to use the npm package manager.

* [NodeJS](https://nodejs.org/en/download/) (since v14.x.x)

2. use npm to install the Polkadot JS CLI:

```text
yarn global add @polkadot/api-cli
```

Run help to learn more about the CLI:

```text
polkadot-js-api --help
```

To configure endpoint use parameter `--ws`, e.g.: `--ws ws://127.0.0.1:9944`, for the Pontem Network testnet use `--ws wss://testnet.pontem.network/ws`.

## Metadata

Because Polkadot CLI works with different Parachains, you have to provide metadata required by the Pontem Parachain node:

1. Create a JSON file titled 'types' where you will put the metadata

```sh
touch ./types.json
```

2. Put the JSON code below inside the 'types.json' file:

```json
{
   "AuthorId":"[u8;32]",
   "Balance":"u64",
   "Bond":{
      "owner":"AccountId",
      "amount":"Balance"
   },
   "Collator2":{
      "id":"AccountId",
      "bond":"Balance",
      "nominators":"Vec<AccountId>",
      "top_nominators":"Vec<Bond>",
      "bottom_nominators":"Vec<Bond>",
      "total_counted":"Balance",
      "total_backing":"Balance",
      "state":"CollatorStatus"
   },
   "Candidate":{
      "bond":"Balance",
      "fee":"Perbill",
      "id":"AccountId",
      "nominators":"Vec<Bond>",
      "state":"ValidatorStatus",
      "total":"Balance"
   },
   "CollatorSnapshot":{
      "bond":"Balance",
      "nominators":"Vec<Bond>",
      "total":"Balance"
   },
   "Estimation":{
      "gas_used":"u64",
      "status_code":"u64"
   },
   "ExitQ":{
      "candidate_schedule":"Vec<(AccountId, RoundIndex)>",
      "candidates":"Vec<AccountId>",
      "nominator_schedule":"Vec<(AccountId, Option<AccountId>, RoundIndex)>",
      "nominators_leaving":"Vec<AccountId>"
   },
   "InflationInfo":{
      "annual":"RangePerbill",
      "expect":"RangeBalance",
      "round":"RangePerbill"
   },
   "MoveStructTag":{
      "address":"AccountId",
      "module":"Text",
      "name":"Text"
   },
   "MoveTypeTag":{
      "Address":"",
      "Bool":"",
      "Signer":"",
      "Struct":"MoveStructTag",
      "U128":"",
      "U64":"",
      "U8":"",
      "Vector":"MoveTypeTag",
      "_enum":[
         "Bool",
         "U8",
         "U64",
         "U128",
         "Address",
         "Signer",
         "Vector",
         "Struct"
      ]
   },
   "Nominator":{
      "nominations":"Vec<Bond>",
      "total":"Balance"
   },
   "Nominator2":{
      "nominations":"Vec<Bond>",
      "revocations":"Vec<AccountId>",
      "scheduled_revocations_count":"u32",
      "scheduled_revocations_total":"Balance",
      "status":"NominatorStatus",
      "total":"Balance"
   },
   "NominatorAdded":{
      "_enum":{
         "AddedToBottom":"Null",
         "AddedToTop":"Balance"
      }
   },
   "OrderedSet":"Vec<Bond>",
   "ParachainBondConfig":{
      "account":"AccountId",
      "percent":"Percent"
   },
   "Range":"RangeBalance",
   "RangeBalance":{
      "ideal":"Balance",
      "max":"Balance",
      "min":"Balance"
   },
   "RangePerbill":{
      "ideal":"Perbill",
      "max":"Perbill",
      "min":"Perbill"
   },
   "RegistrationInfo":{
      "account":"AccountId",
      "deposit":"Balance"
   },
   "RoundIndex":"u32",
   "RoundInfo":{
      "current":"RoundIndex",
      "first":"BlockNumber",
      "length":"u32"
   },
   "ValidatorStatus":{
      "_enum":{
         "Active":"Null",
         "Idle":"Null",
         "Leaving":"RoundIndex"
      }
   },
   "CollatorStatus":{
      "_enum":{
         "Active":"Null",
         "Idle":"Null",
         "Leaving":"RoundIndex"
      }
   },
   "NominatorStatus":{
      "_enum":{
         "Active":"Null",
         "Leaving":"RoundIndex"
      }
   }
}
```

## Account creation

You can create a new account using [The Subkey tool](https://substrate.dev/docs/en/knowledgebase/integrate/subkey).

To use the Subkey, you'll first need to make sure you have Substrate build dependencies. Use the following commands to install both the dependencies and Subkey:

```text
# Use `--fast' to get the dependencies without having to install the Substrate and Subkey binary
curl https://getsubstrate.io -sSf | bash -s -- --fast 
# Install `subkey`, at a specific version
cargo install --force subkey --git https://github.com/paritytech/substrate --version 2.0.1 --locked
```

To generate a key use the following command:

```text
subkey generate
```

Next, let's query the new account after you create it,:

```text
polkadot-js-api query.system.account <address> --ws wss://testnet.pontem.network/ws --types ./types.json
```

Replace `<address>` with your generated account address ('SS58 Address' in the output from the previous command), e.g. `5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY`.

### Transactions

In this documentation we describe how to send the `execute` script and the `publishModule` module transactions using CLI.

{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to learn how to build your first modules and scripts.
{% endhint %}
 
For this, you'll need an account with a balance (if you're using a local network use the default accounts), if you're using the Pontem testnet network, create a new account and fund it using our [FAUCET](https://t.me/pontem_faucet_bot).
 
These next steps require having the [Dove](../move_vm/compiler_&_toolset.md) tool installed. Therefore, these next steps assume you already compiled your modules/scripts
 
## Module
 
To deploy a compiled module run the following command:
 
```text
polkadot-js-api tx.mvm.publishModule @<module.mv> <gas> --seed <seed> --types ./types.json --ws <ws-address>
```
 
Replace parameters:
 
* `<module.mv>` - compiled module file (`.mv` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local nodes. 
* `<ws-address>` - node websocket endpoint.
 
After deploying, you'll see the result of the transaction execution with a JSON output similar to this:

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
🧙‍♂️ Use Dove to compile a script with arguments using the `tx` command. Use this on files with `.mvt` extension.
{% endhint %}

To deploy a compiled script transaction, run the following command:

```text
polkadot-js-api tx.mvm.execute @<script.mvt> <gas> --seed <seed> --types ./types.json --ws <ws-address>
```

Replace parameters:

* `<script.mvt>` - compiled script transaction file (`.mvt` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local node.  
* `<ws-address>` - node websocket endpoint.

## RPC

See [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC.

## More

See [JS tools](https://github.com/polkadot-js/tools/tree/master/packages/api-cli#polkadotapi-cli) documentation and also help:

```text
polkadot-js-api --help
```
