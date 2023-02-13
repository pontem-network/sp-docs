:exclamation:_You are reviewing an outdated version of the Pontem Wallet documentation! We recommend switching to the most recent version from the new_ [_official website_](https://docs.pontemwallet.xyz/)! :warning:<br>

## API Reference

### Extension Version

To get the installed extension version, use the following code.

```javascript
const extensionVersion = window.pontem.version;
console.log(`Pontem Wallet v${extensionVersion}`); // 2.0.0
```
### Connect

For the initial connection to the wallet, use the connect method. It requests access to the site from the user and returns the current account address.
If you already have access to the site, it will also return the current account address.

```javascript
window.pontem.connect()
  .then(address => console.log(`Access for address ${address} allowed by user`))
  .catch(e => console.log('Access denied by user', e))
```

### Check Connection Status ![API Check Connection Status](https://badgen.net/badge/included%20in/>=1.5.0)
To check if any wallet is connected to the page, use the `isConnected` method.

```javascript
window.pontem.isConnected()
  .then(result => {
    console.log('isConnected', result) // true or false
  })
  .catch(e => console.log('Error', e))
```

### Disconnecting ![API Disconnecting](https://badgen.net/badge/included%20in/>=1.5.0)
To disconnect the current account from the site, use the `disconnect` method.

> Important: all methods that require permission from the user will stop working, but the entry in the "Connected Sites" list of the account will not disappear. Only the user can remove access completely through the UI.<br>
If you call the `disconnect()` method and then `connect()`, the user will not be asked for permission again. This will only happen if the user manually removes access through the UI

```javascript
window.pontem.disconnect()
  .catch(e => console.log('Error', e))
```

### Change Active Account Event 

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

### Change Active Network Event ![API Change Active Network Event](https://badgen.net/badge/included%20in/>=1.6.0)

To keep track of when a user changed network, use the `onChangeNetwork` method.
When the network is changed, it calls the method you passed in the first argument.

```javascript
window.pontem.onChangeNetwork((network) => {
  console.log(network);
  // { api: 'https://fullnode.devnet.aptoslabs.com/v1/', chainId: '31', name: 'Aptos devnet' }
})
```

### Get Current Network ![API Get Current Network](https://badgen.net/badge/included%20in/>=1.6.0)

To get the current connected network, use the `network` method.

```javascript
window.pontem.network()
  .then(network => {
    console.log(network);
    // { api: 'https://fullnode.devnet.aptoslabs.com/v1/', chainId: '31', name: 'Aptos devnet' }
  })
```

### Get Current ChainId ![API Get Current Network](https://badgen.net/badge/included%20in/>=1.6.0)

To get the current chainId, use the `chainId` method.

```javascript
window.pontem.chainId()
  .then(chainId => {
    console.log(chainId); // 31
  })
```

### Get Current Account

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

### Get Public Key of the Current Account ![API Get Public Key](https://badgen.net/badge/included%20in/>=1.5.0)

To get the public key of the current account , use the `publicKey` method.

```javascript
window.pontem.publicKey()
  .then(key => {
    console.log('Public key: ', key);
  })
```

### Sign and Submit Transaction

To request a signature and send a transaction to the blockchain, use the `signAndSubmit` method.

`payload` - mandatory parameter containing the transaction body.<br>
`otherOptions` - optional parameter that overrides transaction parameters.

>Included in >=1.7.0.<br>
You can also pass a UInt8Array as transaction arguments, or an array with a `UInt8Array`. This forms a vector, or a vector of vectors.

```javascript
const payload = {
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

You can also pass a UInt8Array as transaction arguments, or an array with a `UInt8Array`. This forms a vector, or a vector of vectors.

```javascript
const payload = {
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

### Sign Message ![API Sign Message](https://badgen.net/badge/included%20in/>=1.7.0)
To request a signature of message, use the `signMessage` method.

```javascript
window.pontem.signMessage({
  address: true, // set true if you want include current address to message
  application: true, // // set true if you want include current application to message
  chainId: true, // set true if you want include current chain id to message
  message: 'a message i trust', // message like string or Uint8Array
  nonce: 'random nonce' // random nonce like string
})
  .then(result => {
    console.log('Signed Message', result)
  })
  .catch(e => console.log('Error', e))
```
