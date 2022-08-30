# Getting Started

Use the v1.3.0 or higher version of Pontem Wallet to go through the current docs.

## Wallets Adapter

If you want to use [Aptos Wallet Adapter](https://github.com/hippospace/aptos-wallet-adapter), navigate to the [Wallet Adapter documentation](./wallet_adapter.md) and skip the current doc.

### Provider Browser Detection

When the page is loaded, the provider is integrated into the site page. You can check it this way:

```javascript
if (typeof window.pontem !== 'undefined') {
  console.log('Pontem Wallet is installed!');
}
```

After that, you need to request access to the site from the user, for this use the connect method:

```javascript
window.pontem.connect()
    .then(address => console.log(`Access for address ${address} allowed by user`))
    .catch(e => console.log('Access denied by user', e))
```

After that, you can fully interact with all wallet methods.
