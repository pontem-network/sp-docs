# Standard Library


Standard **Move VM** library is default modules that already developed and developers can use it in their modules, scripts.

They all placed on the address **0x1**. So when you import something from **0x1**, you import standard modules, like:

```rust
use 0x1::Account;
use 0x1::Event;
use 0x1::PONT;
...
```

You can look for actual standard modules in [move-stdlib](https://github.com/pontem-network/move-stdlib) repository.

{% hint style="warning" %}
👷 Current standard library is old version of Diem standard library with our adoption, we are working forward to adopt new Diem standard library. 
{% endhint %}

## Time

[Time](https://github.com/pontem-network/move-stdlib/blob/master/modules/time.move) module allows getting current UNIX timestamp of latest block.

Example:

```rust
script {
    use 0x1::Time;

    fun main() {
        let _ = Time::now();
    }
}
```

The method will return u64 value as UNIX timestamp of the latest block.

## Block

[Block](https://github.com/pontem-network/move-stdlib/blob/master/modules/block.move) module allows getting current blockchain height.

```rust
script {
    use 0x1::Block;

    fun main() {
        let _ = Block::get_current_block_height();
    }
}
```

The method will return u64 value as the height of the latest block.

## Compare

[Compare](https://github.com/pontem-network/move-stdlib/blob/master/modules/compare.move) module allows comparing two vectors of u8 values \(bytes\).

Comparing two-byte vectors:

```rust
script {
    use 0x1::Compare;

    fun main() {
        let a = x"00";
        let b = x"01";
        assert(Compare::cmp_lcs_bytes(&a, &b) == 0, 101);
    }
}
```

## PONT 

[PONT](https://github.com/pontem-network/move-stdlib/blob/master/modules/pont.move) module allow to get a PONT type of currency that you going to use in your code.

{% hint style="warning" %}
🧙‍♂️ PONT coin is a native coin, means it required to be deposited in VM space first, so before run this example read more about [native balances](./native_balances.md). 
{% endhint %}

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;

    fun main(sender: &signer, payee: address, pont_amount: u128) {
        Account::pay_from_sender<PONT::T>(sender, payee, pont_amount);
    }
}
```

## Event

[Event](https://github.com/pontem-network/move-stdlib/blob/master/modules/event.move) module allows us to emit events.

Example with emitting event contains provided number:

```rust
script {
    use 0x1::Event;

    fun main(account: &signer, a: u64) {
        Event::emit<u64>(account, a);
    }
}
```

Or you you can emit event from your module:

```rust
module MyEvent {
    use 0x1::Event;

    struct MyStruct {
        value: u64
    }

    public fun my_event(account: &signer, a: u64) {
        Event::emit(account, MyStruct {
            value: a
        });
    }
}
```

## Signer

[Signer](https://github.com/pontem-network/move-stdlib/blob/master/modules/signer.move) module allows to work with the `signer` type. To get address of signer:

```rust
script {
    use 0x1::Signer;

    fun main(sender: &signer) {
        let _ = Signer::address_of(sender);
    }
}
```

Signer type is required for functions which work with resources, address of signer could be useful in case of resource related functions: `borrow_global`, `borrow_global_mut`, `exists`, `move_from`, `move_to`, `freeze`.

Read more about the signer type in [Diem's Signer](https://developers.diem.com/main/docs/move-primitives-signer) documentation.

## Account

[Account](https://github.com/pontem-network/move-stdlib/blob/master/modules/account.move) module allows to work with user balances: get balances, deposit coins/tokens to balances, withdraw them to deposit in another module, etc.

Also, it creates an account, if the account doesn't exist yet, and related data, like event handlers for sending/receiving payments.

A lot of different methods can be used to send tokens from account A to account B, as these one-line methods:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;

    fun main(sender: &signer, payee: address, amount: u128, metadata: vector<u8>) {
        // Move PONT from sender account to payee.
        Account::pay_from_sender<PONT::T>(sender, payee, amount);

        // Again move PONT but with metadata.
        Account::pay_from_sender_with_metadata<PONT::T>(sender, payee, amount, metadata);
    }
}
```

Also, you can just withdraw from sender balance and deposit to payee:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;

    fun main(sender: &signer, payee: address, amount: u128) {
        // Move PONT from sender account.
        let pont = Account::withdraw_from_sender<PONT::T>(sender, amount);

        // Deposit PONT to payee account.
        Account::deposit(sender, payee, pont);
    }
}
```

Or deposit to another module:

```rust
script {
    use {{address}}::Swap;
    use {{address}}::ExampleToken;

    use 0x1::PONT;
    use 0x1::Account;

    fun main(sender: &signer, seller: address, price: u128) {
        let pont = Account::withdraw_from_sender(sender, price);

        // Deposit NewToken to swap coins.
        Swap::swap<ExampleToken::T, PONT::T>(sender, seller, pont);
    }
}
```

Also, get a balance:

```rust
script {
    use 0x1::PONT;
    use 0x1::Account;

    fun main(sender: &signer, addr: address) {
        // My balance.
        let my_balance = Account::balance<PONT::T>(sender);

        // Someone balance.
        let someone_balance = Account::balance_for<PONT::T>(addr);

        assert(my_balance > 0, 101);
        assert(someone_balance > 0, 102);
    }
}
```

For the rest of the features of Account module look at [account.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/account.move).

## Pontem

[Pontem](https://github.com/pontem-network/move-stdlib/blob/master/modules/pontem.move) module allows you to work with coins balances, get coins info, also register new tokens, etc.

First of all, Pontem module presents type for all balances in the system, it's `Pontem::T`:

```rust
resource struct T<Coin> {
    value: u128
}
```

The value field contains information about actual balance for specific coin/token, e.g.:

```rust
script {
    use 0x1::Account;
    use 0x1::PONT;

    fun main(sender: &signer, amount: u128) {
        // Use PONT::T to get Pontem::T<PONT::T> contains balance.
        let pont : 0x1::Pontem::T<PONT::T> = Account::withdraw_from_sender<PONT::T>(sender, amount);
        Account::deposit_to_sender(sender, pont);
    }
}
```

Also, you can create an empty coin:

```rust
module BankPONT {
    use 0x1::Pontem;
    use 0x1::PONT;

    resource struct T {
        balance: Pontem::T<PONT::T>,
    }

    public fun create(account: &signer)  {
        move_to<T>(account, T {
            balance: Pontem::zero<PONT::T>()
        })
    }
}
```

Get denom, decimals, and actual value:

```rust
script {
    use 0x1::Pontem;
    use 0x1::Account;
    use 0x1::PONT;

    fun main(sender: &signer, amount: u128) {
        let pont = Account::withdraw_from_sender<PONT::T>(sender, amount);

        // Get denom vector<8>.
        let _ = Pontem::denom<PONT::T>();

        // Get value of withdrawed xfi.
        let value = Pontem::value(&pont);

        assert(amount == value, 101);

        Account::deposit_to_sender(sender, pont);
    }
}
```

And check if it's user token or system coin:

```rust
script {
    use {{address}}::ExampleToken;
    use 0x1::Pontem;
    use 0x1::PONT;

    fun main() {
        assert(Dfinance::is_token<PONT::T>() == false, 101);
        assert(Dfinance::is_token<MyToken::T>(), 102);
    }
}
```

Also, you can create your resource and make it token too!

```rust
module ExampleToken {
    use 0x1::Pontem;

    resource struct T {
    }

    public fun create(account: &signer): Pontem::T<T>  {
        // Create new token with denom "wow" (hex == 776f77).
        Pontem::tokenize<T>(account, 10, 0, x"776f77")
    }
}
```

And also deposit it to your balance:

```rust
script {
    use {{sender}}::ExampleToken;
    use 0x1::Account;

    fun main(sender: &signer) {
        let new_tokens = ExampleToken::create(sender);
        Account::deposit_to_sender(sender, new_tokens);
    }
}
```

More documentation about the feature provided by Dfinance module see in [pontem.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/pontem.move).

## Vector

[Vector](https://github.com/pontem-network/move-stdlib/blob/master/modules/vector.move) module contains functions to work with `vector` type.

For example:

```rust
script {
    use 0x1::Vector;

    fun main() {
        let v = Vector::empty<u64>();
        let i = 0;

        loop {
            if (i == 10) {
                break
            };

            Vector::push_back(&mut v, i);
            i = i + 1;
        };
    }
}
```

Vector module great describe in [Diem Vector](https://developers.diem.com/main/docs/move-primitives-vector) documentation.

## Signature

[Signature](https://github.com/pontem-network/move-stdlib/blob/master/modules/signature.move) module allows to verify ed25519 signature:

```rust
script {
    use 0x1::Signature;

    fun main(signature: vector<u8>, pub_key: vector<u8>, message: vector<u8>) {
        let is_verified = Signature::ed25519_verify(signature, pub_key, message);
        assert(is_verified, 101);
    }
}
```

## More

* [FixedPoint32](https://github.com/pontem-network/move-stdlib/blob/master/modules/fixedpoint32.move) - allows to work with 8 bytes fixed point numbers.
* [Debug](https://github.com/pontem-network/move-stdlib/blob/master/modules/debug.move) - debug module to print values and stack trace.
* [LCS](https://github.com/pontem-network/move-stdlib/blob/master/modules/lcs.move) - serialize Move value to [LCS (Libra Canonical Serialization)](https://github.com/librastartup/libra-canonical-serialization/blob/master/DOCUMENTATION.md). 
* [Math](https://github.com/pontem-network/move-stdlib/blob/master/modules/math.move) - math for using native U256 numbers.
* [U256](https://github.com/pontem-network/move-stdlib/blob/master/modules/u256.move) - U256 number implementation.
