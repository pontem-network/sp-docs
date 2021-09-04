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

As curl is not the best tool to iterate with RPC, see how to call RPC using [Polkadot UI](../getting_started/ui.md#RPC).
