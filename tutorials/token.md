# Own Token

**WE NEED TO CHANGE STANDARD LIBRARY A LITTLE FIRST, SO CURRENTLY I HIDE IT**

The easiest way to make a token in the Pontem Network is to deploy a new module containing your token type. the token type is a structure that will be used in [generic](https://developers.diem.com/docs/move/move-basic-concepts/move-basics-generics) functions or structs, or in any other places.

As you can see, our Standard Library [Account.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/account.move) is managing balance resources for accounts by utilizing generic functions:

```rust
resource struct Balance<Token> {
    coin: Pontem::T<Token>
}
```

Balance is a generic resource structure that allows you to store balances for any type. 

`Pontem::T` is the generic resource struct in [Pontem.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/pontem.move):

```rust
resource struct T<Coin> {
    value: u128
}
```

This contains the amount of coins in your balance. It also implements all the math for balances.

You can read more about `Pontem` and `Account` modules in our [Standard Library](../move_vm/stdlib.md) documentation.

Let's create a new module:

```rust
module MyToken {
    struct T {} // New token type.
}
```

Now let's register the token in the Pontem network [Standard Library](../move_vm/stdlib.md).

1. Create a new script:

```rust
script {
    use 0x01::Pontem;
    use 0x01::Account;
    use {{sender}}::MyToken;

    fun new_token(account: &signer) {
         // Create new token.
         // Total supply - 1000000.000000
         // Decimals - 6
         // Denom - "XXX" (encoded in hex).
        let supply = 1000000000000;
        let my_tokens = Pontem::create_token<MyToken::T>(account, supply, 6, b"585858");

        // Let's deposit new token into your account.
        Account::deposit_to_sender(account, my_tokens);

        // Let's check if new tokens minted.
        assert(Account::balance<MyToken::T>(account) == supply, 101);
    }
}
```

As you can see we use the function `create_token` of the Pontem module to mint a new token and register information about it in global storage.

After this step, you can use `MyToken::T` as a type for your token and work with it in the same way you work with `0x01::PONT::T` type, see [Account.move](../move_vm/stdlib.md#account) for an example.

2. Compile both the module and script:

```sh
dove build
dove tx 'new_token()'
```

3. Deploy the new module and execute a transaction script:

```sh
pontem-cli tx.mvm.publishModule @./artifacts/modules/0_MyToken.mv 100000 --seed <seed>
pontem-cli tx.mvm.execute @./artifacts/transactions/new_token.mvt 1000000 --seed <seed>      
```

You now created your first token on the Pontem Network... Let's move an amount to your friend's account:

```rust
script {
    use 0x01::Account;
    use {{sender}}::MyToken;

    fun new_token(account: &signer, friend: address, amount: u128) {
        Account::pay_from_sender<MyToken::T>(account, friend, amount);
    }
}
```

Congratz! 