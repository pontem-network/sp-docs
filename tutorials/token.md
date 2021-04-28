# Own Token

Easiest way to make a token in Pontem network it's to deploy a new module containing your token type. Token type is a structure that will be used in [generic](https://developers.diem.com/main/docs/move-basics-generics) functions or structs, or in any other places.

As you can see, our Standard Library [Account.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/account.move) is managing balances resources for accounts by utilizing generic functions:

```rustc
resource struct Balance<Token> {
    coin: Pontem::T<Token>
}
```

Balance is a resource generic structure that allows you to store balance for any type. 

`Pontem::T` is the resource generic struct in [Pontem.move](https://github.com/pontem-network/move-stdlib/blob/master/modules/pontem.move):

```rustc
resource struct T<Coin> {
    value: u128
}
```

That contains the amount of your coins placed on your balance, also it implements all balances math.

You can read more about `Pontem` and `Account` modules in our [Standard Library](../move_vm/stdlib.md) documentation.

Let's create a new module:

```rustc
module MyToken {
    struct T {} // New token type.
}
```

Now let's register token in Pontem network [Standard Library](../move_vm/stdlib.md), create new script:

```rustc
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

        // Let's deposit new token on your account.
        Account::deposit_to_sender(account, my_tokens);

        // Let's check if new tokens minted.
        assert(Account::balance<MyToken::T>(account) == supply, 101);
    }
}
```

As you see we use function `create_token` of Pontem module to mint new token, register information about it in global storage.
After current step you can use `MyToken::T` as type of your token and work with it in same way you work with `0x01::PONT::T` type, see [Account.move](../move_vm/stdlib.md#account) for example.

Compile both module and script:

```sh
dove build
dove ct 'new_token()'
```

Deploy new module and execute transaction script:

```sh
pontem-cli tx.mvm.publishModule @./target/modules/0_MyToken.mv 100000 --seed <seed>
pontem-cli tx.mvm.execute @./target/transactions/new_token.mvt 1000000 --seed <seed>      
```

 Your created your first token in Pontem network. Let's move some amount of your tokens to your friend account:

```rustc
script {
    use 0x01::Account;
    use {{sender}}::MyToken;

    fun new_token(account: &signer, friend: address, amount: u128) {
        Account::pay_from_sender<MyToken::T>(account, friend, amount);
    }
}
```

Congrats!
