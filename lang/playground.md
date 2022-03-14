# Move Playground

Move Playground is web version of Move VM, Dove and basic IDE, which allows developer to write, test, build their Smart Contracts
from the browser.

## Quick Guide

### Create module

1. Go to [Move Playground](https://playground.pontem.network/) and create new project (by click on **"+" near "Projects**").
2. Put name of project into appeared input.
3. Click on created project.
4. Add new module to project by clicking on **"+"** near `sources` folder.
5. Put name of the module in appeared input, e.g. `test_module.move` (file extension is must).
6. Put there your Move code, e.g.:

```rust
module 0x01::TestModule {
}
```

Click on first button in the top menu (hammer and wrench) to build new module, close console.

### Run script

1. Create new script similar to module (but in script folder), e.g. `test_script.move`.
2. Put there following code:

```rust
script {
   fun test_script(val: u128) {
     assert!(val == 15, 101);
   }
}
```

3. Build it.
4. Navigate to `Run` section in left menu. 
5. In the bottom of the screen you can put script command to execute, e.g.: `test_script(100)`.
6. Enjoy playing with arguments :) 

### Custom addresses
 
1. Click on the "chain" icon in the left menu.
2. Add new address by entering name and address in field in apperead popup.
3. Done. Use the named address in your code.

## Roadmap

Playground is currently in alpha stage, current Roadmap is:

    * Dependencies import from Github.
    * Pre-filled example project.
    * Tests in browser.
    * Allow to watch stored resource.
    * Support different Move VM networks.
    * Move Proover in browser.
    * Ability to save/share projects, code samples.   

## Tech stack

The Move Playground become possible because of:

    * [Move VM WASM](https://github.com/pontem-network/sp-move-vm) fork developed by our team.
    * [Dove Light](https://github.com/pontem-network/dove) - Dove package manager built for Web.
    * [Monaco Editor](https://github.com/microsoft/monaco-editor) for frontend.


