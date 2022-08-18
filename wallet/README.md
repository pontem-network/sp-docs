# Introduction

![Pontem Wallet](/assets/wallet-promo.png "Pontem Wallet")

Welcome to the Pontem Wallet documentation. With this documentation, you will understand how to integrate Pontem Wallet into your web page and how to interact with it.

## Extension

You can find the current version of the wallet in the [Google Chrome Store](https://chrome.google.com/webstore/detail/pontem-wallet/phkbamefinggmakgklpkljjmgibohnba) and newest version in the [Github](https://github.com/pontem-network/pontem-wallet/releases). 

[![Download From Chrome Store](/assets/chrome-store.png "Download From Chrome Store")](https://chrome.google.com/webstore/detail/pontem-wallet/phkbamefinggmakgklpkljjmgibohnba) [![Download From Github](/assets/github-store.png "Download From Github")](https://github.com/pontem-network/pontem-wallet/releases).


To have all recent changes and bug fixes use Github version as it takes time to reach Chrome Store due to Google Review policy.

## Getting Started

Before starting, please download v1.2.0 [dev release](https://github.com/pontem-network/pontem-wallet/releases/tag/v1.2.0) or higher if it is still not in Chrome Webstore.

Use the v1.2.0 Pontem wallet release during this documentation or higher.

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
