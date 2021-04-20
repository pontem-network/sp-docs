# Access Control

When you develop smart contracts there could be situations when you want to develop a module containing functions only your account can call.
For example, in Solidity it's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) Smart Contract, that brings modifier `onlyOwner`, that deny everyone to call specific functions, but allows to call it only by owner.

Let's create a new module with the same logic, we will store. We will use the Signer module to extract the transaction sender address and compare it with the defined module owner.

// TODO: check code works.

```rustc
module OnlyOwnerStore {
    use 0x01::Signer;

    // Just a simple number storage.
    resource struct U64 {
        val: u64
    }

    // Owner address.
    const OWNER : address = {{sender}};

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
```

As you, only owner address can call `store_u64` method of module, otherwise it sends error:

```rustc
assert(Signer::address_of(account) == OWNER, 101); // Throw error if function called not from owner.
```

Deploy module under your address and use next script for experiment:

```rustc
script {
    use {{sender}}::OnlyOwnerStore;
    use 0x01::Event;

    fun main(account: &signer, n: u64) {
        Event::emit(OnlyOwnerStore::owner()); // emit owner address.

        OnlyOwnerStore::store_u64(account, n);
    }
}
```

Try to execute the script from different accounts, it will return an error if a transaction sender is not the owner.
