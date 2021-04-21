# Watch resources

`move-resource-viewer` allows you to watch resources stored inside a blockchain node. First of all, install [move-resource-viewer](../move_vm/compiler_&_toolset.md) using our documentation.

Compile using [dove](../move_vm/compiler_&_toolset.md) and deploy `Storage` module:

// TODO: check code.

```rustc
module Store {
    resource struct U64 {val: u64}
    resource struct Address {val: address}
    resource struct VectorU8 {val: vector<u8>}

    public fun store_u64(account: &signer, val: u64) {
        let foo = U64 {val: val};
        move_to<U64>(account, foo);
    }

    public fun store_address(account: &signer, val: address) {
        let addr = Address {val: val};
        move_to<Address>(account, addr);
    }

    public fun store_vector_u8(account: &signer, val: vector<u8>) {
        let vec = VectorU8 {val: val};
        move_to<VectorU8>(account, vec);
    }
}
```

Once the module published, let's store a number using a script:

```rustc
script {
    use {{sender}}::Store;
    
    fun store_num(account: &signer, a: u64) {
        Store::store_u64(account, a);
    }
}
```

Use resource viewer to see stored value:

```text
move-resource-viewer -a <address> -q "<address>::Store::U64" --api="ws://127.0.0.1:9944"  -o=output.json
cat ./output.json
```

**Important: replace <address> with your address.**

You will see something like (if store value is `10`):

```json
{
  "height": "0xc02a5689b6120fa495e6a5606b279c7a4b200f2fb5e4d0edec6969578d81ecef",
  "result": {
    "is_resource": true,
    "type": {
      "address": [...],
      "module": "Store",
      "name": "U64",
      "type_params": []
    },
    "value": [
      {
        "id": "val",
        "value": {
          "U64": 10
        }
      }
    ]
  }
}
```

More easy way to try resource viewer, just to check PONT balance (be sure you have PONT tokens in [VM space](../move_vm/native_balances.md)):

```json
move-resource-viewer -a <address> -q "0x1::Account::Balance<0x1::PONT::T>" --api="ws://127.0.0.1:9944"  -o=output.json
cat ./output.json
```
