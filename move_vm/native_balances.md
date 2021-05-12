# Native Balances

PONT is a native coin in the Pontem Network, as PONT coin is accessible not only on smart contracts level, but also on Substrate level: staking, government, transfers.

{% hint style="info" %}
🧙‍♂️ It's important to keep units in mind, when you transfer balance please be sure you transfer at least some minimum value, let's say 1 Unit, for each Substrate chain it could be different. For example, if you transfer too small value, human readable balance couldn't be updated, as the amount changed is too small.
 
We recommend to transfer at least 2 Units on each account to cover fees and successful transfer funds. Currently 1 Unit is a `1000000000000` amount of coins.
{% endhint %}

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
🧙‍♂️ During deposit PONT coins to VM space, the VM will create new resource with provided amount of PONT coins, in the same time native balance will be reduced on the same amount. Same happens when a developer withdraws his PONT balance using native functions, his PONT balance inside VM space will be reduced, and increased as native balance.
{% endhint %}

### Deposit to another account

First of all, create an account and fund it, or just use Bob's account.
Query account:

```sh
pontem-cli query.system.account <address>
```

{% hint style="info" %}
🧙‍♂️ Replace `<address>` with your own.
{% endhint %}

You will see something like it:
```json
{
  "account": {
    "nonce": "0",
    "consumers": "0",
    "providers": "1",
    "data": {
      "free": "2.000 Unit",
      "reserved": "0",
      "miscFrozen": "0",
      "feeFrozen": "0"
    }
  }
}
```

Filed `free` is available for transfer balance.

Let's see how to send PONT coin from one account to another:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun transfer(account: &signer, payee: address, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 12);

        // Deposit a sender PONT coins from native balance.
        // The function returns PONT balance resource.
        let pont = Account::deposit_native<PONT::T>(account, amount);

        // Transfer PONT resource to payee balance resource.
        Account::deposit<PONT::T>(account, payee, pont);
    }
}
```

Compile script with recipient address and amount:

```sh
dove ct 'transfer(<recipient>, 1000000000000)'
```

{% hint style="info" %}
🧙‍♂️ Replace `<recipient>` with address of recipient.
{% endhint %}

Submit transaction using CLI/UI and see changed balance:

```sh
pontem-cli query.system.account <address>
```

Balance should be reduced at least on 1 Unit.

Use `deposit_native` can deposit his PONT coins to VM space, function returns PONT balance resource developer can use in the future, for example, in `deposit` function.

If you query the account you used to execute the script you will see how balance changes (but be sure you move enough coins otherwise balance wouldn't be updated in the UI/CLI output, but will be updated in the storage).

### Withdraw

To withdraw PONT coins from VM resource back to native balance use function `withdraw_native`.

As the recipient already got coins in VM space, just let's withdraw the received coins to native balance.

{% hint style="info" %}
🧙‍♂️ Recipient should has at least 1 Unit on balance to cover transaction fees.
{% endhint %}

Compile next script and execute using recipient account:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun main(account: &signer, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 12);

        // Acquire PONT coins from resource.
        let pont = Account::withdraw_from_sender<PONT::T>(account, amount);

        // Witdraw sender coins.
        Account::withdraw_native<PONT::T>(account, pont);
    }
}
```

See updated balance:


```sh
pontem-cli query.system.account <recipient>
```

{% hint style="info" %}
🧙‍♂️ Replace `<recipient>` with recipient address.
{% endhint %

Recipient balance would be updated on 1 Unit deposited by the sender in the previous section.

### Deposit to ourself


Let's see how to deposit PONT coins without sending it:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;
    use 0x1::Pontem;

    fun main(account: &signer, amount: u128) {
        // To make sure PONT coin registered and known.
        Pontem::register_coin<PONT::T>(b"PONT", 12);

        // Deposit coins from native balance to VM resource.
        let pont = Account::deposit_native<PONT::T>(account, amount);
        Account::deposit_to_sender(account, pont);
    }
}
```

Using the previous example developer just moved his coins from native balance to VM resource, and can use these coins in future, his other scripts or modules.

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
        Pontem::register_coin<PONT::T>(b"PONT", 12);

        // Get balance of native PONT coin.
        let pont_balance = Account::get_native_balance<PONT::T>(account);

        // Emit event with balance.
        Event::emit<u128>(account, pont_balance);
    }
}
```
