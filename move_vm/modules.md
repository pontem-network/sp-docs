# Modules

There are two types of smart contracts in Pontem: module and script.

Difference between them is that a module is published into blockchain storage and is stored under the publisher account, while script is simply a transaction-as-script and can only operate with existing modules. Means, your and other developers can use your modules into their scripts or modules.

Modules store resources, structures, functions, constants. Typically, module names start with an uppercase letter. A module named MyModule should be stored in a source file named `MyModule.move`.

Each module is stored under the user address and it's own name, e.g. 'address::Math'. Address of deployer required during compilation, as during deploy Move VM verifies if module compiled indeed using deployer address.

To include module into your script/module use next construction:

```text
use <address>::<module name>;
```

You can read more about modules in the [Diem Modules](https://developers.diem.com/docs/move/move-modules-and-scripts/) documentation.

You can use `{{sender}}` literal that represents the account address from `Dove.toml`.

## Write a module

{% hint style="info" %}
🧙‍♂️ Install [Dove](./compiler_&_toolset.md) before continue with this tutorial.
{% endhint %}

Let's see an example of a small module that will just add two numbers \(a and b\).

Create a new project using **dove**:

```text
dove new my_modules --dialect pont --address <address>
cd my_modules
```

{% hint style="info" %}
🧙‍♂️ Don't forget to replace `<address>` with your ss58 Polkadot address!
{% endhint %}

In modules folder create `Math.move` file and put next code inside:

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

As you see the function `add` accepts two `u64` numbers and returns a sum of provided numbers.

Let's compile this module using **dove**. Compiler requires the sender's address as it's included into the bytecode. This address will then be verified on module publish.

```text
dove build
```

And see compiled module ready for deploy:

```text
ls -la ./artifacts/modules
```

You will see `0_Math.mv`, use this file to deploy your new module.

{% hint style="info" %}
✈️ See our instruction how to send transaction (execute/publish module) using [UI](../getting_started/ui.md) or [CLI](../getting_started/cli.md).
{% endhint %}

## Module with resource

Resource is the main feature of **Move VM**. Resource is a special type in Move VM, which has strict rules of usage - therefore more safety, and is created to work with digital assets. Usually resource represents as structure with abilities.

Resource type can only be defined and managed in a single module. This module sets rules for accessing, destroying, transferring and checking the existence of resources defined in it.

This documentation is not going deep into resources, but still let's make a module containing resources for demo proposes. Read more about resources in Move language [documentation](../lang/resources.md) and in [Move Book](https://move-book.com/advanced-topics/types-with-abilities.html) documentation.

Module stores the sum of two numbers into a resource. Create new module calling `Storage.move` and put next code inside it:

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

Compile it and deploy, same like with previous module.

Congrats! You just published your first modules, in the next part of this documentation we will write scripts and use our modules.
If you want more advanced examples of modules, like new tokens, see our [tutorials](/tutorials/README.md).
