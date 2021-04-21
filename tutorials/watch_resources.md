# Watch resources

`move-resource-viewer` allows to watch resources stored inside blockchain node. First of all, install [move-resource-viewer](../move_vm/compiler_&_toolset.md) using our documentation.

Compile and deploy `Storage` module:

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



move-resource-viewer -a 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty -q "0x1::Account::Balance<0x1::PONT::T>" --api="ws://127.0.0.1:9944"  -o=output.json
cat ./output.json
