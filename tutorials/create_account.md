# Create account

Before you continue with the rest of our tutorials, create a Diem account to be compatible with the Diem ecosystem.

In this example we will create VASP account, that could just another account that holds balances and fires events:

```rust
script {
    use 0x1::DiemAccount;
    use 0x1::PONT::PONT;

    fun create_account(tc_account: signer, account: address) {
        DiemAccount::create_parent_vasp_account<PONT>(
            &tc_account, // Treasury signature.
            account, // Address of account.
            x"", // Auth key, ignore for now.
            x"626f62", // Account name, hex (Bob for example)
            true // Support all currencies
        );
    }
}
```

Compile provided script:

```sh
dove tx 'create_account(tr, <address>)
```

Replace `<address>` with your account and execute transaction using UI or CLI.

{% hint style="info" %}
✈️ See our instruction how to send transaction (execute/publish) using [UI](../getting_started/substrate.md) or [CLI](../getting_started/cli.md).
{% endhint %}

