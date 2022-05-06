# Move Playground

![Move Playground](/assets/playground.png "Move Playground")

The Move Playground is a web version of the Move VM, Move CLI and basic IDE. This allows developer to write, test, build their Smart Contracts in the browser.

## Quick Guide

### Create module

1. Go to [Move Playground](https://playground.pontem.network/) and create a new project by clicking on **"+" near "Projects**".
2. Put the name of your project into the input field.
3. Click on the created project.
4. Add a new module to the project by clicking on **"+"** near the `sources` folder.
5. Put the name of the module in the input field, e.g. `test_module.move` (file extension is required).
6. Put your Move code inside, e.g.:

```rust
module 0x01::TestModule {
}
```

Click on the 🛠️ button in the top menu to build the new module then close the console.

### Run script

1. Create a new script similarly to how you created a module (but inside the script folder), e.g. `test_script.move`.
2. Put the following code inside:

```rust
script {
   fun test_script(val: u128) {
     assert!(val == 15, 101);
   }
}
```

3. Build it by clicking the 🛠️ button in the top menu.
4. Navigate to the `Run` section in left menu. 
5. In the bottom of the screen you can input a script command to execute, e.g.: `test_script(100)`.
6. Enjoy experimenting with arguments 👩‍🔬 

### Custom addresses
 
1. Click on the 🔗 icon in the left menu.
2. Add a new address by entering the name and address in the fields inside the popup.
3. Use the named address in your code.

## Roadmap

Playground is currently in alpha stage. The current Roadmap is:

    * Tests in browser.
    * Allow watching a stored resource.
    * Add support for different Move VM networks.
    * Move Prover in browser.
    * Ability to save/share projects and code samples.   

## Tech stack

The Move Playground is possible due to:

    * [Move VM WASM](https://github.com/pontem-network/sp-move-vm) fork developed by our team.
    * [Dove Light](https://github.com/pontem-network/dove) - Dove package manager built for Web.
    * [Monaco Editor](https://github.com/microsoft/monaco-editor) for frontend.
