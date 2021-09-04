# Standard Library


Standard **Move VM** library is default modules that already developed and developers can use it in their modules, scripts.

They all placed on the address **0x1**. So when you import something from **0x1**, you import standard modules, like:

```rust
use 0x1::DiemAccount;
use 0x1::Event;
use 0x1::PONT::PONT;
use 0x1::Diem::{Self, Diem};
...
```

You can look for actual standard modules in [move-stdlib](https://github.com/pontem-network/move-stdlib) repository.

## DiemTimestamp

[DiemTimestamp](https://github.com/pontem-network/move-stdlib/blob/master/modules/DiemTimestamp.move) module allows getting current UNIX timestamp of latest block.

Example:

```rust
script {
    use 0x1::DiemTimestamp as Time;

    fun main() {
        let _ = Time::now_microseconds();
    }
}
```

The method will return u64 value as UNIX timestamp of the latest block.

## DiemBlock

[DiemBlock](https://github.com/pontem-network/move-stdlib/blob/master/modules/DiemBlock.move) module allows getting current blockchain height.

Example:

```rust
script {
    use 0x1::DiemBlock as Block;

    fun main() {
        let _ = Block::get_current_block_height();
    }
}
```

The method will return u64 value as the height of the latest block.

## PONT 

[PONT](https://github.com/pontem-network/move-stdlib/blob/master/modules/PONT.move) module allows you to get a PONT type of currency that you are going to use in your code.

```rust
script {
    use 0x1::PONT::PONT;
    use 0x01::DiemAccount;

    fun transfer(sender: signer, payee: address, amount: u64) {
        // Withdraw tokens from sender account.
        let pont_tokens = DiemAccount::pnt_withdraw<PONT>(&sender, amount);

        // Transfer PONT tokens to payee balance.
        DiemAccount::pnt_deposit<PONT>(payee, pont_tokens);
    }
}
```

## Event

[Event](https://github.com/pontem-network/move-stdlib/blob/master/modules/Event.move) module allows us to emit events.

Example with emitting event contains provided number:

```rust
script {
    use 0x1::Signer;
    use 0x01::Event;

    fun sum(account: signer) {
        let addr = Signer::address_of(&account);

        // Create event emitter.
        let event_handle = Event::new_event_handle(&account);

        // Emit event.
        Event::emit_event(
           &mut event_handle,
           addr
        );

        // Destroy event emitter.
        Event::destroy_handle(event_handle);
    }
}
```

Or you can emit event from your module by creating event handler one time:

```rust
address {{sender}} {
    module MyEvent {
        use 0x1::Signer;
        use 0x1::Event;

        struct MyStruct has key {
            value: u64,
            event_handle: Event::EventHandle<u64>,
        }

        public fun initialize(account: &signer, value: u64) {
            let event_handle = Event::new_event_handle(account);
            
            move_to<MyStruct>(account, MyStruct {
                value,
                event_handle,
            });
        }

        public fun emit(account: &signer) acquires MyStruct {
            let my_struct = borrow_global_mut<MyStruct>(Signer::address_of(account));
            Event::emit_event(
                &mut my_struct.event_handle,
                my_struct.value,
            );
        }
    }
}
```

## Signer

[Signer](https://github.com/pontem-network/move-stdlib/blob/master/modules/Signer.move) module allows to work with the `signer` type. To get address of signer:

```rust
script {
    use 0x1::Signer;

    fun address_of_signer(sender: signer) {
        let _ = Signer::address_of(sender);
    }
}
```

Signer type is required for functions which work with resources, address of signer could be useful in case of resource related functions: `borrow_global`, `borrow_global_mut`, `exists`, `move_from`, `move_to`, `freeze`.

Read more about the signer type in [Diem's Signer](https://developers.diem.com/main/docs/move-primitives-signer) documentation.

## DiemAccount

[DiemAccount](https://github.com/pontem-network/move-stdlib/blob/master/modules/DiemAccount.move) module allows to work with user balances: get balances, deposit coins/tokens to balances, withdraw them to deposit in another module, etc.

Also, it creates a VASP account, if the account doesn't exist yet, and related data, like event handlers for sending/receiving payments. 

A lot of different methods can be used to send tokens from account A to account B.

Let's say you don't want to create VASP account, so you functions introduced by Pontem network:

{% hint style="info" %}
🧙‍♂️ Functions with `pnt_` prefix is not compatible with Diem, so you code wouldn't be migrated to Diem, yet there's no other way to work with Diem balances directly (store it in modules, etc).
{% endhint %}

```rust
script {
    use 0x1::Signer;
    use 0x1::DiemAccount;
    use 0x1::PONT::PONT;
    fun deposit(sender: signer, amount: u64) {
        // Withdraw PONT tokens from sender account.
        let pont_tokens = DiemAccount::pnt_withdraw<PONT>(&sender, amount);

        // Deposit PONT tokens to account.
        DiemAccount::pnt_deposit(Signer::address_of(&sender), pont_tokens);
    }
}
```

Let's say you are ready to create VASP account and use Diem functions:

```rust
script {
    use 0x1::DiemAccount;
    use 0x1::PONT::PONT;
    use 0x1::Signer;

    fun transfer_diem(tc_account: signer, sender: signer, payee: address, amount: u64) {
        // Create VASP accounts.
        DiemAccount::create_parent_vasp_account<PONT>(
            &tc_account,
            Signer::address_of(&sender),
            x"",
            x"626f62", // Account name, hex (Bob)
            true
        );


        DiemAccount::create_parent_vasp_account<PONT>(
            &tc_account,
            payee,
            x"",
            x"616c696365", // Account name, hex (Alice)
            true
        );
        
        // Do transfers.
        let cap = DiemAccount::extract_withdraw_capability(&sender);
        DiemAccount::pay_from<PONT>(&cap, payee, amount, x"", x"");
        DiemAccount::restore_withdraw_capability(cap);
    }
}
```

Or deposit to another module:

```rust
script {
    use {{address}}::Swap;
    use {{address}}::ExampleToken;

    use 0x1::PONT::PONT;
    use 0x1::DiemAccount as Account;

    fun main(sender: &signer, seller: address, price: u128) {
        let pont = Account::pnt_withdraw(sender, price);

        // Deposit NewToken to swap coins.
        Swap::swap<ExampleToken::Token, PONT>(sender, seller, pont);
    }
}
```

Also, get a balance:

```rust
script {
    use 0x1::Signer;
    use 0x1::PONT::PONT;
    use 0x1::DiemAccount;

    fun get_balance(sender: signer, addr: address) {
        // My balance.
        let my_balance = DiemAccount::balance<PONT>(Signer::address_of(&sender));

        // Someone balance.
        let someone_balance = DiemAccount::balance<PONT>(addr);

        assert(my_balance > 0, 101);
        assert(someone_balance > 0, 102);
    }
}
```

For the rest of the features of DiemAccount module look at [account.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/DiemAccount.move).

## Diem

[Diem](https://github.com/pontem-network/move-stdlib/blob/master/modules/Diem.move) module allows you to work with coins balances, get coins info, also register new tokens, etc.

First of all, Pontem module presents type for all balances in the system, it's `Diem::Diem`:

```rust
/// The `Diem` resource defines the Diem coin for each currency in
/// Diem. Each "coin" is coupled with a type `CoinType` specifying the
/// currency of the coin, and a `value` field specifying the value
/// of the coin (in the base units of the currency `CoinType`
/// and specified in the `CurrencyInfo` resource for that `CoinType`
/// published under the `CoreAddresses::CURRENCY_INFO_ADDRESS()` account address).
struct Diem<CoinType> has store {
    /// The value of this coin in the base units for `CoinType`
    value: u64
}
```

The value field contains information about actual balance for specific coin/token, e.g.:

```rust
script {
    use 0x1::Signer;
    use 0x1::DiemAccount;
    use 0x1::PONT::PONT;
    use 0x1::Diem::{Self, Diem}; // Use both Diem resource and Diem functions itself.

    fun deposit(sender: signer, amount: u64) {
        // Withdraw PONT tokens from sender account.
        let pont_tokens : Diem<PONT> = DiemAccount::pnt_withdraw<PONT>(&sender, amount);

        assert(Diem::value<PONT>(&pont_tokens) == amount, 101); // Throw an error if the withdrawn amount is wrong.

        // Deposit PONT tokens to account.
        DiemAccount::pnt_deposit(Signer::address_of(&sender), pont_tokens);
    }
}
```

Also, you can create an empty coin:

```rust
address {{sender}} {
    module BankPONT {
        use 0x1::Diem::{Self, Diem};
        use 0x1::PONT::PONT;

        struct Storage has key {
            balance: Diem<PONT>,
        }

        public fun create(account: &signer)  {
            move_to(account, Storage {
                balance: Diem::zero<PONT>()
            })
        }
    }
}
```

Get denom, decimals, and actual value:

```rust
script {
    use 0x1::Signer;
    use 0x1::Diem;
    use 0x1::DiemAccount;
    use 0x1::PONT::PONT;

    fun diem_example(sender: signer, amount: u64) {
        let pont_tokens = DiemAccount::pnt_withdraw<PONT>(&sender, amount);

        // Get denom vector<8>.
        let _ = Diem::currency_code<PONT>();

        // Get value of withdrawed xfi.
        let value = Diem::value(&pont_tokens);

        assert(amount == value, 101);

        DiemAccount::pnt_deposit(Signer::address_of(&sender), pont_tokens);
    }
}
```

And check if it's user token or system coin:

```rust
script {
    use {{sender}}::ExampleToken::Token;
    use 0x1::Diem;
    use 0x1::PONT::PONT;

    fun custom_token() {
        assert(Diem::is_currency<PONT>() == false, 101);
        assert(Diem::is_currency<Token>(), 102);
    }
}
```

More documentation about the feature provided by Dfinance module see in [Diem.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/Diem.move).

## Vector

[Vector](https://github.com/pontem-network/move-stdlib/blob/master/modules/Vector.move) module contains functions to work with `vector` type.

For example:

```rust
script {
    use 0x1::Vector;

    fun vec() {
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

[Signature](https://github.com/pontem-network/move-stdlib/blob/master/modules/Signature.move) module allows to verify ed25519 signature:

```rust
script {
    use 0x1::Signature;
    fun signature(signature: vector<u8>, pub_key: vector<u8>, message: vector<u8>) {
        let is_verified = Signature::ed25519_verify(signature, pub_key, message);
        assert(is_verified, 101);
    }
}
```

## More

Diem Standard Library contains a lot of other modules, some of its functions [restricted](https://github.com/pontem-network/move-stdlib#restricted) by Pontem. The rest of modules you can review in Pontem [Standard Library repository](https://github.com/pontem-network/move-stdlib/tree/master/modules).
