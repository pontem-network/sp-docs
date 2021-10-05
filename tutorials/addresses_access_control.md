# Addresses & Access Control

When you develop smart contracts there could be situations when you want to develop a module containing functions only your account can call.

For example, in Solidity it's the [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) Smart Contract that brings the modifier `onlyOwner` which denies everyone the ability to call specific functions and allows only the owner to call them.

Let's create a new module with the same logic. We will use the Signer module to extract the transaction's sender address and compare it with the defined module owner.

```rust
address {{sender}} {
    module OnlyOwnerStore {
        use 0x01::Signer;

        // Just a simple number storage.
        struct U64 has store, key {
            val: u64
        }

        // Owner address.
        const OWNER : address = @{{sender}};

        public fun store_u64(account: &signer, val: u64) {
            assert(Signer::address_of(account) == OWNER, 101); // Throw error if function called is not from owner.
            let foo = U64 { val: val };
            move_to<U64>(account, foo);
        }

        // The function returns the owner's address.
        public fun owner() : address {
            OWNER
        }
    }
}
```

If you put the address inside a variable, or as a literal, you should use the `@` prefix (like in our example).

Only the owner address can call the `store_u64` methods of the module, otherwise it sends an error:

```rust
assert(Signer::address_of(account) == OWNER, 101); // Throw error if function called is not from owner.
```

Deploy the module under your address and use the following script as an experiment:

```rust
script {
    use {{sender}}::OnlyOwnerStore;

    fun owner_store(account: signer, n: u64) {
        OnlyOwnerStore::store_u64(&account, n); // Will throw error if the account doesn't match the owner.
    }
}
```

Try to execute the script from different accounts, and you will see that it will return an error if the transaction sender is not the owner.
