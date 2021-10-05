# Modules

There are two types of smart contracts in Pontem: modules and scripts.

The difference between them is that a module is published into blockchain storage and is stored under the publisher account, while a script is simply a transaction-as-script and can only operate with existing modules. This means that you and other developers can use your modules in their own scripts or modules.

A Module - like a class in object oriented programming - stores resources, structures, functions and constants. Typically, a module name start with an uppercase letter and uses a camelback naming convention. A module named MyModule should be stored in a source file named `MyModule.move`.

Each module is stored under the user address and it's own name, e.g. 'address::Math'. The address of a deployer is required during compilation because the Move VM verifies during deployment if a module being compiled is using the deployer's address.

To include a module into your script/module use the following construction:

```text
use <address>::<module name>;
```

You can read more about modules in the [Diem modules documentation](https://developers.diem.com/docs/move/move-modules-and-scripts/).

You can use the `{{sender}}` literal that represents the account address from `Dove.toml`.

## Write a module

{% hint style="info" %}
🧙‍♂️ Install the [Dove](./compiler_&_toolset.md) before continue with this tutorial.
{% endhint %}

Let's see an example of a small module that will just add two numbers \(a and b\).

Create a new project using **Dove**:

```text
dove new my_modules --dialect pont --address <address>
cd my_modules
```

{% hint style="info" %}
🧙‍♂️ Don't forget to replace `<address>` with your SS58 Polkadot address!
{% endhint %}

In the modules folder create a `Math.move` file and put the following code inside:

```rust
address {{sender}} {
    module Math {
        // Sum two numbers and return result.
        public fun add(a: u64, b: u64): u64 {
            a + b
        }
    }
}
```

As you can see, the function `add` accepts two `u64` numbers and returns the sum of the numbers provided.

Let's compile this module using the **Dove**. The compiler requires the sender's address as it's included into the bytecode. This address will then be verified when the module is published.

```text
dove build
```

See compiled module ready for deployment:

```text
ls -la ./artifacts/modules
```

You will see `0_Math.mv`, use this file to deploy your new module.

{% hint style="info" %}
✈️ See our instruction on how to send a transaction to execute/publish modules using the Polkadot JS [UI](../getting_started/ui.md) or [CLI](../getting_started/cli.md).
{% endhint %}

## Module with resource

A resource is the main feature of the **Move VM**. A resource is a special type in Move VM which has strict rules of usage for safety, and it was created to work with digital assets. Usually a resource is represented as a structure with abilities.

A resource type can only be defined and managed in a single module. This module sets rules for accessing, destroying, transferring and checking the existence of resources defined in it.

This documentation doesn't go in depth into resources, but we can still make a module containing resources for demonstration purposes. You can read more about resources in the Move language [documentation](../lang/resources.md) and in the [Move Book](https://move-book.com/advanced-topics/types-with-abilities.html) documentation.

This module stores the sum of two numbers into a resource. Create a new module calling `Storage.move` and put the following code inside:

```rust
address {{sender}} {
    module StorageSum {
        // Include Math module.
        use {{sender}}::Math;

        // Store U64 number into struct that has key (resource).
        struct Sum has key {
            val: u64
        }

        // The function 'store_sum' to store the sum of a + b into a resource.
        public fun store_sum(account: &signer, a: u64, b: u64) {
            let sum = Sum {val: Math::add(a, b)};
            move_to<Sum>(account, sum);
        }

        // Get the sum stored under address resource.
        public fun get_sum(addr: address): u64 acquires Sum {
            borrow_global<Sum>(addr).val
        }
    }
}
```

Compile it and deploy it like the previous module.

Congrats! You just published your first modules. In the next part of this documentation we will write scripts and use our modules.
If you want more advanced examples of modules, like creating new tokens, see our [tutorials](/tutorials/README.md).
