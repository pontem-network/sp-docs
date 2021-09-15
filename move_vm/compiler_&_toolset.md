# Compiler & Toolset

We've developed a toolchain for the Move VM and language which allows for:

* **Compiling** Move code into bytecode.
* **Testing** Move code with tests and formal verifications.
* **Viewing** Move resources (stored data) from remote blockchain nodes.
* **Disassembling** Move binary and even read human readable results.

The toolchain is implemented by the Dove tool and several separate binaries, which you can install following this tutorial.

## Installation

All tools stored in our [Move-Tools](https://github.com/pontem-network/move-tools) Github repository.

You can download all tools for your operating system from [releases page](https://github.com/pontem-network/move-tools/releases).

After downloading, rename tool(s) by removing version and os and moving binary to `/usr/local/bin/`.

For example, for dove on linux:

```sh
mv ./dove-1.3.2-linux-x86_64 ./dove
chmod +x ./dove
mv ./dove /usr/local/bin/dove
```

For Windows:

1. Go to **"Program Files"** directory.
2. Create there **"dove"** directory.
3. Rename the downloaded file to **"dove"** and put it into **"dove"** directory.

Now dove is available from **"cmd"**.

To build tools from sources see [README](https://github.com/pontem-network/move-tools#move-tools).

## Dove

Dove is a Move compiler and package manager. Using Dove you can create your own Move smart contract application.

Let's create your first project:

```sh
dove new first_project --dialect pont  --address <address>
```

* Replace `<address>` with your address.

Navigate to `first_project` folder and examine what's automatically generated inside:

```sh
cd ./first_project
ls -la
```

* `Dove.toml` - configuration file.
* `modules` - place modules here.
* `scripts` - place scripts here.
* `tests` - place tests here.

Let's examine what is contained in `Dove.toml`:

```toml
[package]
name = "first_project1"
account_address = "<your address>"
dialect = "pont"

dependencies = [
    { git = "https://github.com/pontem-network/move-stdlib", tag = "v0.3.0"}
]
```

* `name` - name of project.
* `account_address` - address of your account, used during compilation.
* `dialect` - can be `diem`, or `pont` (SS58 Polkadot addresses).
* `dependencies` - list of dependencies, git (tag or branch also supported) or local folder (use `path`).

Let's create an empty script and build it:

```sh
touch ./scripts/test.move
```

Insert Move code:

```rust
script {
    fun test() {

    }
}
```

Build your empty project in one of two ways:

1. Without arguments:

```sh
dove build # Build script without providing arguments which can't be used in Substrate Pallet.
```

2. With arguments:

```sh
dove tx 'test()' # Build script with arguments.
```

Difference between the `build` and `tx` commands: 

- If you want to just `build` your scripts, use `build`. However, this command is mainly used for building modules (modules are similar to smart contracts in Move).
 
- if you want to simply send transactions to the Pontem node use `tx` and provide arguments. 

{% hint style="info" %}
Use the `tx` command only for scripts and use `build` for modules (similar concept to smart contracts in Move) 
{% endhint %}

For more information use the help function:

```sh
dove tx --help
```

See your built artifacts in `./artifacts` folder:

```sh
ls -la ./artifacts/scripts # Just built script
ls -la ./artifacts/transactions # Script with arguments for pallet.
```

### Script Transaction

Command `tx` allows you to create transactions for Polkadot or Kusama based chains with the Move Pallet.

`tx` takes a script identifier, type parameters, and arguments to create a transaction file as an artifact of work:

Example:

```sh
dove tx 'store_u64(60)'
```

This command searches for the script by name 'store_u64' in the script directory. Then it compiles it and creates a transaction file.

This command will fail if:

* There is no script with the name given name 'store_u64'.
* There is more than one script with the name 'store_64'.
* The passed parameters or type parameters do not match the script parameters.
* There are syntax errors in the script.
* You can use type parameters like in the move language.

Example:

```sh
dove tx 'create_account<0x01::PONT::PONT>()'
```

You allowed to use SS58 address format and other types:

```sh
dove tx 'create_account<5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY::MyToken::Token>()'
dove tx 'create_account(5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY, 10, true, [10, 20, 30, 40])'
```

Supported types:

**Numbers (u8, u64, u128):**

```sh
dove tx 'my_script(255, 18446744073709551615, 340282366920938463463374607431768211455)'
```

**Boolean:**

```sh
dove tx 'my_script(true, false)'
```

**Addresses:**

```sh
dove tx 'my_script(5GcFHawZJHgwymsu9X2F5Lt5sSX89TxxFgHAMJ7TnoUJKqJD, 0x1CF326C5AAA5AF9F0E2791E66310FE8F044FAADAF12567EAA0976959D1F7731F)'
```

**Vectors:**

```sh
dove tx 'my_script([10, 20, 1024])' // Vector u64
dove tx 'my_script([5GcFHawZJHgwymsu9X2F5Lt5sSX89TxxFgHAMJ7TnoUJKqJD, 0x1CF326C5AAA5AF9F0E2791E66310FE8F044FAADAF12567EAA0976959D1F7731F, 0x01])' // Vector addresses.
```

### Executor

Executor allows you to execute scripts without sending transactions to the chain.

See help:

```sh
dove run --help
```

Put next code inside `modules/Store.move`:

```rust
address {{sender}} {
    module Store {
        struct Store<T: store> has key {
            value: T
        }

        public fun store<T: store>(account: &signer, value: T) {
            move_to(account, Store<T> {
                value
            });
        }

        public fun destroy<T: store>(account: address): T acquires Store {
            let Store { value } = move_from<Store<T>>(account);
            value
        }
    }
}
```

After put next code inside `scripts/store.move`: 

```rust
script {
    use {{sender}}::Store;
    use 0x1::Signer;

    fun store_u64(acc: signer, a: u64) {
        Store::store(&acc, a);
        Store::destroy<u64>(Signer::address_of(&acc));
    }
}
```

Run following command to put number into storage with `run` command:

```rust
dove run 'store_u64(<account>, 100)' # Store number 100 in storage.
```

Replace `<account>` with your address.

Dove `run` command will publish provided modules and execute script with arguments.
The data generated by script will be stored into `./artifacts/storage/` folder.

### Tests

You can run tests on your smart contracts using dove.

Use same module and script from [executor](#executor) section.

Create a new test:

```sh
touch ./tests/store_test.move
```

Put the following code inside:

```rust
address {{sender}} {    
    module StoreTest {
        use 0x1::Signer;
        use {{sender}}::Store;

        #[test(
            account = @5GcFHawZJHgwymsu9X2F5Lt5sSX89TxxFgHAMJ7TnoUJKqJD,
        )]
        fun test_store(account: &signer) {
            let value: u64 = 100;
            Store::store(account, value); // Successful store value.
            let stored_value = Store::destroy<u64>(Signer::address_of(account)); // Successful destroy value.

            assert(stored_value == value, 101); // Throw error.
        }
    }
}
```

Provided code implements a test for module `Store`, and looks similar to the script we already implemented, yet it allows us to test your code.

See macro `test`, it allows you to provide a list of transaction [signers](https://developers.diem.com/main/docs/move-primitives-signer) that will be passed to the test function during execution.

Run tests:

```sh
dove test
```

See result of test execution output, e.g.:

```sh
[ PASS    ] 0xbeffe738f774f699ca7624bd45e8207f590cafb48bdca72fce02125480e7e116::StoreTest::test_store
Test result: OK. Total tests: 1; passed: 1; failed: 0
```

See other test options:

```sh
dove test --help
```

See more examples in [Bridge repository](https://github.com/pontem-network/bridge/blob/master/tests/test_1.move).

### Signers

Usually you have scripts that contain one signer. Signer is an account where a signed transaction contains a script. When Diem builds such a transaction using the `tx` command it adds information about signers to the transaction, so you shouldn't care about such arguments.
 
### Root and Treasury signers

As Diem is a very regulated network, often some functions of Diem Standard Library require Diem Treasury Signatures and Diem Root Signature.
 
If we compare Pontem to Diem, Pontem is not a very regulated network, so Pontem disabled part of functions that required Treasury and Root signatures, and at the same time provided a way to get Treasury and Root signatures immediately.
 
So, using Dove you can build a transaction using Treasury and Root signature by using `tr` and `dr` literals as arguments:

```sh
dove tx 'my_script(dr, tr, ...the rest of arguments)'
```

### More

To learn more commands see help:

```sh
dove --help
```

To continue with Dove and create your first scripts and modules read our [Move VM](../move_vm/scripts.md) pallet documentation.

## Language Server

**Language server is currently out of date and currently in process of migration in a separate project.**

## Resource viewer

**Resource viewer is currently out of date and pending migration inside dove in future versions.**

## Disassembler

Allows to disassemble compiled `.mv` (modules/scripts) files.

See help:

```sh
decompiler --help
```

Try to decompile .mv file:

```sh
decompiler --input <path to compiled module or script>
```
