# Aptos tutorial

Aptos is a Layer 1 blockchain that allows developers to create their smart-contracts in Move language. 

Aptos appears as the fastest, scalable and secure Layer 1 blockchain built by the original Meta Diem team 
for production usage in real world financial systems. The key components of Aptos are AptosBFT consensus 
and pure new Move language secured by design which allows to build safe realtime decentralized applications. 
With the current tutorial you will start learning Move language and create your first smart contract for Aptos.

### Move

Move is a smart-contract language, which was created with a heavy focus on security. Inspired by Rust, 
it has a lot of unique features that prevent developers from shooting themselves in a foot and miss 
the critical vulnerability.

In this tutorial, we're going to create a simple module that allows users to store their username in the blockchain. 
This will allows us to explore unique features of Move and prepare for the more serious projects.

### Aptos CLI

First we need to install Aptos CLI. Go to the Aptos CLI releases page at
https://github.com/aptos-labs/aptos-core/releases, and download zip archive for your OS. 

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

Optionally, you can also install CLion or PyCharm (all 2021.1+ versions are supported), 
and add Intellij-Move plugin there. It provides very nice support for the Move language.

For that, go to https://www.jetbrains.com/pycharm/download/ and follow instructions for your operating system.

After you install it, go to File -> Settings, select `Plugins` on the left, then `Marketplace` and 
search for the `Move language`.      

### Creating new project

Move projects are called Packages, and contain multiple Modules. 

Module is a smart-contract that combine types and functions, and provides a very unique set of rules and restrictions 
to manage relationships between them.  

Package is a set of related Move modules which share common dependencies and often published
to the same address in the Aptos blockchain.

First, create a directory and initialize it with a Package using aptos binary:

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

`sources/` - directory is where you put your modules.  
`Move.toml` - manifest file for the package. Here, you define package metadata, dependencies and addresses used in the code.

Let's add an address with name `Sender` and value `0x42` under the `[addresses]`. We're going to store all our modules at that
address in the Aptos blockchain. 

```toml
[package]
name = "UserInfo"
version = "0.0.0"

[dependencies]
AptosFramework = { git = "https://github.com/aptos-labs/aptos-core.git", subdir = "aptos-move/framework/aptos-framework/", rev = "main" }

[addresses]
Sender = "0x42"
```

`move init` command automatically adds a dependency on the `AptosFramework` package. It also transitively adds a `MoveStdlib`
dependency. Let's compile that empty package to make Aptos CLI fetch the dependencies, and inspect them. 

First time compilation could take a while, as it fetches the whole `aptos-core` repo from Github.

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
                ├── Account.move
                ├── AccountUtils.move
                ........
            └── MoveStdlib
                ├── ASCII.move
                ├── Signer.move
                ├── Vector.move
                ........
```

`MoveStdlib` - standard library of the Move language, it consists of modules that are really indispensable, like functions 
to work with vectors and signers. 

`AptosFramework` - a set of modules specific to Aptos blockchain, like `Coin` module for ERC20-like, and `Account` 
for the account metadata. 

### Resources and storage

TODO: talk about &signer and resources

Every user of the Aptos blockchain has it's own object storage, located at the user address. There are builtin methods, 
which allow to communicate with said storage from the Move code. 
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

In that storage, smart-contracts store a special structs called Resources. Those are marked with `has key` ability 
after the name of the struct. 

All functions that fetch resource objects from the storage require an annotation on the function signature. For that, 
add `acquires ResourceName` after the return type. 

### Implementation

#### UserProfile

First, let's add an `UserProfile` resource struct where we're going to store our username in a field of type `String`. Resources 
in Move are marked with `has key` ability. 

```move
module Sender::UserInfo {
    // imports String type from module ASCII that resides on address Std.
    // Std is an address defined in the transitive Std dependency of the AptosFramework dependency
    // and automatically available to all the package code
    use Std::ASCII::String;
    
    struct UserProfile has key { username: String }
}
```

There's no text strings in Move, all text is represented as `vector<u8>` objects, sequences of bytes. To use them easier, 
byte string literal were introduced, i.e. `b"MyUser", b"MyString"`.

Later, `ASCII` module was added to the standard library, which provides a `String` struct that wraps `vector<u8>` and ensures that
it contains only ASCII characters. In our usernames, we're going to use those.  

#### Methods

Now let's add getter and setter methods for the `username`:

Let's implement getter first. We need to retrieve `UserProfile` object from the user global storage. For that, we will use
`borrow_global()` method, which needs an address of the user store as a parameter. This also allows us to fetch username 
of any other user.   

We also add `acquires UserProfile` explained before. 

```move
module Sender::UserInfo {
    use Std::ASCII::String;
    use Std::Signer;
    
    struct UserProfile has key { username: String }
    
    public fun get_username(user_addr: address): String acquires UserProfile {
        // no need for the semicolon at the end, last expression is the returning one (just like in Rust)
        borrow_global<UserProfile>(user_addr).username
    }
}
```

Implementation of the setter is a bit more involved. We need to create an if-statement with two branches:
* first for the case when there's no `UserProfile` in the global storage. We need to create one with the correct username
* second is just to update username inside the existing `UserInfo`

We also need `&signer` here, as we want to only allow users themserves to change their profile. 

```move
module Sender::UserInfo {
    use Std::ASCII::String;
    use Std::Signer;

    struct UserProfile has key { username: String }

    public fun get_username(user_addr: address): String acquires UserProfile {
        borrow_global<UserProfile>(user_addr).username
    }

    public fun set_username(user_account: &signer, username: String) acquires UserProfile {
        let user_addr = Signer::address_of(user_account);
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
but it's a nice convention to store those in the `tests/{$MODULE_NAME}Tests.move` module. 

Create a `tests/` directory to the root of the package, and add `UserInfoTests.move` there. Test functions are marked 
with `#[test]` attribute, this way Move knows it's a test and can apply special treatment. 
We also mark module itself with `#[test_only]` attribute, to remove it from the public namespace. 

```move
#[test_only]
module Sender::UserInfoTests {
    use Std::ASCII;
  
    use Sender::UserInfo; 
    
    // this named parameter to the test attribute allows to provide a signer to the test function, 
    // it should be named the same way as parameter of the function
    #[test(user_account = @0x42)]
    fun test_getter_setter(user_account: signer) {
        // ASCII::string() function allows to create a `String` object from a bytestring  
        let username = ASCII::string(b"MyUser");
        UserInfo::set_username(&user_account, username);
      
        let user_addr = Signer::address_of(&user_account);
        // assert! macro for asserts, needs an expression and a failure error code
        assert!(UserInfo::get_username(user_addr) == username, 1);
    }          
}
```

Now, to run this test:

```shell
~/userinfo $ ~/bin/aptos move test
INCLUDING DEPENDENCY MoveStdlib
BUILDING AptosFramework
Running Move unit tests
[ PASS    ] Sender::UserInfoTests::test_getter_setter
Test result: OK. Total tests: 1; passed: 1; failed: 0
{
  "Result": "Success"
}
```

