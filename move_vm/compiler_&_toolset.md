# Compiler & Toolset

We've developed a toolset for the Move VM and language which allows for:

* **Compiling** Move code into bytecode.
* **Testing** Move code with tests and formal verification.
* **Viewing** Move resources (stored data) from remote blockchain nodes.
* **Disassembling** Move binary and even read human readable result.

The toolset is implemented by Dove tool and several separate binaries, which you can install following the current tutorial.

## Installation

All tools stored in our [Move-Tools](https://github.com/pontem-network/move-tools) Github repository.

You can download all tools from [releases page](https://github.com/pontem-network/move-tools/releases).

After downloading rename tool(s) removing version and os and moving binary to `/usr/local/bin/`.

For example, for dove:

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

Dove is Move compiler and package manager. Using Dove you can create your own Move smart contract projects.

Let's create first project:

```sh
dove new first_project --dialect pont  --address <address>
```

* Replace `<address>` with your address.

Navigate to `first_project` folder and see what's automatically generated inside:

```sh
cd ./first_project
ls -la
```

* `Dove.toml` - configuration file.
* `modules` - place modules here.
* `scripts` - place scripts here.
* `tests` - place tests here.

Let's see what contains `Dove.toml`:

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

Let's create empty script and build it:

```sh
touch ./scripts/test.move
```

Put there Move code:

```rustc
script {
    fun test() {

    }
}
```

Build your empty project:

```sh
dove build # Build script without providing arguments, so can't use in pallet.
dove tx 'test()' # Build script with arguments.
```

There is a difference between `build` and `tx` commands, if you want just to `build` your scripts use `build`, if you want to send transactions to the Pontem node use `tx` and provide arguments. 

Use `tx` only for scripts, build modules with `build` command.

```sh
dove tx --help
```

See your builded artifacts in `./artifacts` folder:

```sh
ls -la ./artifacts/scripts # Just built script
ls -la ./artifacts/transactions # Script with arguments for pallet.
```

### Script Transaction

Command `tx` allows you to create transactions for an Polkadot based chain with Move Pallet.

`tx` takes script identifier, type parameters, and arguments and creates a transaction file as an artifact of work:

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

```rustc
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

```rustc
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

```rustc
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

```rustc
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
