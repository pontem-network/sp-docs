# First Transaction

Here is guide how to send your first transaction in **Pontem** network using.

Let's try to send basic coins transfer transactions between two accounts.

**If you still don't have accounts, please create two accounts using [UI](./ui.md#account-creation) or [CLI](./cli.md#account-creation).**

Use [FAUCET](https://t.me/pontem_faucet_bot) to fund one of your accounts.

## UI

To send coins between accounts just do following steps:

* Open UI.
* Navigate to **"Accounts"** in the header menu.

![Account](/assets/module.png "Account")

* Click on **"send"** button near account which sends funds.

![Send](/assets/send.png "Send")

* Choose an account you want to send coins, for example **Bob**.
* Put the amount you want to send.
* Click on **"Make Transfer"** button.
* Click on **"Sign and Submit"** button on new modal.

![Send Form](/assets/send_form.png "Send Form").

Done! Once your transaction is confirmed, balances will be updated.

## CLI

To send tokens from CLI use next command:

```text
yarn run:api tx.balances.transfer <recipient> 1 --seed <seed>
```

Where:
* `<recipient>` - address of recipient, replace with actual one.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local nodes. 

Replace `<recipient>` with your actual address.

After your execute command and new transaction will be approved, the recipient will receive tokens.

## Transfer coins via script

{% hint style="info" %}
🧙‍♂️ Install [Dove](../move_vm/compiler_&_toolset.md) before continue with this tutorial.
{% endhint %}

Let's make your first smart contract in Move language, we will transfer coins using Move script.

We recommend to install Idea and extension so you easy work with Move language:

* [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=PontemNetwork.move-language)
* [IntelliJ IDEA Extension](https://plugins.jetbrains.com/plugin/14721-move-language)

Let's create new project using Dove:

```sh
dove new transfer_tokens --dialect polkadot  --address <address>
```

**Replace address with your sender address.**

// TODO: check code.

Create a new script:

```sh
touch ./scripts/transfer.move
```

And put next code inside:

```rustc
script {
    use 0x1::Account;
    use 0x1::PONT;

    fun transfer(account: &signer, payee: address, amount: u128) {
        // Deposit a sender PONT coins from native balance.
        // The function returns PONT balance resource.
        let pont = Account::deposit_native<PONT::T>(account, amount);

        // Transfer PONT resource to payee balance resource.
        Account::deposit<PONT::T>(account, payee, pont);
    }
}
```

This code deposits your native PONT coins into VM space and transfers it to the payee account. You can read more about native balances and VM space into [native balances](../move_vm/native_balances.md) documentation.

Let's create script transaction using dove:

```sh
dove ct 'transfer(<recipient>, <amount>)'
```

**Before run command replace:**

* `<recipient>` - address of recipient, replace with actual one.
* `<amount>` - coins amount to send.

See builded artifacts:

```sh
ls -la ./target/transactions
```

Use `0_transfer.mvt` to send transaction, see how:

* [Send script transaction using UI](./ui.md#script)
* [Send script transaction using CLI](./cli.md#script)

After the transaction is confirmed and added to the block, you will see how balances changed.
Congrats!

In the next parts we explain what's address, how to launch local nodes, and iterate with UI/CLI.

If you are interesting in Move language or Pallet see next parts:

* [Move Pallet](../move_vm/README.md)
* [Tutorials](../tutorials/access_control.md)
