# Addresses & Access Control

When you develop smart contracts there could be situations when you want to develop a module containing functions only your account can call.

For example, in Solidity it's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) Smart Contract, that brings modifier `onlyOwner`, that deny everyone to call specific functions, but allows to call it only by owner.

Let's create a new module with the same logic, we will store. We will use the Signer module to extract the transaction sender address and compare it with the defined module owner.

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
            assert(Signer::address_of(account) == OWNER, 101); // Throw error if function called not from owner.
            let foo = U64 { val: val };
            move_to<U64>(account, foo);
        }

        // The function returns owner address.
        public fun owner() : address {
            OWNER
        }
    }
}
```

If you put the address inside a variable, or as literal, you should use `@` prefix (like in our example).

As you, only owner address can call `store_u64` method of module, otherwise it sends error:

```rust
assert(Signer::address_of(account) == OWNER, 101); // Throw error if function called not from owner.
```

Deploy module under your address and use next script for experiment:

```rust
script {
    use {{sender}}::OnlyOwnerStore;

    fun owner_store(account: signer, n: u64) {
        OnlyOwnerStore::store_u64(&account, n); // Will throw error if account doesn't match owner.
    }
}
```

Try to execute the script from different accounts, it will return an error if a transaction sender is not the owner.
