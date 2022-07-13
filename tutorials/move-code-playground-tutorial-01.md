&nbsp;

# Introduction to the Move Code Playground
&nbsp; ![Welcome to the Move Code Playground.](/assets/move-code-playground-tutorial-images/welcome.png "This is a sample image.") 
## Testing Your First Move Project
In this guide, you’ll use the Pontem Network [Move Code Playground](https://playground.pontem.network/) to play with an example Move project. Along the way, we'll walk through the key features of this browser-based IDE to help jump start your own Move projects.

### This quick start guide shows you how to:

* Navigate the Move Code Playground (MCP) interface.
* Understand an example project's configuration within the MCP.
* Compile an example Move package within the MCP.
* Run an example script within the MCP.

## Before You Begin
Getting oriented to the Move Code Playground is easy. With just a web browser and a few key concepts, you can use this online code editor to build, test, and run Move packages on Move compatible blockchains like Aptos.

If you are not yet familiar with the Move language, the [Move Book](https://diem.github.io/move/) is a great developer resource. 

For everyone else, let's check out the interface.

## Navigating an Example Project
To help developers quickly familiarize themselves with Move packages, the MCP automatically loads an example-project when you first visit the IDE. This project appears in the top left navigation pane when you click on 'Projects'.

| ![Welcome to the Move Code Playground.](/assets/move-code-playground-tutorial-images/orientation.png "Move Code Playground Orientation.") |
|:--|
| *Fig.1 - Move Code Playground: Interface and pre-loaded example-project.* |

In order to expand the file tree, simply click on the 'example-project' label within the "Explorer" panel.

In this section, we'll expand the example project tree to highlight how your Move projects should be set up. We will briefly touch on the following key concepts:

* The Move folder structure
* Project dependencies
* Addresses
* Development reminders


#### A Move project folder structure

```
example-project/
  scripts/
    file_name.move
  sources/
    Filename.move
  tests/
    FileName.move
```
**Within the folder structure: <br>**
 All Modules are listed under `sources/`.<br>
 The scripts to interact with Modules are listed under `scripts/`.<br>
 And all test files appropriately go under `tests/`.



#### Project Dependencies
Clicking on the left navigation panel's 'Dependencies' icon reveals that the Move Code Playground comes pre-loaded with two dependencies:
* AptosFramework - A set of modules specific to the Aptos blockchain.
* MoveStdlib - The standard library of the Move language.

*Note: When building your own projects, you may need to add these manually.*

| Description       | Url                                       | Rev                           | Subdir |
| -------------     | -------------                             |:-------------                :|--------|
| Aptos Framework   | https://github.com/aptos-labs/aptos-core  | 367608ab5cf726039ff44bfdac1f2177210b1440     | aptos-move/framework/aptos-framework/ |
| Move Std&nbsp;Lib | https://github.com/aptos-labs/aptos-core  | 367608ab5cf726039ff44bfdac1f2177210b1440     | aptos-move/framework/move-stdlib/     |

#### Addresses
Clicking on the left navigation panel's 'Addresses' icon reveals that the Move Code Playground comes pre-loaded with one Address:

*Note: When building your own projects, you may need to add these manually.*

| Name     | Address    | Description                                        |
| ---------| -----------|----------------------------------------------------|
| Sender   | 0x1        | Default address for use as a stand-in for storing this package's modules.     |

<div style="background:rgb(252, 249, 233);padding:1px 0px 10px 20px;border-radius:5px;">

### Important Development Reminders
#### Project Dependencies
If beginning a project from scratch within the MCP, the StdLib dependency listed above must be added manually in order to utilize Move's core library features.
#### Addresses
In development, addresses can be abbreviated (0x1, 0x2, etc.), but they must be replaced by 'real' addresses before code can be deployed to production.
#### Configuration File
If you export the example project, you will notice a configuration file ("Move.toml") in the zipped folder that does not appear in the MCP directory tree. Opening this reveals the project's dependencies and addresses. Creating and importing your own "Move.toml" files can be an easy way to skip manually entering dependencies while testing new code in the Playground.
</div>
<br>

Now that we understand the example project's configuration, let's dive in to compiling and running some code.

## Understanding the Example Project

The goal of this first example is a simple demonstration of moving a generic resource. The code will allow us to store a value (of type u128) at an address (of our choosing) and then to retrieve it at a later time.

If you are programming in Move for the first time, it is key to understand two basic concepts before we begin: 

* First, that Move is a sort of Resource-Oriented language. The simplified definition of this just states that resources can not be duplicated or destroyed once created, they can only be moved.

* Second, that Move's compiler strictly enforces the best practices needed to create the safe and efficient code that is needed specifically for blockchain deployment. This ensures that the first point listed above is baked-in to all code deployed for use.
 
Further tutorials will explain in detail the syntax and methodology behind constructing Move files, for our first demo, let's just get familiar with executing basic functions within the MCP.

#### For this first demo, you will use the example-project files to perform the following within the MCP:

 * Compile a demo package.
 * Store a value at a demo address.
 * Retrieve the value from your demo address.
 * Read common debugging messages.
 
Now, on to the code!

## Compiling the Example Code

As is the case with all Move packages: This example-project's modules are defined in the 'sources' sub-folder and we will use the functions defined within the 'scripts' sub-folder to interact with these modules.

#### Our demo uses the following files from the example-project:

```
example-project/
  scripts/
    get_u128.move
    store_u128.move
  sources/
    Storage.move
```

Before we run any scripts, let's make sure the package is compiled. To do this, click 'Build' in the top left navigation menu. A build status will be displayed in the console at the bottom of the screen (Fig.2).

| ![MCP Build Screenshot.](/assets/move-code-playground-tutorial-images/build-demo-screen.png "Build Screenshot.") |
|:--|
| *Fig.2 - Move Code Playground: Build button and console message.* |

#### Build Success Message

A successful build will return a timestamp and console log message: `The project was successfully built` along with an execution time `(0.04s)`.

#### Build Error messages
A failed build will return error messages in the console. To test this, open the file `/scripts/get_u128.move` and make the following modification:

Change the line:

`let _ = Storage::get<u128>(&account);` 

to read

`let A = Storage::get<u128>(&account);`.

Then try to 'Build' the project.

Your console will now light up with warnings and errors. Since this `get_u128` file uses external dependencies, the actual error may be buried at the bottom of a list. Scroll to the bottom of the console and you'll find the culprit (Fig.3):


| ![MCP Error Console.](/assets/move-code-playground-tutorial-images/build-error-demo.png "MCP Error Console.") |
|:--|
| *Fig.3 - Move Code Playground: Build error message.* |

The console window lets us know we made a simple naming convention error:

```
error[E02010]: invalid name
  ┌─ /example-project/scripts/get_u128.move:6:13
  │
6 │         let A = Storage::get<u128>(&account);
  │             ^ Invalid local variable name 'A'. Local variable names must start with 'a'..'z' (or '_')
```

Change the `let A ` back to a `let _ `, hit 'Build' and we are back in business. 

Let's move on to running our first script.


## Running an Example Script

In order to run scripts within the Move Code Playground. Click the 'Run Script' link on the left navigation pane (Fig.4). From here, you will type commands directly into the prompt in the 'Run' pane.


| ![MCP Run Script Screenshot.](/assets/move-code-playground-tutorial-images/run-demo.png "MCP Run Script Screenshot.") |
|:--|
| *Fig.4 - Move Code Playground: Run Script Command Prompt.* |


For our demo, we'll be running the `get_u128()` and `store_u128()` functions. Both of these interact with our `Storage` resource, which is defined locally in our `sources/Storage.move` file. We are also going to be using a new demo address `0x5`.

Let's give it a try.

### Getting a Value From a Specified Address

1. In the 'Run' command prompt, type `get_u128(0x5)`. 
2. To execute the script, press 'enter'.
3. This script will attempt to get a u128-type value from the `0x5` address.

This should produce the following error message in the console:

```
Execution aborted with code 102 in module 0000000000000000000000000000000000000000000000000000000000000001::Storage.
```

What has gone wrong? The message is letting us know to look in the `Storage` module at address `000...0x1` for `code 102`. If we remember from earlier in the tutorial that address `0x1` is our example-project's pre-defined `Sender` address, we can really understand the error message to be read as below:

```
Execution aborted with code 102 in module Sender::Storage.
```

Let's take a look in our `Storage` module to see what is going on.

### The Storage Module and Address

It is important to know that in our example-project package, `Storage` as a module is bound to the `Sender` address (0x1) as defined locally within the `/sources/Storage.move` file:

```
module Sender::Storage {
    ...
}
```

This is equivalent to defining the module as:
```
module 0x1::Storage {
    ...
}
```

A quick scan of the rest of our `Storage` module reveals that error `code 102` is a result of nothing being found at the specified address `0x5`.

```
module Sender::Storage {
 ...
 public fun get<T: store>(account: &signer): T acquires Storage {
   ...
   // Check if resource exists on address, otherwise throw error with code 102.
   assert!(exists<Storage<T>>(addr), 102);
   ...
 }
 ...
}
```

This is an obvious error, but it is helpful to show that our demo address `0x5` holds no values by default. Let's now use a script to store a value there.

*Note: In your own future projects, `Sender` may be replaced by other addresses. And in production, all modules will have unique addresses.*


### Storing a Value at a Specified Address

To demonstrate how this looks in Move, we are going to run a basic script that allows us to store a simple value (of type u128) at an address `0x5` and then to retrieve it later.

Let's give it a try.

1. In the 'Run' command prompt, type `store_u128(0x5, 123)`. 
2. To execute the script, press 'enter'.
3. This script will attempt to store a u128-type value of `123` in the `0x5` address.

This should produce the following message in the console:

```
Gas used: 14
Changed resource(s) under 1 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000005:
    Added type 0x1::Storage::Storage<u128>: [123, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0] (wrote 67 bytes)
      key 0x1::Storage::Storage<u128> {
          val: 123u128
      }
Wrote 67 bytes of resource ID's and data
```

There is a lot to unpack here in further documentation, but the general takeaway is that a value of `123u128` is now stored under address `0x5`.

Let's try adding another value to the same address.

1. In the 'Run' command prompt, type `store_u128(0x5, 456)`. 
2. To execute the script, press 'enter'.
3. This script will attempt to store a u128-type value of `456` in the `0x5` address.

```
Execution aborted with code 101 in module 0000000000000000000000000000000000000000000000000000000000000001::Storage.
```

Whoops! What happened? We have forgotten another of the key concepts in Move: One unique resource for each address. Since we are currently holding a Storage resource at `0x5`, we cannot overwrite what already exists there or replace it with another Storage resource.

But what if we...wait for it...move that value from `0x5`?

1. In the 'Run' command prompt, type `get_u128(0x5)`. 
2. To execute the script, press 'enter'.
3. This script will attempt to get a u128-type value from the `0x5` address.

Success!

```
Gas used: 10
Changed resource(s) under 1 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000005:
    Deleted type 0x1::Storage::Storage<u128> (wrote 51 bytes)
      key 0x1::Storage::Storage<u128> {
          val: 123u128
      }
Wrote 51 bytes of resource ID's and data
```

Again, a lot to unpack, but the general takeaway is that we retrieved that 'val' of `123u128` from `0x5`, and you can even see it has been deleted as a resource in the previous line, freeing the space for another Storage resource.

Let's re-try adding that `456` value to `0x5` by executing ```store_u128(0x5, 456)``` again.

```
Gas used: 14
Changed resource(s) under 1 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000005:
    Added type 0x1::Storage::Storage<u128>: [200, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0] (wrote 67 bytes)
      key 0x1::Storage::Storage<u128> {
          val: 456u128
      }
Wrote 67 bytes of resource ID's and data
```
Success!

We have now seen a most basic example of how generic types can be stored and retrieved using Move within the Move Code Playground. The power of Move goes far beyond this, however. As further tutorials will show how we can use the same basic principles to define our own resource types to represent all sorts of things virtually.

## Recap & Next Steps

In this tutorial, we covered a number of core concepts to get started with the Move Code Playground. From touring the interface to running basic scripts and debugging common errors. For more advanced resources, check out or other tutorials and documentation and join us on Discord.







<br><br>