# Arguments

The main function of a script can have arguments and will be executed when you send a transaction with your script.

With the `dove tx` command you can pass arguments to script functions and build it. 

For more information see help:

```text
dove tx --help
```

Calling the main function, developers can pass arguments to the scrip. For example: 

```text
dove tx 'my_script<0x1::PONT::PONT>(1, [1, 2, 3], 1exaAg2VJRQbyUBAeXcktChCAqjVP9TUxF3zo23R2T6EGdE, false)'
```

The command both compiles the script file and adds arguments into one file with a `*.mvt` extension. The file can be used to send an `execute` (script) transaction to the network.

From this example you can see that Dove's **"tx"** command supports different kind of arguments such as:

* Numbers (u8, u64, u128)
* Boolean
* Addresses
* Vectors
* Type parameters (generics).
