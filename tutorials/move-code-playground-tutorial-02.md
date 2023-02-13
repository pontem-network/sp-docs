&nbsp;

# Working with Coins
&nbsp; ![Welcome to the Move Code Playground.](/assets/move-code-playground-tutorial-images/placeholder-image.jpg "Coins in the Move Code Playground.") 
## Your First Playground Coin
In this guide, you’ll use the Pontem Network [Move Code Playground](https://playground.pontem.network/) (MCP) to mint, store, and transfer movable coins. This tutorial will build on what we learned in the previous MCP tutorial to improve our Move programming skills and learn the basics of token logic.

### This quick start guide shows you how to:

* Create and upload a Move package to the MCP.
* Understand the basic concepts of creating movable coins within the MCP.
* Test the minting, storing, and transferring of coins within the MCP.

## Before You Begin
This tutorial builds on the previous Move Code Playground tutorial for basic concept development. To expand on a few core Move concepts, it assumes you are already familiar with using this online code editor to build, test, and run Move packages. 

If you are not yet familiar with the Move Code Playground, check out the previous tutorial [Testing Your First Move Project](https://docs.pontem.network/03.-tutorials/move-code-playground-tutorial-01) before continuing. 

For everyone else, let's mint some coins!

## Part 1 - Building a Move Package
For this tutorial, we will be creating our first custom Move package.

Although the MCP automatically loads an example-project when we first visit the IDE, it is a good practice to familiarize ourselves with packaging clean code for repeatable deployment.

In this section, we'll export the pre-loaded example-project package and remove any files that are unneccessary for working with coins. The remaining files will be repackaged in order to maintain a clean codebase that we can deploy back to the MCP as needed.

**In part 1, we'll review the following:**

* Exporting packages.
* Creating a Move project package.
* Importing packages.

### 1.1) To Export a Package from the MCP

Click the 'Export' button along the MCP's top toolbar. You will be prompted to confirm that you want to `Download the project "example-project" in a zip archive`.

Click 'Download', and you will find that the downloaded archive contains a `Move.toml` manifest file as well as all of the files from the MCP File Explorer pane.

Now let's modify this `example-project` to build our own `coins-project` package.

### 1.2) To Create a Move Project Package

When building custom Move packages, the zip archive structure must exactly match the taxonomy as outlined below. Errors within your archive's taxonomy or manifest file may prevent a successful import to the MCP.

#### To Begin
First, make a copy of the `example-project` archive that you downloaded in the previous step and modify its contents to match the following:

```
coins-project/
  scripts/
    create_balance.move
    mint.move
    transfer.move
  sources/
    Coins.move
  tests/
    (leave empty)
  Move.toml
```

#### Next, Give Your Project a Name
A `Move.toml` file must always be saved in your project's root directory. This is your project's manifest file. In addition to providing your package with name and version details, this file is also responsible for telling the MCP which dependencies to load and which addresses to pre-define. 

Let's modify the manifest that file you copied from the example-project to match the code below:

```
[package]
name = "coins-project"
version = "0.0.0"

[addresses]
Sender = "0x1"
Friend = "0x9"

[dependencies]
aptos-core = { git="https://github.com/aptos-labs/aptos-core", rev="367608ab5cf726039ff44bfdac1f2177210b1440", subdir="aptos-move/framework/move-stdlib/" }
```

Adding addresses and dependencies to our Move.toml file saves us the step of adding them manually within the MCP.

__Addresses Reference__

Our `coins-project` needs at least two addresses:
* A `Sender` (0x1) to use as the default stand-in for where this package's modules are stored.
* A `Friend` (0x9) address, to use as the recipient of our demonstration's coin transfer.

__Dependencies Reference__

The Move Code Playground uses two core dependencies:
* The `Aptos Framework` (aptos-move/framework/aptos-framework/).
* The `Move Std Lib` (aptos-move/framework/move-stdlib/).

#### Lastly, Zip and Import your Project
Now that we have built and configured the coins-project package: 
1. Zip the archive
1. Rename it `coins-project.zip`
1. Import the zip file to the MCP.

### 1.3) To Import a Package to the MCP

Before importing a package, click `Clear all data` along the MCP's top toolbar to reset the MCP.

To import a package, click the 'Import' button along the MCP's top toolbar. You will be prompted to `Drag and drop a zip archive with project here` or to `Upload` the archive from a folder.


## Part 2 - Working with Coins

The goal of this tutorial is a simple demonstration of coins logic within Move. It is not intended for production use. The code will allow us to create tokens (of a type just called 'Coin') at an address (of our choosing) and then to retrieve and transfer them as needed.

> *__Key Concept For Beginners:__<br/>Move is a sort of Resource-Oriented language. The simplified definition of this just states that resources (Coins in this tutorial) can not be duplicated or destroyed once created, they can only be moved. When working with coins, this is an especially essential component of the language.*
 
Further tutorials will explain in detail the syntax behind creating Move resources from scratch. For this demo, we will be simply executing the basic functions required to mint, store, and transfer coins as provided by the coins-project package we created in the previous step.

**In part 2, we will use the coins-project files to perform the following within the MCP:**

 * Set up the MCP workspace.
 * Understand coins as a concept.
 * Understand the coins-project files.
 * Use scripts to mint, store, and transfer coins.
 
Now, on to the code!

### 2.1) Setting Up the MCP Workspace

If you have already created your `coins-project` package, let's reset our MCP workspace so that we can focus only on working with coins for this tutorial.

First, click the `clear all data` button along the MCP's top toolbar. This will prompt you for a confirmation before proceeding. Click `ok` to erase all data from your MCP interface.

Then import your `coins-project` package. If successful, the MCP's `Explorer` tab will automatically populate with all of the files from your package.

Lastly, click `Build` along the MCP's top toolbar to prepare your code for use.

### 2.2) What is a Coin?

To demonstrate working with coins in Move, it is important to know that a 'coin' as a concept has a few properties that can be approached a number of different ways. 

For instance, we could create a coin as a resource type where each newly minted coin contains a unique identifier. In that scenario, if users holds 50 coins, they could conceivably send a single coin of a specific identifier to a friend, similar to sending a single NFT from a larger collection.

For the sake of this tutorial, we are opting for a conceptually simpler type of coin. If users hold 50 Coins in our example, they can only transfer **quantities** of those Coins, and not any specific individual Coin. So, a script that "mints 50 Coins" is really just increasing the simple count of an address's Coins balance by 50.

**To put it another way, in this tutorial we can think of "Working with Coins" to mean "Working with a balance of Coins".**

Accordingly, there are actually two core resources defined within our Coins module: one for `Balance` and one for `Coins`.

### 2.3) Understanding the Project Files

Now that we understand that working with Coins is as much about modifying account balances as it is about creating or moving coins, let's dive in to how this is achieved by our `coins-project` package.

**To work with Coins and Balances, users will need the following:**

* Addresses from which to send or receive Coins.
* Scripts to mint and transfer Coins.
* A Module to describe how Coins and Balance resources behave.

Before we begin, it helps to lay out how all of these components interact with each other.

**The Addresses**<br>
To begin, remember we defined two addressess in our Move.toml configuration file: `Sender` and `Friend`. For this tutorial, we will be considering ourselves as the `Sender`.

**The Scripts**<br>
In Move, scripts on their own do not contain resource definitions. That part is self-contained within each unique module. However, scripts are allowed to call the execution of module methods as needed, provided they identify the module whose specific behaviors will be called.

For example, the script `create_balance.move`, begins with `use Sender::Coins;` which explicitly allows the script's function `fun create_balance()` to access the methods defined within the module whose address is `Sender::Coins;`.

**The Module**<br>
The module `Coins.move` opens by declaring itself as `module Sender::Coins { ... } `. 

This declaration actually contains its storage address. Remember that in our Move.toml file, `Sender` is predefined as the address `0x1`? This means the module definition can be read as `module 0x1::Coins { ... } `. This becomes important later in production, as unique modules must have unique addresses.

For now, we just need to know that `Sender::Coins` contains all of the behaviors of the two Move resources that we will be using in this tutorial: `Coins` and `Balances`. As such, it includes functions for everything that is needed in the basic minting, depositing, withdrawing, burning, and retrieving of Coins.

Are we ready to mint some Coins? Almost!

### 2.4) Minting our First Coins

Now that we understand what kind of Coins we will be creating and the components needed to process their behaviors, it is time to get minting. 

**This section will walk us through the following:**

* Preparing an addresses for interacting with Coins.
* Minting Coins.
* Transferring Coins.

#### To Prepare an Address to Send or Receive Coins
As mentioned earlier, our Coins.move module defines two core resources:
* The `Coins` resource can be thought of as always containing an integer value. This integer represents a discrete quantity that can be added or subtracted from a Balance.
* The `Balance` resource can be thought of as a sort of key-value storage pair. This will exist at a specific user address and contain a value that represents the total balance of Coins deposited to it.

**Opening Balances**<br/>

| ![create_balance() script.](/assets/move-code-playground-tutorial-images/scriptCreateBalance.jpg "create_balance() script") |
|:--|
| *Fig.1 - create_balance() scripts.* |

To begin working with our tutorial Coins, we must first add a `Balance` resource to both the sender and the receiver addresses.

This is achieved by running the `create_balance()` script on each address.

**Let's give it a try.**

1. In the MCP's 'Run' command prompt, type `create_balance(Sender)`. 
1. To execute the script, press 'enter'.
1. This script will attempt to create a Coins-type Balance counter and store it at the `Sender` address.

This should produce the following message in the console:
```
Gas used: 17
Changed resource(s) under 1 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000001:
    Added type 0x1::Coins::Balance: [0, 0, 0, 0, 0, 0, 0, 0] (wrote 56 bytes)
      key 0x1::Coins::Balance {
          coins: store 0x1::Coins::Coins {
              val: 0
          }
      }
Wrote 56 bytes of resource ID's and data
```
The important takeaway in this message is that a value of `0` of the resource type `Coins` is now stored as a `Coins::Balance` under the `Sender` address (0x1).

> Similar to our previous tutorial on storing u128 values at an address, running this script a second time on the same address will result in an error.


#### To Mint Coins
| ![mint() script.](/assets/move-code-playground-tutorial-images/scriptMint50Coins.jpg "mint() script") |
|:--|
| *Fig.2 - mint() script.* |

1. In the MCP's 'Run' command prompt, type `mint(Sender, 50)`. 
1. To execute the script, press 'enter'.
1. This script will attempt to mint and deposit a quantity of 50 Coins to the `Balance` stored at the `Sender` address.

This should produce the following error message in the console:
```
Found more than one script with the specified name "mint"
```

Whoops! It looks like our `mint()` demo function needs a new name. Let's open the file `mint.move` and make the following modification:

Change the line:

`fun mint(acc: signer, amount: u64) {` 

to read

`fun mymint(acc: signer, amount: u64) {`.

Then try to 'Build' the project.

> Note: This change will only impact the MCP files and should be reflected in your own `coins-project` package archive.

Let's try to rerun the mint function as `mymint(Sender,50)`.

```
Gas used: 25
Changed resource(s) under 1 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000001:
    Changed type 0x1::Coins::Balance: [50, 0, 0, 0, 0, 0, 0, 0] (wrote 56 bytes)
         key 0x1::Coins::Balance {
             coins: store 0x1::Coins::Coins {
-                val: 0
+                val: 50
             }
         }
Wrote 56 bytes of resource ID's and data
```

Success!

We can now see that our initial Coins value of 0 has been removed and a new value of 50 has been added to our Sender account.

#### To Transfer Coins
| ![mint_and_deposit() script.](/assets/move-code-playground-tutorial-images/scriptTransfer20Coins.jpg "mint_and_deposit() script") |
|:--|
| *Fig.2 - mint_and_deposit() script.* |

1. In the MCP's 'Run' command prompt, type `mint_and_deposit(Sender, Friend, 20)`.
1. To execute the script, press 'enter'.
1. This script will attempt to withdraw a quantity of 50 Coins from the `Sender` account and deposit them to the `Balance` stored at the `Friend` address.

```
Execution aborted with code 101 in module 0000000000000000000000000000000000000000000000000000000000000001::Coins.
```

Hm. Let's take a look inside our `Coins.move` module to see what `code 101` means.

```
    /// Error when `Balance` doesn't exist on account.
    const ERR_BALANCE_NOT_EXISTS: u64 = 101;
    /// Error when `Balance` already exists on account.
    const ERR_BALANCE_EXISTS: u64 = 102;
```

It looks like we forgot to add a `Balance` resource on our Friend account.

Let's run the `create_balance()` script again, this time taking our `Friend` address as its parameter. This will allow us to deposit coins to the Friend address.

To test this, let's retry our transfer by running `mint_and_deposit(Sender, Friend, 20)`.

```
Gas used: 23
Changed resource(s) under 2 address(es):
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000001:
    Changed type 0x1::Coins::Balance: [30, 0, 0, 0, 0, 0, 0, 0] (wrote 56 bytes)
         key 0x1::Coins::Balance {
             coins: store 0x1::Coins::Coins {
-                val: 50
+                val: 30
             }
         }
  
Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000009:
    Changed type 0x1::Coins::Balance: [20, 0, 0, 0, 0, 0, 0, 0] (wrote 56 bytes)
         key 0x1::Coins::Balance {
             coins: store 0x1::Coins::Coins {
-                val: 0
+                val: 20
             }
         }
Wrote 112 bytes of resource ID's and data
```

Success! The console shows both sides of our transfer by noting `Changed resource(s) under 2 address(es)`:
* The first block represents our Sender address: `Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000001`.
* The second block represents our Friend: `Changed 1 resource(s) under address 0000000000000000000000000000000000000000000000000000000000000009`.

We can also see that the Sender Balance value of 50 is replaced by a value of 30, and the Friend Balance value of 0 is replaced by a value of 20.

We have now seen a most basic example of how Coins resources can be minted and transferred between account Balances using Move within the MCP. This is a conceptually simplified example, as a number of more advanced checks and balances are advised for production usage. These can be explored in greater depth by exploring the Standard Library [Accounts](https://github.com/pontem-network/pont-stdlib/blob/master/sources/PontAccount.move) and [Tokens](https://github.com/pontem-network/pont-stdlib/blob/master/sources/Token.move) files.

## Recap & Next Steps

In this tutorial, we extended our understanding of Move programming within the MCP. We learned to create and import our own Move packages. We covered the basic concepts of working with different coin types. And finally, we explored the core concepts to get us started with minting and transferring our own coins module. 

For more advanced resources, check out our other tutorials and documentation and join us on Discord.


<br><br>