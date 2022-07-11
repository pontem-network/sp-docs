&nbsp;

# Introduction to the Move Code Playground
&nbsp; ![Welcome to the Move Code Playground.](/assets/move-code-playground-tutorial-images/welcome.png "This is a sample image.") 
## Testing Your First Move Project
In this guide, you’ll use the Pontem Network [Move Code Playground](https://playground.pontem.network/) to play with an example Move project. Along the way, we'll walk through the key features of this browser-based IDE to help jumpstart your own Move projects.

### This quickstart guide shows you how to:

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

| ![Welcome to the Move Code Playground.](/assets/move-code-playground-tutorial-images/orientation.png "This is a sample image.") |
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
* A quick overview of what's in the example project
 * Modules
 * Scripts
 * Tests

## Compiling the Example Code
* Components
* Debugging common errors

## Running an Example Script
* Components
* Debugging common errors

<br><br>