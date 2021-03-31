# Scripts

As already mentioned, **Pontem** supports transaction scripting. It means users can compile and execute scripts. The Difference between modules here is that you can't publish a script and use it again in the future, each script executing by a new transaction every time. Scripts are programmable transactions, one script can use several modules.

You can read more about scripts in [Diem Scripts](https://developers.diem.com/docs/move/move-modules-and-scripts/) documentation.

## Write a script

Let's write a basic script, accept two arguments, a and b values, and then using module math make a sum from these two numbers and then fire events.
We are going to use the module we deployed in previous part (`Math.move` and `Storage.move`).

{% hint style="info" %}
🧙‍♂️ A script block must start with all of its use declarations, followed by any constants and (finally) the main function declaration. The main function can have any name (i.e., it need not be called main), is the only function in a script block, can have any number of arguments, and must not return a value. 
{% endhint %}

Let's create new script calling `sum.move` in `./scripts/` folder and put next code new file:

```rust
script {
   use 0x1::Event;
   use <address>::Math;

   fun sum(account: &signer, a: u64, b: u64) {
      let sum = Math::add(a, b);
      Event::emit(account, sum);
   }
}
```

{% hint style="info" %}
🧙‍♂️ Don't forget to replace `<address>` with your ss58 Polkadot address!
{% endhint %}

The script accepts two arguments in function **"sum"**, then calculate the sum with provided arguments, and fire the event with this sum. Both arguments are **u64** integers.

Let's compile script using dove to create a new binary contains both script and arguments for script:

```text
dove ct 'sum(10, 20)'
```

After compilation see transaction file:

```text
ls -la ./target/transactions 
```

There must be a `sum.mvt` script transaction file you can use to send a new `execute` transaction to the network.

TODO: link to how to send execute script using polkadotjs or UI.

## Store resource

Let's store new resources using the `Storage` module we've done.
Create a new script `store_sum.move` in `./scripts/` folder and put next code inside: 

```rust
script {
    use 0x01::Signer;
    use <address>::Storage;

    fun store_sum(account: &signer, a: u64, b: u64) {
        // Store sum.
        Storage::store_sum(account, a, b);

        // Get sum from resource.
        let sum = Storage::get_sum(Signer::address_of(account));

        // Throw error if sums don't match.
        // Error code is 101.
        assert((a + b) == sum, 101);
    }
}
```

{% hint style="info" %}
🧙‍♂️ Don't forget to replace `<address>` with your ss58 Polkadot address!
{% endhint %}

Compile new script like previous example:

```text
dove ct 'store_sum(10, 20)'
```

TODO: link to how to send execute script using polkadotjs or UI.

After transaction executing, your sum resource will be stored in the Substrate storage, and you can query it any time using script:

```rust
script {
    use 0x01::Signer;
    use <address>::Storage;

    fun store_sum(account: &signer) {
        // Get sum from resource.
        let sum = Storage::get_sum(Signer::address_of(account));
        ...
    }
}
```

Read more about resources in Move language [documentation](../lang/resources.md) and in Diem Move [documentation](https://developers.diem.com/docs/move/move-structs-and-resources).
