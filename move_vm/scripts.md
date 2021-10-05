# Scripts

**Pontem** supports transaction scripting. This means users can compile and execute scripts. The Difference between a modules and a script is that you can't publish a script and use it again in the future. Each script is executed by a new transaction every time. Scripts are programmable transactions, one script can use several modules.

You can read more about scripts in the [Diem script documentation](https://developers.diem.com/docs/move/move-modules-and-scripts/).

## Write a script

{% hint style="info" %}
🧙‍♂️ Install [Dove](./compiler_&_toolset.md) before continuing with this tutorial.
{% endhint %}

Let's write a basic script that accept two arguments: values a and b, and then using math from a module, makes a sum from these two numbers and then fires events.
For this example, we are going to use the module we deployed in the previous [modules section](../move_vm/modules.md) (`Math.move` and `Storage.move`).

{% hint style="info" %}
🧙‍♂️ A script code block must start with all of its use declarations followed by any constants and in the end, the main function declaration. The main function can have any name (i.e. it could have different names, not only `main`), is the only function in a script block, can have any number of arguments, and must not return a value.
{% endhint %}

1. Create a new script file called `sum.move` in the `./scripts/` folder and put the following code in there:

```rust
script {
    use 0x01::Event;
    use {{sender}}::Math;

    fun sum(account: signer, a: u64, b: u64) {
        let sum = Math::add(a, b);
        
        // Create event emitter.
        let event_handle = Event::new_event_handle(&account);

        // Emit event.
        Event::emit_event(
           &mut event_handle,
           sum
        );

        // Destroy event emitter.
        Event::destroy_handle(event_handle);
    }
}
```

The script accepts two arguments in the function **"sum"** then calculates the sum with the provided arguments and fires the event with this sum. Both arguments are **u64** integers.

2. Compile the script using Dove to create a new binary containing both the script and arguments for the script:

```text
dove tx 'sum(10, 20)'
```

After compilation, inspect the transaction file:

```text
ls -la ./artifacts/transactions
```

There is now a `sum.mvt` script transaction file you can use to send a new `execute` transaction to the network.

3. Execute transactions using Polkadot JS

{% hint style="info" %}
✈️ See our instructions on how to send transactions (execute/publish module) using the Polkadot JS [UI](../getting_started/substrate.md) or [CLI](../getting_started/cli.md).
{% endhint %}

## Store resource

Now let's store new resources using the `Storage` module we built in the [modules section](../move_vm/modules.md).

1. Create a new script file `store_sum.move` in the `./scripts/` folder and put the following code inside:

```rust
script {
    use 0x1::Signer;
    use {{sender}}::StorageSum;

    fun store_sum(account: signer, a: u64, b: u64) {
        // Store sum.
        StorageSum::store_sum(&account, a, b);

        // Get sum from resource.
        let sum = StorageSum::get_sum(Signer::address_of(&account));

        // Throw error if sums don't match.
        // Error code is 101.
        assert((a + b) == sum, 101);
    }
}
```

2. Compile the script like in the previous example:

```text
dove tx 'store_sum(10, 20)'
```

3. Execute transactions using Polkadot JS

{% hint style="info" %}
✈️ See our instructions on how to send transactions (execute/publish module) using the Polkadot JS [UI](../getting_started/substrate.md) or [CLI](../getting_started/cli.md).
{% endhint %}

After the transaction executes, your sum resource will be stored in Substrate storage, and you can query it any time using the following script:

```rust
script {
    use 0x1::Signer;
    use {{sender}}::StorageSum;

    fun get_sum(account: signer) {
        // Get sum from resource.
        let sum = StorageSum::get_sum(Signer::address_of(&account));

        // Do something with sum.
        ...
    }
}
```

Read more about resources in our [Move](../lang/resources.md) and in the [Move Book](https://move-book.com/advanced-topics/types-with-abilities.html) documentation.
