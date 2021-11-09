# First Transaction

This is a guide on how to send your first transaction in **Pontem** network using Move.

Let's try to send basic coin transfer transactions between two accounts.

**If you still don't have accounts, please create two accounts using [UI](./ui.md#account-creation) or [CLI](./cli.md#account-creation).**

Use [FAUCET](https://t.me/pontem_faucet_bot) to fund one of your accounts.

## UI

To send coins between accounts just perform following steps:

![Account](/assets/accounts.png "Account")

1. Open the [Pontem Polkadot JS UI](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/accounts).
2. Navigate to **"Accounts"** in the header menu.
3. Click on **"send"** button near account which sends funds.

![Send](/assets/send.png "Send")

4. Choose an account you want to send coins, for example **5CAbdS5gefECWhvYUb12wYZL7vXWXAV1uPTa9N2Yx39cb2wn**.
5. Put the amount you want to send, e.g. 1 PONT.
6. Click on **"Make Transfer"** button.
7. Click on **"Sign and Submit"** button on new modal.

![Send Form](/assets/send_form_1.png "Send Form").

Done! Once your transaction is confirmed, balances will be updated.

## CLI

To send tokens from CLI use the following command:

```text
polkadot-js-api tx.balances.transfer <recipient> 10000000000 --seed "<seed>" --types ./types.json --ws wss://testnet.pontem.network/ws # Send 1 PONT.
```

{% hint style="info" %}
🧙‍♂️ Read more about [types file](./cli.md#account-creation).
{% endhint %}

Where:
* `<recipient>` - address of recipient, replace with actual one.
* `<seed>` - account seed. Can be replaced with `"//Bob"` or `"//Alice"` in case of local dev nodes. 
* The parameter `10000000000` is 1 PONT token, as PONT has 10 decimals.

Replace `<recipient>` with your actual address.

After you execute the command, the new transaction will be approved and the recipient will receive the tokens.

## Transfer coins via script

{% hint style="info" %}
🧙‍♂️ Install [Dove](../move_vm/compiler_&_toolset.md) before continue with this tutorial.
{% endhint %}

Let's make your first smart contract in Move language, we will transfer coins using Move script.

We recommend you install our IDE extension so you can easily work with the Move language:

* [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=PontemNetwork.move-language)
* [IntelliJ IDEA Extension](https://plugins.jetbrains.com/plugin/14721-move-language)

1. Create a new project using Dove:

```sh
dove new transfer_tokens --dialect pont --address <address>
```

**Replace address with the sender address.**

2. Create a new script:

```sh
touch ./scripts/transfer.move
```

3. Insert the following code inside:

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

This code transfers PONT coins between accounts, from a sender account to payee receiving address.

4. Let's create a script transaction using dove:

```sh
dove tx 'transfer(<recipient>, <amount>)'
```

{% hint style="info" %}
🧙‍♂️ **IT'S IMPORTANT:** your sender account should match `account_address` from Dove.toml, otherwise you will get a bad signer error.
{% endhint %}

**Before running command replace:**

* `<recipient>` - address of recipient, replace with actual one.
* `<amount>` - coins amount to send, e.g. `10000000000`.

5. See built artifacts:

```sh
ls -la ./artifacts/transactions
```

6. Use `transfer.mvt` to send transaction, see how to do it on UI or CLI:

* [Using UI](./ui.md#script)
* [Using CLI](./cli.md#script)

After the transaction is confirmed and added to the block, you will see how the balances changed.
Congratulations! You just performed your first transaction.

In the next sections we explain our Polkadot Parachain architecture, addresses, how to launch local nodes, and iterate with UI/CLI.

If you are interested in first learning more about the Move language or Pallet skip to these sections:

* [Move Pallet](../move_vm/README.md)
* [Tutorials](../tutorials/create_account.md)
