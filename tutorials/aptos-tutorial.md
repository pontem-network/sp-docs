# Aptos tutorial

Aptos is a Layer 1 blockchain that allows developers to create their smart-contracts in Move language. 

Aptos is focused on delivering the safest and most production-ready Layer 1 blockchain in the world. 
The team is comprised of the original creators, researchers, designers, and builders of Diem,
the blockchain that was first built at Meta.

The key components of Aptos are AptosBFT consensus and the new Move language which allows developers to build safe 
and scalable decentralized applications. 

With this tutorial you will start learning Move language and create your first smart contract for Aptos.

### Move

Move is a smart-contract language created with a heavy focus on security. Built on Rust, 
it inherits features that prevent developers from inadvertently introducting critical vulnerabilities. 

The key feature of Move is the ability to define custom resource types with semantics inspired by linear
logic. A resource can never be copied, double spent or implicitly discarded, only moved between program storage
locations. These safety guarantees are enforced statically by Move’s type system. 

You can read the Move whitepaper here: [https://developers.diem.com/papers/diem-move-a-language-with-programmable-resources/2019-06-18.pdf](https://developers.diem.com/papers/diem-move-a-language-with-programmable-resources/2019-06-18.pdf) 

In this tutorial, we're going to create a simple module that allows users to store their username in the blockchain. 
This will allow us to explore unique features of Move and prepare for more complex projects. 

### Aptos CLI

First we need to install the Aptos CLI for tools to interact with Aptos. Go to the Aptos CLI [releases page](https://github.com/aptos-labs/aptos-core/releases), and download the zip file for your OS. 

The archive contains the `aptos` binary, which consists of:
- `aptos move` namespace has everything related to the Move language:
   * compiler
   * test runner
   * deploying Move modules to the blockchain
   * executing transactions

- `aptos key` allows you to generate a new private key

- `aptos init` allows you to initialize your Aptos project in order to:
    * set a private key for the project
    * specify REST API urls

Put the archive somewhere in your `$PATH`.

Optionally, you can also install Pontem's Move extension for CLion or PyCharm (all 2021.1+ versions are supported), 
and add the Intellij-Move plugin there. It provides support for the Move language.

To do this, go to [https://www.jetbrains.com/pycharm/download/](https://www.jetbrains.com/pycharm/download/) and follow instructions for your operating system.

After you install it, go to File -> Settings, select `Plugins` on the left, then `Marketplace` and 
search for the `Move language`.      

### Creating a new project

Move projects are called Packages, and contain multiple Modules. 

A Module is a smart-contract that combines types and functions, and provides a very unique set of rules and restrictions 
to manage relationships between them.  

A Package is a set of related Move modules which share common dependencies and are often published
to the same address in the Aptos blockchain.

First, create a directory and initialize it with a Package using the aptos binary:

```shell
mkdir userinfo
cd userinfo
aptos move init --name UserInfo
```
This should be the resulting directory structure of our new Package:
```
userinfo/
├── sources/
└── Move.toml
```

`sources/` - directory where you put your modules.  
`Move.toml` - manifest file for the package. Here, you define the package metadata, dependencies and addresses used in the code.

Let's add an address with name `sender` and value `0x42` under `[addresses]`. We're going to store all our modules in that
address in the Aptos blockchain. 

```toml
[package]
name = 'UserInfo'
version = '1.0.0'

[dependencies.AptosFramework]
git = 'https://github.com/aptos-labs/aptos-core.git'
rev = 'devnet'
subdir = 'aptos-move/framework/aptos-framework'

[addresses]
sender = "0x42"
```

The `move init` command automatically adds a dependency to the `AptosFramework` package. It also transitively adds a `MoveStdlib`
dependency. Let's compile that empty package to make the Aptos CLI fetch the dependencies and inspect them. 

Compiling for the first time could take a while because it fetches the whole `aptos-core` repo from Github.

```shell
~/userinfo $ ~/bin/aptos move compile
{
  "Result": []
}
```

Fetched dependencies as well as build artifacts are stored in the `build/` directory at the package root. 

```shell
build/
└── UserInfo
    ├── bytecode_modules
    ├── source_maps
    └── sources
        └── dependencies
            ├── AptosFramework
                ├── account.move
                ├── coin.move
                ........
            └── MoveStdlib
                ├── string.move
                ├── signer.move
                ├── vector.move
                ........
```

`MoveStdlib` - standard library of the Move language consisting of modules that are indispensable such as functions 
to work with vectors and signers. 

`AptosFramework` - a set of modules specific to the Aptos blockchain, like the `coin` module for an ERC20-like fungible token, 
and `accoint` for the account metadata. 

### Resources and storage

Every user of the Aptos blockchain has their own object storage located at the user address. 

There are built in methods which allow access to this storage from the Move code. 
```move
    /// check whether object is present in storage
    fun exists<T>(addr): bool;
    
    /// return read-only reference to the object
    fun borrow_global<T>(addr): &T;

    /// return mutable reference to the object
    fun borrow_global_mut<T>(addr): &mut T;

    /// add object to the storage
    fun move_to<T>(&signer, T);

    /// remove object from the storage
    fun move_from<T>(addr): T;
```

In that storage, smart-contracts store special structs called Resources. Those are marked with the `has key` ability after the name of the struct. 

To place a resource on a user address, a developer should have the `&signer` argument in scope and call the `move_to` function. The `&signer` data type in the Move language represents the sender account of the current transaction, and is used mostly for resource store and access restrictions in modules. Developers can extract the address of the transaction sender using `signer::address_of(&signer)` function.

All functions that fetch resource objects from storage require an annotation on the function signature. For that, 
add `acquires ResourceName` after the return type. 

### Implementation

#### UserProfile

First, let's add a `UserProfile` resource struct where we're going to store our username in a field of type `String`. Resources 
in Move are marked with the `has key` ability. 

```move
module sender::user_info {
    // imports String type from module string that resides on address std.
    // std is an address defined in the transitive std dependency of the AptosFramework dependency
    // and automatically available to all the package code
    use std::string::String;
    
    struct UserProfile has key { username: String }
}
```

There's no text strings in Move. All text is represented as `vector<u8>` objects or sequences of bytes. To use them more easily, 
the byte string literal was introduced, i.e. `b"MyUser", b"MyString"`.

Later, the `string` module was added to the standard library, which provides a `String` struct that wraps `vector<u8>` and ensures that
it contains only UTF8 characters. In our usernames, we're going to use those.  

#### Methods

Now let's add getter and setter methods for the `username`:

Let's implement getter first. We need to retrieve the `UserProfile` object from the user global storage. For that, we will use the
`borrow_global()` method which needs the address of the user store as a parameter. This also allows us to fetch the username 
of any other user.   

We also add `acquires UserProfile` which was explained earlier. 

```move
module sender::user_info {
    use std::string::String;
    
    struct UserProfile has key { username: String }
    
    public fun get_username(user_addr: address): String acquires UserProfile {
        // no need for the semicolon at the end, last expression is the returning one (just like in Rust)
        borrow_global<UserProfile>(user_addr).username
    }
}
```

Implementation of the setter is a bit more complex. The setter will be a public entry function. Unlike the usual function which can
be called only by other modules, entry functions can be called by sending a transaction to the Aptos blockchain 
containing arguments, generics and name/path in the function. 
Using public entry functions, users of your DApp can interact with deployed modules. 

We need to create an if-statement with two branches:
* first for the case when there's no `UserProfile` in the global storage, we need to create one with the correct username
* second is just to update username inside the existing `user_info`

We also need `&signer` here, as we want to only allow users themselves to change their profile. 

```move
module sender::user_info {
    use std::string::{String, utf8};
    use std::signer;

    struct UserProfile has key { username: String }

    public fun get_username(user_addr: address): String acquires UserProfile {
        borrow_global<UserProfile>(user_addr).username
    }

    public entry fun set_username(user_account: &signer, username_raw: vector<u8>) acquires UserProfile {
        // wrap username_raw (vector of bytes) to username string
        let username = utf8(username_raw);

        // get address of transaction sender
        let user_addr = signer::address_of(user_account);
        // `exists` just to check whether resource is present in storage
        if (!exists<UserProfile>(user_addr)) {
          let info_store = UserProfile{ username: username };
          move_to(user_account, info_store);
        } else {
          // `borrow_global_mut` is to fetch mutable reference, we can change resources in storage that way
          let existing_info_store = borrow_global_mut<UserProfile>(user_addr);
          existing_info_store.username = username;
        }
    }
}
```

### Tests

Now, let's write a test for our module to make sure everything works correctly. Test functions could be added anywhere, 
but it's a nice convention to store them in the `tests/{$MODULE_NAME}_tests.move` module. 

Create a `tests/` directory to the root of the package, and add `user_info_tests.move` there. Test functions are marked 
with the `#[test]` attribute, this way Move knows it's a test and can apply special treatment. 
We also mark the module itself with a `#[test_only]` attribute to remove it from the public namespace. 

```move
#[test_only]
module sender::user_info_tests {
    use std::string::utf8;
    use std::signer;

    use sender::user_info;

    // this named parameter to the test attribute allows to provide a signer to the test function,
    // it should be named the same way as parameter of the function
    #[test(user_account = @0x42)]
    public entry fun test_getter_setter(user_account: signer) {
        let username = b"MyUser";
        user_info::set_username(&user_account, username);

        let user_addr = signer::address_of(&user_account);
        // assert! macro for asserts, needs an expression and a failure error code
        assert!(user_info::get_username(user_addr) == utf8(username), 1);
    }
}
```

To run this test:

```shell
~/userinfo $ ~/bin/aptos move test
INCLUDING DEPENDENCY AptosExperimental
INCLUDING DEPENDENCY AptosFramework
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
BUILDING test-tutorial
Running Move unit tests
[ PASS    ] 0x42::user_info_tests::test_getter_setter
Test result: OK. Total tests: 1; passed: 1; failed: 0
{
  "Result": "Success"
}
```


### Deployment to Aptos blockchain

Let's deploy our module to the Aptos blockchain and make it available for everyone. 

We must initialize a new Aptos account and deployment configuration using the `aptos init` command. 

During execution of the command a new Aptos account will be created and test coins will be deposited to it so we can cover gas costs of the `UserInfo` module publication.

Use default parameters proposed by the command:

```shell
~/userinfo $ ~/bin/aptos init
Configuring for profile default
Enter your rest endpoint [Current: None | No input: https://fullnode.devnet.aptoslabs.com]

No rest url given, using https://fullnode.devnet.aptoslabs.com...
Enter your faucet endpoint [Current: None | No input: https://faucet.devnet.aptoslabs.com]

No faucet url given, using https://faucet.devnet.aptoslabs.com...
Enter your private key as a hex literal (0x...) [Current: None | No input: Generate new key (or keep one if present)]

No key given, generating key...

Account 6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42 doesn't exist, creating it and funding it with 10000 coins
Aptos is now set up for account 6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42!  Run `aptos help` for more information about commands
{
  "Result": "Success"
}
```

The deployment config which contains the private key of the new account will be created in `.aptos/config.yaml` in the root of the project. Don't share your private key with anyone! If you want to change the configuration or account, just run `aptos init` again and the configuration will be overwritten.

Copy the new generated address, in our example it's `0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42` (don't forget to add `0x` prefix to the start of the address), and replace the `sender` address in `Move.toml`.

You will get something like this but with your own address:

```toml
[addresses]
sender = "0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42"
```

Finally let's deploy the module using the `aptos move publish` command:

```shell
~/userinfo $ ~/bin/aptos move publish
{
  "Result": {
    "changes": [
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "authentication_key": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
          "coin_register_events": {
            "counter": "1",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "0"
              }
            }
          },
          "sequence_number": "1"
        },
        "event": "write_resource",
        "resource": "0x1::account::Account"
      },
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "packages": [
            {
              "package info...",
              "modules": [
                {
                  "modules info..."
                }
              ],
              "name": "test-tutorial",
              "upgrade_policy": {
                "policy": 1
              }
            }
          ]
        },
        "event": "write_resource",
        "resource": "0x1::code::PackageRegistry"
      },
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "coin": {
            "value": "9950"
          },
          "deposit_events": {
            "counter": "1",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "1"
              }
            }
          },
          "withdraw_events": {
            "counter": "0",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "2"
              }
            }
          }
        },
        "event": "write_resource",
        "resource": "0x1::coin::CoinStore<0x1::aptos_coin::AptosCoin>"
      },
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "event": "write_module"
      }
    ],
    "gas_used": 50,
    "success": true,
    "version": 20020209,
    "vm_status": "Executed successfully"
  }
}
```

You should get a similar output which means your module is now published.

## Setting username for our account

After we deployed the `user_info` module we can send a transaction to the Aptos blockchain which will call the `user_info::set_username` function and set the username for our account.

To execute the `set_username` function we need to utilize the `aptos move run` command. In this example we will use the username `AptosDev` as the username for our account which has to be provided as an argument. Also, `function-id` should contain a path to the deployed contract and function name. In your case the path will be different because you are using your own account.

```shell
~/userinfo $ ~/bin/aptos move run --function-id 0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42::user_info::set_username --args string:"AptosDev"
{
  "Result": {
    "changes": [
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "authentication_key": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
          "coin_register_events": {
            "counter": "1",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "0"
              }
            }
          },
          "sequence_number": "2"
        },
        "event": "write_resource",
        "resource": "0x1::account::Account"
      },
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "coin": {
            "value": "9946"
          },
          "deposit_events": {
            "counter": "1",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "1"
              }
            }
          },
          "withdraw_events": {
            "counter": "0",
            "guid": {
              "id": {
                "addr": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
                "creation_num": "2"
              }
            }
          }
        },
        "event": "write_resource",
        "resource": "0x1::coin::CoinStore<0x1::aptos_coin::AptosCoin>"
      },
      {
        "address": "6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42",
        "data": {
          "username": "AptosDev"
        },
        "event": "write_resource",
        "resource": "0x6e81b91a98226a2622b6993b9d14d3244fa8afacf622aa3cb11a32c799e93c42::user_info::UserProfile"
      }
    ],
    "gas_used": 4,
    "success": true,
    "version": 20027657,
    "vm_status": "Executed successfully"
  }
}
```

If the transaction is executed successfully, you will see an output similar to the one above.
Now you can query your username. In this example, the URL to the query resource would be:
 
[https://fullnode.devnet.aptoslabs.com/v1/accounts/0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42/resource/0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42::user_info::UserProfile](https://fullnode.devnet.aptoslabs.com/v1/accounts/0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42/resource/0x6E81B91A98226A2622B6993B9D14D3244FA8AFACF622AA3CB11A32C799E93C42::user_info::UserProfile)

To interact with the module deployed by you: replace the address of the account and the address of the module resource in the url with your own.

Enjoy! Please share your feedback and more tutorial requests in our [Discord](https://discord.gg/44QgPFHYqs) and [Telegram](https://t.me/pontemnetworkchat).
