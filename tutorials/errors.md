# Errors

If you want to abort your Smart Contract execution, because of error or some condition, inside module or inside script, you can use two constructs: `abort` and `assert`.

In case of error VM will generate error and add error code to the transaction output events.

For example, let's start with `assert`:

```rustc
script {
    fun error(a: u128, b: u128) {
        assert(a > b, 101); // Throw error with code 101 if ‘a’ is less than ‘b’.
    }
}
```

And same could be done with `abort`:

```rustc
script {
    fun error(a: u128, b: u128) {
        if (a < b) {
            abort 101; // Throw error with code 101 if ‘a’ is less than ‘b’.
        }
    }
}
```

You can use `abort` and `assert` in your modules also.

Let's compile one of examples using [dove](../move_vm/compiler_&_toolset.md#dove) and execute using [CLI](../getting_started/cli.md) (easiest way to see returned events):

```sh
yarn run:api tx.mvm.execute <script> <file> --seed <seed>
```

Replace values with your own, run execute transaction and see output.

// TODO: there is no error in assert now :(.
