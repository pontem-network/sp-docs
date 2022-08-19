# Getting Started

Before starting, please download v1.3.0 [dev release](https://github.com/pontem-network/pontem-wallet/releases/tag/v1.3.0) or higher if it is still not in Chrome Webstore.

Use the v1.3.0 Pontem wallet release during this documentation or higher.

### Provider Browser Detection

When the page is loaded, the provider is integrated into the site page. You can check it this way:

```js
if (typeof window.pontem !== 'undefined') {
  console.log('Pontem Wallet is installed!');
}
```

After that, you need to request access to the site from the user, for this use the connect method:

```js
window.pontem.connect()
    .then(address => console.log(`Access for address ${address} allowed by user`))
    .catch(e => console.log('Access denied by user', e))
```

After that, you can fully interact with all wallet methods.
