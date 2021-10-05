# Learn Resources

The 'resource' is the main feature of the **Move VM**. It's possible to define and create new resources in the code, allowing you to manage digital assets in the same manner as your would use any other data. Resource has strict rules of usage - therefore it was created for safety and to work with digital assets. It was originally implemented as its own type but lated replaced with two abilities: 'store' and 'key'.

the resource type can only be defined and managed in a single module. It's a structure containing a list of abilities. The module sets rules for accessing, destroying, transferring and checking the existence of resources defined in it. A resource can never be duplicated, reused or discarded. It can only be created or destroyed by the module that defines it.

Move resembles object oriented programming so you can think of a resource as an object and a module as a class.

## Develop a resource

{% hint style="info" %}
✈️ See our instructions on how to send a transaction (execute/publish) using the Polkadot JS [UI](../getting_started/substrate.md) or [CLI](../getting_started/cli.md).
{% endhint %}

Let's create a swap module that will allow us to swap coins between users.

We will make it easy since it will only support one swap per coin pair. This means you can't create multiple swaps using the same pair in the same account. This module will have just two functions - publishing your offer and allowing other users to swap it for a specified price.

```rust
address {{sender}} {
    module Swap {
        use 0x1::Diem::{Self, Diem};
        use 0x1::DiemAccount;
        use 0x1::Signer;

        // The resource of the module which contains swap parameters.
        struct Swap<Offered: store + key, Expected: store + key> has key, store {
            offered: Diem<Offered>,
            price: u64,
        }

        // Create a swap deal with two coin pairs: Offered and Expected.
        public fun create<Offered: store + key, Expected: store + key>(sender: &signer, offered: Diem<Offered>, price: u64) {
            let sender_addr = Signer::address_of(sender);

            assert(!exists_at<Offered, Expected>(sender_addr), 101);

            move_to<Swap<Offered, Expected>>(
                sender,
                Swap<Offered, Expected> {
                    offered: offered,
                    price
                }
            );
        }

        // Get the price of the swap deal.
        public fun get_price<Offered: store + key, Expected: store + key>(seller: address): u64 acquires Swap {
            let offer = borrow_global<Swap<Offered, Expected>>(seller);
            offer.price
        }

        // Change price before swap happens.
        public fun change_price<Offered: store + key, Expected: store + key>(sender: &signer, new_price: u64) acquires Swap {
            let offer = borrow_global_mut<Swap<Offered, Expected>>(Signer::address_of(sender));
            offer.price = new_price;
        }

        // Swap coins and deposit them to accounts: both creator and buyer.
        public fun swap<Offered: store + key, Expected: store + key>(sender: &signer, seller: address, exp: Diem<Expected>) acquires Swap {
            let Swap<Offered, Expected> { offered, price } = move_from<Swap<Offered, Expected>>(seller);
            let exp_value = Diem::value<Expected>(&exp);

            assert(exp_value == price, 102);
            DiemAccount::pnt_deposit(seller, exp);
            DiemAccount::pnt_deposit(Signer::address_of(sender), offered);
        }

        // Check if the swap pair already exists for the account.
        public fun exists_at<Offered: store + key, Expected: store + key>(addr: address): bool {
            exists<Swap<Offered, Expected>>(addr)
        }
    }
}
```

The provided code creates a new module **"Swap"** and a resource named **"Swap"** \(default name for default resource in modules\), which holds information about the deal.

