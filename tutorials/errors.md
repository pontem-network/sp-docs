# Errors

If you want to abort your smart contract execution because of an error or some condition, inside a module or script you can use the following two constructs: `abort` and `assert`.

In case of an error, the VM will generate the error and add error code to the transaction output events.

For example, let's start with `assert`:

```rust
script {
    fun error(a: u128, b: u128) {
        assert(a > b, 101); // Throw error with code 101 if a is less than b.
    }
}
```

And same could be done with `abort`:

```rust
script {
    fun error_1(a: u128, b: u128) {
        if (a < b) {
            abort 101 // Throw error with code 101 if a is less than b.
        }
    }
}
```

You can also use `abort` and `assert` in your modules.

Let's compile one of the examples using [Dove](../move_vm/compiler_&_toolset.md#dove) and execute using [CLI](../getting_started/cli.md) (easiest way to see returned events):

```sh
polkadot-js-api tx.mvm.execute @<script> <file> --seed <seed> --ws <ws-endpoint>
```

Replace values with your own, run execute transaction and see the output, a generated event:

```json
"event": {
    "method": "Event",
    "section": "mvm",
    "index": "0x0800",
    "data": [
    "5C4hrfjw9DjXZTzV3MwzrrAr9P1MJhSrvWGWqi1eSuyUpnhM",
    "0x07000000000000000000000000000000000000000000000000000000000000000120564d53746174757320564d53746174757300",
    "0x02016500000000000000",
    null
    ]
}
```

Where value `0x02016500000000000000` is indeed our error included in BCS, see [Events](./events.md) tutorial for details.

**Important: The way described above is not the best, but it is the only way to get custom errors from the Move VM in the Move pallet right now. We are working to make it better and more useful.**
