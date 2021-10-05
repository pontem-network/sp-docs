# `Dove.toml` manifest format

`Dove.toml` is a [TOML](https://toml.io/en/) file which describes properties of your Move project.

It consists of the following sections: 

* [`[package]`](#the-package-section) 
    * `name` - The name of the project
      
    * [`account_address`](#the-account_address-field) - The user's account address

    * [`dialect`](#the-dialect-field) - Address format of the project 
      
    * [`dependencies`](#the-dependencies-field) - Specifies a set of project dependencies
    
* [`[layout]`](#the-layout-section) - Directory layout of the project
    * `modules_dir`
    * `scripts_dir`
    * `tests_dir`
    
## The `[package]` section

```toml
[package]
name = "my_project"
account_address = "<your account address>"
dialect = "pont"
dependencies = [
    { git = "https://github.com/pontem-network/move-stdlib", branch = "v0.3.0" },
    { path = "./local_modules" }
]
```

### The `account_address` field

The address of the user account. 

1. You can use `{{sender}}` clause anywhere in your source code, 
   which will be replaced with the value of `account_address` at compilation time.

2. You can `dove run` the script and replace the signers' arguments with addresses of the signers.
 
3. You should wrap your module with `address {}` block. You have the option to use `{{sender}}` instead of a real address.

### The `dialect` field

This field controls which address format to use in your project. 

Possible values are:
* `"pont"`(default) - Polkadot addresses,
  i.e. `5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty`

* `"diem"` - Diem addresses, i.e. `0x1232414212412`

* `"dfinance"` - Dfinance bech32 addresses, 
  i.e. `wallet1pxqfjvnu0utauj8fctw2s7j4mfyvrsjd59c2u8`
  
### The `dependencies` field

This field is a TOML list where each entry specifies a set of modules to use as dependencies.

1. Git dependencies

```toml
dependencies = [ 
    { git = "https://github.com/pontem-network/move-stdlib", branch = "my-branch" } 
]
```

You can also add `tag`, `rev` and `path` modifiers. 
`path` will only include a subdirectory of the repo.  

2. Local dependencies 
```toml
dependencies = [ 
    { path = "./mymodules" } 
]
```

## The `[layout]` section

This allows customization of the layout of the project i.e. the directories where you store your Move source code.

```toml
[layout]
modules_dir = "modules"
scripts_dir = "scripts"
tests_dir = "tests"
```
