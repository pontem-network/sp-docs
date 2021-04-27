# Native Balances

PONT is a native coin in the Pontem Network, as PONT coin is accessible not only on smart contracts level, but also on Substrate level: staking, government, transfers.

To make it possible to work with PONT coins from smart contracts, we had to implement a few native functions in the Move VM, that allows developers to: deposit, withdraw, and query balance of PONT coins. Also, [PONT](https://github.com/pontem-network/move-stdlib/blob/master/modules/pont.move) coin represented as resource in our Standard Library and looks so:
 
```rust
address 0x1 {

/// PONT Coin. The one and only.
/// Put into separate module to highlight its importance and role in Dfinance
/// ecosystem. Also moved not to be mistaken for peg-zone coin. Same-naming
/// attack from peg-zone is impossible when PONT coin moved outside of Coins module
module PONT {
    struct T {}
}
}
```

Developers use `PONT::T` to interact with native PONT coin, but first developer need to deposit PONT coins into Virtual Machine visibility space.

{% hint style="info" %}
🧙‍♂️ During deposit POINT coins to VM space, the VM will create new resource with provided amount of PONT coins, in the same time native balance will be reduced on the same amount. Same happens when a developer withdraws his PONT balance using native functions, his PONT balance inside VM space will be reduced, and increased as native balance.
{% endhint %}

### Deposit.

Let's see how to send PONT coin from one account to another:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun transfer(account: &signer, payee: address, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 6);

        // Deposit a sender PONT coins from native balance.
        // The function returns PONT balance resource.
        let pont = Account::deposit_native<PONT::T>(account, amount);

        // Transfer PONT resource to payee balance resource.
        Account::deposit<PONT::T>(account, payee, pont);
    }
}
```

Use `deposit_native` can deposit his PONT coins to VM space, function returns PONT balance resource developer can use in the future, for example, in `deposit` function.

Let's see how to deposit PONT coins without sending it:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun main(account: &signer, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 6);

        // Deposit coins from native balance to VM resource.
        let pont = Account::deposit_native<PONT::T>(account, amount);
        Account::deposit_to_sender(account, pont);
    }
}
```

Using the previous example developer just moved his coins from native balance to VM resource, and can use these coins in future, his other scripts or modules.

### Withdraw

To withdraw PONT coins from VM resource back to native balance use function `withdraw_native`:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun main(account: &signer, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 6);

        // Acquire PONT coins from resource.
        let pont = Account::withdraw_from_sender<PONT::T>(account, amount);

        // Witdraw sender coins.
        Account::withdraw_native<PONT::T>(account, pont);
    }
}
```

### Query balance

To query available native balance:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Event;
    use 0x1::Pontem;

    fun main(account: &signer) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 6);

        // Get balance of native PONT coin.
        let pont_balance = Account::get_native_balance<PONT::T>(account);

        // Emit event with balance.
        Event::emit<u128>(account, pont_balance);
    }
}
```
