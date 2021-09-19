# Polkadot JS CLI

If you don't want to use the UI to operate with Pontem Network, we offer to use CLI.
 
[Polkadot JS](https://polkadot.js.org/) provides tools for Substrate blockchains, including [CLI](https://github.com/polkadot-js/tools/tree/master/packages/api-cli).

Required:

* [NodeJS](https://nodejs.org/en/download/) (since v14.x.x)

CLI installation:

```text
npm install -g polkadot-js-api
```

Run help:

```text
polkadot-js-api --help
```

To configure endpoint use parameter `--ws`, e.g.: `--ws ws://127.0.0.1:9944`, for testnet use `--ws wss://testnet.pontem.network/ws`.

## Metadata

As Polkadot CLI works with different parachains, you have to provide metadata required by Pontem parachain node:

```sh
touch ./types.json
```

And put next JSON inside created types file:

```json
{ 
  "Balance":"u64",
  "RoundIndex":"u32",
  "AuthorId":"[u8;32]",
  "RegistrationInfo":{
      "account":"AccountId",
      "deposit":"Balance"
  },
  "Candidate":{
      "id":"AccountId",
      "fee":"Perbill",
      "bond":"Balance",
      "nominators":"Vec<Bond>",
      "total":"Balance",
      "state":"ValidatorStatus"
  },
  "Nominator":{
      "nominations":"Vec<Bond>",
      "total":"Balance"
  },
  "Bond":{
      "owner":"AccountId",
      "amount":"Balance"
  },
  "ValidatorStatus":{
      "_enum":{
        "Active":"Null",
        "Idle":"Null",
        "Leaving":"RoundIndex"
      }
  },
  "Range":"RangeBalance",
  "RangeBalance":{
      "min":"Balance",
      "ideal":"Balance",
      "max":"Balance"
  },
  "RangePerbill":{
      "min":"Perbill",
      "ideal":"Perbill",
      "max":"Perbill"
  },
  "InflationInfo":{
      "expect":"RangeBalance",
      "annual":"RangePerbill",
      "round":"RangePerbill"
  },
  "RoundInfo":{
      "current":"RoundIndex",
      "first":"BlockNumber",
      "length":"u32"
  },
  "OrderedSet": "Vec<Bond>",
  "NominatorAdded":{
      "_enum":{
        "AddedToBottom":"Null",
        "AddedToTop":"Balance"
      }
  },
  "ParachainBondConfig":{
      "account":"AccountId",
      "percent":"Percent"
  },
  "ExitQ":{
      "candidates":"Vec<AccountId>",
      "nominators_leaving":"Vec<AccountId>",
      "candidate_schedule":"Vec<(AccountId, RoundIndex)>",
      "nominator_schedule":"Vec<(AccountId, Option<AccountId>, RoundIndex)>"
  },
  "Nominator2":{
      "nominations":"Vec<Bond>",
      "revocations":"Vec<AccountId>",
      "total":"Balance",
      "scheduled_revocations_count":"u32",
      "scheduled_revocations_total":"Balance",
      "status":"NominatorStatus"
  },
  "CollatorSnapshot":{
      "bond":"Balance",
      "nominators":"Vec<Bond>",
      "total":"Balance"
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
  "MoveTypeTag":{
      "_enum":[
        "Bool",
        "U8",
        "U64",
        "U128",
        "Address",
        "Signer",
        "Vector",
        "Struct"
      ],
      "Bool":"",
      "U8":"",
      "U64":"",
      "U128":"",
      "Address":"",
      "Signer":"",
      "Vector":"MoveTypeTag",
      "Struct":"MoveStructTag"
  },
  "MoveStructTag":{
      "address":"AccountId",
      "module":"Text",
      "name":"Text"
  }
}
```

## Account creation

You can create new account using [The Subkey Tool](https://substrate.dev/docs/en/knowledgebase/integrate/subkey).

Let's try to query account after creation:

```text
polkadot-js-api query.system.account <address> --ws wss://testnet.pontem.network/ws --types ./types.json
```

Replace `<address>` with your account address, e.g. `5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY`.

### Transactions

In the current documentation we describe how to send `execute` script and `publishModule` module transactions using CLI.

{% hint style="info" %}
🧙‍♂️ Read [Move VM](../move_vm/README.md) pallet documentation to build first modules and scripts.
{% endhint %}
 
You need an account with balance (in case of local network use default accounts), in case of Pontem testnet network create a new account and fund it using [FAUCET](https://t.me/pontem_faucet_bot).
 
Next steps required to have the [dove](../move_vm/compiler_&_toolset.md) tool installed, means you already compiled your modules/scripts.
 
## Module
 
To deploy compiled module run the following command:
 
```text
polkadot-js-api tx.mvm.publishModule @<module.mv> <gas> --seed <seed> --types ./types.json --ws <ws-address>
```
 
Replace parameters:
 
* `<module.mv>` - compiled module file (`.mv` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local nodes. 
* `<ws-address>` - node websocket endpoint.
 
You will see result of the transaction execution, like:

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
🧙‍♂️ Compile a script using dove with arguments using `tx` command, use file with `.mvt` extension.
{% endhint %}

To deploy compiled script transaction run following command:

```text
polkadot-js-api tx.mvm.execute @<script.mvt> <gas> --seed <seed> --types ./types.json --ws <ws-address>
```

Replace parameters:

* `<script.mvt>` - compiled script transaction file (`.mvt` extension).
* `<gas>` - amount of gas.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local node.  
* `<ws-address>` - node websocket endpoint.

## RPC

So see [RPC](../move_vm/rpc.md) documentation to see how to send requests to RPC.

## More

See [JS tools](https://github.com/polkadot-js/tools/tree/master/packages/api-cli#polkadotapi-cli) documentation and also help:

```text
polkadot-js-api --help
```
