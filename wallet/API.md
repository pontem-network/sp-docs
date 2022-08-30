## API Reference

### Extension Version

To get the installed extension version, use the following code.

```javascript
const extensionVersion = window.pontem.version;
console.log(`Pontem Wallet v${extensionVersion}`); // 1.3.0
```
### Connect

For the initial connection to the wallet, use the connect method. It requests access to the site from the user and returns the current account address.
If you already have access to the site, it will also return the current account address.

```javascript
window.pontem.connect()
  .then(address => console.log(`Access for address ${address} allowed by user`))
  .catch(e => console.log('Access denied by user', e))
```

### Change active account

To keep track of when a user changed their account, use the `onChangeAccount` method.
When the account is changed, it calls the method you passed in the first argument.
If the user at some point revokes the extension's access to the site, then this method will also be called.

```javascript
window.pontem.onChangeAccount((address) => {
  if(address) {
    console.log('New selected account: ', address);
  } else {
    console.log('The user has selected an account that is not allowed to access');
  }
})
```

### Get current account

To get the address of the current account, use the `account` method.

```javascript
window.pontem.account()
  .then(address => {
    if(address) {
      console.log('Account address: ', address);
    } else {
      console.log('The user has selected an account that is not allowed to access');
    }
  })
```

### Sign and Submit Transaction

To request a signature and send a transaction to the blockchain, use the `signAndSubmit` method.

`payload` - mandatory parameter containing the transaction body.<br>
`otherOptions` - optional parameter that overrides transaction parameters.

```javascript
const payload = {
  type: "entry_function_payload",
  function: "0x1::coin::transfer",
  type_arguments: ["0x1::aptos_coin::AptosCoin"],
  arguments: ["0xeb442855143ce3e26babc6152ad98e9da7db7f0820f08be3d006535b663a6292", "1000"]
};
const otherOptions = {
  max_gas_amount: '1000',
  gas_unit_price: '1',
  expiration_timestamp_secs: '1646793600',
  sequence_number: '10'
}
window.pontem.signAndSubmit(payload, otherOptions)
  .then(tx => {
    console.log('Transaction', tx)
  })
  .catch(e => console.log('Error', e))
```

### Sign Transaction ![API Sign Transaction](https://badgen.net/badge/included%20in/>=1.4.0)
To request a signature of transaction, use the `signTransaction` method.

`payload` - mandatory parameter containing the transaction body.<br>
`otherOptions` - optional parameter that overrides transaction parameters.

```javascript
const payload = {
  type: "entry_function_payload",
  function: "0x1::coin::transfer",
  type_arguments: ["0x1::aptos_coin::AptosCoin"],
  arguments: ["0xeb442855143ce3e26babc6152ad98e9da7db7f0820f08be3d006535b663a6292", "1000"]
};
const otherOptions = {
  max_gas_amount: '1000',
  gas_unit_price: '1',
  expiration_timestamp_secs: '1646793600',
  sequence_number: '10'
}
window.pontem.signTransaction(payload, otherOptions)
  .then(tx => {
    console.log('Transaction', tx)
  })
  .catch(e => console.log('Error', e))
```

### Sign Message ![API Sign Message](https://badgen.net/badge/included%20in/>=1.4.0)
To request a signature of message, use the `signMessage` method.

```javascript
window.pontem.signMessage('My super secret message for sign')
  .then(result => {
    console.log('Transaction', result)
  })
  .catch(e => console.log('Error', e))
```
