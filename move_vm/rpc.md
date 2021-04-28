# RPC

Move VM pallet implements several RPC calls:

* `mvm_gasToWeight` - converts gas to weight.
* `mvm_weightToGas` - converts weight to gas.
* `mvm_estimateGasPublish` - estimate gas for `publishModule` transaction.
* `mvm_estimateGasExecute` - estimate gas for `execute` transaction.

You can get all available RPC methods using curl:

```text
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "rpc_methods"}' http://localhost:9933/
```

As curl is not the best tool to iterate with RPC, we are going to show how to request RPC using Polkadot JS CLI.

## Polkadot JS CLI

Install Polkadot JS CLI using [instruction](../getting_started/cli.md).

Estimate gas for execute transaction:

```text
pontem-cli rpc.mvm.estimateGasExecute <address> @<mvt file> <gas>
```

Replace arguments:

* `<address>` - with account address used during compilation.
* `<mvt file>` - path to compiled script transaction file.
* `<gas>` - amount of gas.

Response for RPC looks so:

```json
{
  "estimateGasExecute": {
    "gas_used": "100000",
    "status_code": "4,001"
  }
}
```

Where:

* `gas_used` - amount of gas used to execute a script.
* `status_code` - status code returned from VM, 4001 is OK, look for more [status codes](https://github.com/pontem-network/sp-move-vm/blob/master/language/move-core/types/src/vm_status.rs#L408).
  
Other examples:

```text
pontem-cli rpc.mvm.estimateGasPublish <address> @<mvt file> <gas>
pontem-cli rpc.mvm.gasToWeight <gas>
pontem-cli rpc.mvm.weightToGas <weight>
```
