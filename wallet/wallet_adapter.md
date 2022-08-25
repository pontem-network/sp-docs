The wallet adapter helps you to integrate many different wallets at once and use the same interface to interact with any supported wallet.

Developed by Hippo team, main repository - [https://github.com/hippospace/aptos-wallet-adapter](https://github.com/hippospace/aptos-wallet-adapter).

Supports:

- [Pontem Wallet](https://pontem.network/pontem-wallet)
- [Aptos official wallet](https://github.com/aptos-labs/aptos-core/releases/tag/wallet-v0.1.1)
- [Martian wallet](https://martianwallet.xyz/)
- [Fewcha wallet](https://fewcha.app/)
- [Hippo wallet](https://github.com/hippospace/hippo-wallet)
- [Hippo web wallet](https://hippo-wallet-test.web.app/)

# Installation

With `yarn`

```
yarn add @manahippo/aptos-wallet-adapter
```

With `npm`

```
npm install @manahippo/aptos-wallet-adapter
```

# Examples

## Add Pontem Wallet to an existing codebase (React Provider)

```typescript
import React from "react";
import {
  WalletProvider,
  HippoWalletAdapter,
  ...
  PontemWalletAdapter, // Import Pontem Wallet Adapter.
} from '@manahippo/aptos-wallet-adapter';

const wallets = () => [
  new HippoWalletAdapter(),
  new PontemWalletAdapter(), // Add Pontem Wallet Adapter to list of supported wallets.
  ...
  new HippoExtensionWalletAdapter(),
];

...

```

## Use React Provider

```typescript
import React from "react";
import {
  WalletProvider,
  HippoWalletAdapter,
  PontemWalletAdapter,
  AptosWalletAdapter,
  HippoExtensionWalletAdapter,
  MartianWalletAdapter,
  FewchaWalletAdapter,
} from '@manahippo/aptos-wallet-adapter';

const wallets = () => [
  new HippoWalletAdapter(),
  new PontemWalletAdapter(),
  new MartianWalletAdapter(),
  new AptosWalletAdapter(),
  new FewchaWalletAdapter(),
  new HippoExtensionWalletAdapter(),
];

const App: React.FC = () => {
  return (
    <WalletProvider
      wallets={wallets}
      onError={(error: Error) => {
        console.log('Handle Error Message', error)
      }}>
      {/* your website */}
    </WalletProvider>
  );
};

export default App;
```

# Web3 Hook

```typescript
import { useWallet } from '@manahippo/aptos-wallet-adapter';

const { connected, account, ...rest } = useWallet();

/*
  ** Properties available: **

  wallets: Wallet[]; - Array of wallets
  wallet: Wallet | null; - Selected wallet
  account(): AccountKeys | null; - Wallet info: address, publicKey, authKey
  connected: boolean; - check the website is connected yet
  connect(walletName: string): Promise<void>; - trigger connect popup
  disconnect(): Promise<void>; - trigger disconnect action
  signAndSubmitTransaction(
    transaction: TransactionPayload
  ): Promise<PendingTransaction>; - function to sign and submit the transaction to chain
*/
```

# Connect & Disconnect

```typescript
const { wallets, connect, disconnect, isConnected } = useWallet();
const wallet = 'PontemWallet';

if (!isConnected) {
  return (
    <Button
      onClick={() => {
        connect(wallet);
      }}
    >
      Connect
    </Button>
  );
} else {
  return (
    <Button
      onClick={() => {
        disconnect();
      }}
    >
      Disconnect
    </Button>
  );
}
```