# Aptos tutorial

## Introduction

TODO: what blockchain is, assume user knows some stuff
TODO: what Aptos is
TODO: what Move is

### Aptos CLI

Go to the Aptos CLI releases page at https://github.com/aptos-labs/aptos-core/releases, 
and download zip archive for your OS. 

Archive contains `aptos` binary, which is consists of:
- `aptos move` namespace has everything related to the Move language:
   * compiler
   * test runner
   * deploying Move modules to the blockchain
   * executing transactions

- `aptos key` allows you to generate a new private key

- `aptos init` allows you to initialize your Aptos project, like:
    * set a private key for the project
    * specify REST API urls

Put it somewhere in your `$PATH`. 

### Creating new project

In this tutorial, let's make a new UserCoin token. 
You'll see that it's pretty easy to go from an idea to the deployed smart contract.

First, create a directory and initialize it with a "package" using aptos-cli:

```shell
mkdir usercoin
cd usercoin
aptos move init --name UserCoin
```

This should be the resulting directory structure:
```
usercoin/
├── sources/
└── Move.toml
```

TODO: talk about packages

`Move.toml` is a manifest file for the package. Here, you define package metadata, dependencies and addresses used in the code. 

Let's add address with name `Sender` and value `0x42` there. We'll use it to store our modules in the blockchain. 
`0x42` is the placeholder address, it should be changed to the address of the user later:

```toml
[package]
name = "UserCoin"
version = "0.0.0"

[dependencies]
AptosFramework = { git = "https://github.com/aptos-labs/aptos-core.git", subdir = "aptos-move/framework/aptos-framework/", rev = "main" }

[addresses]
Sender = "0x42"
```

TODO: talk about AptosFramework library, stdlib, other packages, local and git dependencies a bit

Let's add some code and compile. Add new file `UserCoin.move` under `sources/`. 

```shell
usercoin/
├── sources
│   └── UserCoin.move
└── Move.toml
```

We'll add a marker struct for our new coin.

```move
module Sender::UserCoin {
    struct UserCoin {}
}
```

Now that we have something to compile, let's do it 
```shell
~/usercoin $ ~/bin/aptos move compile
{
  "Result": [
    "0000000000000000000000000000000000000000000000000000000000000042::UserCoin"
  ]
}
```

First time compilation could take a while, as it fetches the `AptosFramework` dependency from the internet. 

Now let's add a `mint` function with `public(script)` visibility, which could be used in transactions 
```move
module Sender::UserCoin {
    // imports Coin struct from module Coin that resides on address AptosFramework.
    // AptosFramework is an address defined in the AptosFramework dependency and automatically 
    // available to all the package code
    use AptosFramework::Coin::Coin;
  
    struct UserCoin {}
  
    public(script) fun mint(): Coin<UserCoin> {
                              
    }
}
```

Let's talk about `Coin<UserCoin>`. It's a type used by all the coins in the Aptos blockchain, 
kind of like ERC20 if you're familiar with Etherium.
So, here we specify that the `mint` function "returns a set amount of `UserCoin` coin". 

`Coin` is an example of Move Resource. 
Resources are different from plain types as they can't be easily copied or dropped from a scope.

It's easier to think about Move resources as some physical objects, like a book, ticket or a 10$ dollars in cash.
You cannot easily copy a physical object, you need to actually print a new dollar note.

TODO: more on resources
TODO: what `copy` ability is

TODO: global storage (on address)
TODO: move_to / move_from / borrow_global

TODO: finish implementation of the UserCoin mint function with MintCapability

TODO: UserCoinTests.move
TODO: run tests

TODO: deploy module
TODO: execute transaction on the network
