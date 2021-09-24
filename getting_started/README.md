# Getting Started

Welcome to the guide on how to connect to the Pontem Network and execute your first transaction. In this guide, we will move PONT coins from one account to another account using the Move language.

## Connection to network

1. To connect to the network you can use the Polkadot JS web user interface (UI) or command-line interface (CLI) to connect to our node. If you want to run a node locally skip to step 2. 

    {% hint style="info" %}
    🏝️ The easiest way to connect to our network is by connecting directly to our node on the [Pontem Polkadot JS UI](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet.pontem.network%2Fws#/accounts).

    You will also need to request test tokens from our[faucet](https://t.me/pontem_faucet_bot)
    {% endhint %}

    Our testnet node is deployed on this IP address:

    * `wss://testnet.pontem.network/ws`
    
    Use this IP address to configure the Polkadot JS UI or CLI: 

    * [How to configure Polkadot JS UI](./ui.md)
    * [How to configure Polkadot JS CLI](./cli.md)

2. Alternatively, if you want to test the Pontem Network locally on your machine, see our [local node](./local_node.md) build guide.

3. You might need to first create a private key account if needed:

    * [How to create account in UI](./ui.md#account-creation)
    * [How to create account in CLI](./cli.md#account-creation)

4. You will also need to fund your account with testnet tokens using the [Pontem Faucet](https://t.me/pontem_faucet_bot).

## Tools

5. In order to continue with this guide you will need to install our Dove toolchain which includes a package manager & compiler to not only install dependencies but also compile, test and deploy smart contracts from your IDE. 
    See [Dove installation guide](./../move_vm/compiler_&_toolset.md#Dove)
    
    {% hint style="success" %}
    Once installed, you can call the `Dove` function.
    {% endhint %}


After you installed Dove and configured UI/CLI let's move to the next step.
