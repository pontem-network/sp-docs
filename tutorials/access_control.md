# Access Control

When you developing smart contracts there could be a situations when you want to develop a module contains functions only your account can call.
For example, in Solidity it's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) Smart Contract, that brings modifier `onlyOwner`, that deny everyone to call specific functions, but allows to call it only by owner.

Let's create a new module with the same logic, we will store. We will use Signer module to extract transaction sender address and compare it with defined module owner.

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
