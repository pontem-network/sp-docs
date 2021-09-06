# Errors

If you want to abort your Smart Contract execution, because of error or some condition, inside module or inside script, you can use two constructs: `abort` and `assert`.

In case of error VM will generate error and add error code to the transaction output events.

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

You can use `abort` and `assert` in your modules also.

Let's compile one of examples using [dove](../move_vm/compiler_&_toolset.md#dove) and execute using [CLI](../getting_started/cli.md) (easiest way to see returned events):

```sh
polkadot-js-api tx.mvm.execute @<script> <file> --seed <seed> --ws <ws-endpoint>
```

Replace values with your own, run execute transaction and see output, generated event:

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

**Important: The described way is not the best, but only way to get custom errors from Move VM in Move pallet right now, we are working hard to make it better and more useful.**
