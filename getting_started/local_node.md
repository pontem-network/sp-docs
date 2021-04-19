# Local Node

This part explains how to build Move VM pallet locally, and run the node in development mode locally.

We recommend to use [rust toolchain](https://github.com/pontem-network/sp-move/blob/master/rust-toolchain) that we use during development.

Install [Rust](https://www.rust-lang.org/tools/install) before moving to next steps.

Visit repository of [Move Pallet](https://github.com/pontem-network/sp-move) and clone it:

```sh
git clone git@github.com:pontem-network/sp-move.git
```

Navigate to cloned repository and launch init script:

```sh
cd ./sp-move
./scripts/init.sh
```

Run local node:

```sh
make run
```

## Connect UI

Navigate to [Polkadot JS App UI](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/settings/developer).

Setup UI to use the local node `ws://127.0.0.1:9944` if needed (to see network settings click on the orange circle in the top right corner and change custom endpoint).

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

Finally you can use Move VM pallet locally.