To create a swap use the **"create"** function. To make an exchange use the **"swap"** function. Other methods in this module provide the ability to get/set price and check if the swap option already exists at a specific address. All methods in this example uses [generic](https://developers.diem.com/docs/move/move-basic-concepts/move-basics-generics) Offered and Expected, which allows you to make unique resources for each swap.

The resource `Diem::Diem` represents the account balance of `Offered` type.

```rust
// The resource of module which contains swap parameters.
struct Swap<Offered: store + key, Expected: store + key> has key, store {
    offered: Diem<Offered>,
    price: u64,
}
```

Even though there's a lot of code inside, we'll focus on 4 main methods: `borrow_global_mut`, `move_to_sender`, `move_from`, `exists` and on the `acquires` keyword.

### move\_to&lt;T&gt;\(&signer, T\)

When a resource is created, it needs to be moved to an address \(otherwise it will never be actually created - there's no 'contract storage' - only accounts\). It is important to note that a newly created resource can be moved only to the sender of a transaction - this makes the initialization of a resource impossible at someone else's address.

To move a resource to a sender, the `move_to<T>(&signer, T)` method is used where T is a generic type and instance of this type. Below is the resource for reference:

```rust
// Create a swap deal with two coin pairs: Offered and Expected.
public fun create<Offered: store + key, Expected: store + key>(sender: &signer, offered: Diem<Offered>, price: u64) {
    let sender_addr = Signer::address_of(sender);

    assert(!exists_at<Offered, Expected>(sender_addr), 101);

    move_to<Swap<Offered, Expected>>(
        sender,
        Swap<Offered, Expected> {
            offered: offered,
            price
        }
    );
}
```

In the `create` function we created new resources which contain information about the swap, and moved it to sender. After this, we can start working with our resources.

### exists&lt;T&gt;\(address\)

Allow us to check if the resource already exists on the specific address or not:

```rust
// Check if swap pair already exists for account.
public fun exists_at<Offered: store + key, Expected: store + key>(addr: address): bool {
    exists<Swap<Offered, Expected>>(addr)
}
```

### borrow\_global\_mut&lt;T&gt;\(address\)

```rust
// Change price before swap happens.
public fun change_price<Offered: store + key, Expected: store + key>(sender: &signer, new_price: u64) acquires Swap {
    let offer = borrow_global_mut<Swap<Offered, Expected>>(Signer::address_of(sender));
    offer.price = new_price;
}
```

Allows us to get a mutable reference to a resource that can be changed. There is also `borrow_global` to get an immutable reference:

```rust
// Get the price of the swap deal.
public fun get_price<Offered: store + key, Expected: store + key>(seller: address): u64 acquires Swap {
    let offer = borrow_global<Swap<Offered, Expected>>(seller);
    offer.price
}
```

`borrow_global` gives immutable reference to a resource stored under address. You can use this reference to read resource. However, you can't change the resource.

### acquires

Every function which accesses an already created resource must have the `acquires` keyword in its signature after which acquired resources are listed. Look at the usage of `borrow_global` and `borrow_global_mut` again. Resource `Swap` is acquired by both the methods `get_price` and `change_price`.

### move\_from&lt;T&gt;\(address\)

```rust
// Swap coins and deposit them to accounts: both creator and buyer.
public fun swap<Offered: store + key, Expected: store + key>(sender: &signer, seller: address, exp: Diem<Expected>) acquires Swap {
    let Swap<Offered, Expected> { offered, price } = move_from<Swap<Offered, Expected>>(seller);
    let exp_value = Diem::value<Expected>(&exp);

    assert(exp_value == price, 102);
    DiemAccount::pnt_deposit(seller, exp);
    DiemAccount::pnt_deposit(Signer::address_of(sender), offered);
}
```

The `move_from<T>(address)` function moves the resource from an address. After taking the resource from the account, it must be used - either destroyed \(like in the example below\) or passed to another function. Resources are not automatically destroyed like regular variables and their lifetime must be specified.

### Summary

The `create` function creates a new resource, `swap` function allows you to swap \(deposit coins to both accounts and _destroy_ resource T\); we've also added methods to get the price of the deal and to change it.

## More about resources

Resources are the most interesting and the most complex topic in Move language. But once you've gotten the idea, the rest is easy.

To get to know Move better and to learn about resources specifically, please reference the Move Book:

* [Programmable Resources](https://move-book.com/resources/index.html
* [Advanced topics - Structures] (https://move-book.com/advanced-topics/struct.html)
