# Intellij Move IDE Tutorial

![IDE Screen 1](/assets/ide_1.png)

Move Language is a plugin for the Intellij IDE platform that enables developers to write smart contracts using the Move language in editors/IDEs based on the Intellij platform therefore also compatible with other IDEs like Pycharm and Clion for projects that require multi-language development within one IDE.

**Key Features:**

- Syntax highlighting
- Code formatting
- Go-to-definition
- Rename refactoring
- Type inference
- `Move.toml` and `aptos` binary integration

## 1. Install Aptos CLI

The [Aptos CLI](https://github.com/aptos-labs/aptos-core/blob/main/crates/aptos/README.md#install-the-aptos-cli) is required for the Move Language plugin to work correctly. 

## 2. Install Intellij IDE and Move plugin

To work with Move you need an IDE based on the Intellij platform, it can be PyCharm, CLion or any other Jetbrains IDE. Note that the current version of the plugin tested well only on PyCharm and CLion, so we recommend using these mentioned platforms until future updates. 

The community edition of the IDEs work well with the plugins.

Use the following links to download IDEs:

- [PyCharm](https://www.jetbrains.com/pycharm/)
- [CLion](https://www.jetbrains.com/clion/)

After you installed one of the recommended IDE’s, open the following link to install the plugin:

- [Move Language Plugin](https://plugins.jetbrains.com/plugin/14721-move-language)

Open your IDE and click on “Install” button to download it, otherwise you can [install plugin directly](https://www.jetbrains.com/help/idea/managing-plugins.html) from IDE.

## 3. Create and build your first Move Project

* In Main Menu choose “File” → “New Project”.
* Choose “Move” in the left Menu.
* Choose a name for your project, for example “MyFirstMoveProject”.
* Put path to Aptos CLI if needed. To get path of Aptos CLI in your local system just open Console/Terminal and run `which aptos`.
* Click on “Create”.

![IDE Screen 2](/assets/ide_2.png)

* In the new project, first open the `Move.toml` file.
* Open terminal in IDE and run the following command: `aptos init`.
* Just skip all steps Aptos is asking you (this means press “Enter” each time to use the default configuration).
* Copy the address of the new generated account during `aptos init` to `Move.toml`, under the addresses section (see screenshot below for example): `Sender = “0xFFA7F40F44B93B24202E010E9E2BE0CB443FD964B27519EA68BE04BF3A00C3A1”`

![IDE Screen 3](/assets/ide_3.png)

* Now create a new Move module file, let’s call it `Math`:
    * Right Click on the “sources” folder.
    * “New” → “Move File”.
    * Choose the name (”Math”) and the type of file (”Module”).
    
![IDE Screen 4](/assets/ide_4.png)
    

* Put the following code into your module:

```jsx
module Sender::Math {
    public fun add(a: u128, b: u128): u128 {
        a + b
    }
}
```

* Build your project by clicking on the “Run” button (it depends on each IDE, but it’s generally always on the top right or left corner).
    
![IDE Screen 5](/assets/ide_5.png)
    
* See the newly built artifacts in the `build` folder and in the output of the IDE console.
    
![IDE Screen 6](/assets/ide_6.png)
    

* If there are errors in your code, you can see it in the “Problems” console:

![IDE Screen 7](/assets/ide_7.png)

**Configure Aptos CLI manually**

In the case that you opened an existing project Aptos project, you will still have to configure the Aptos CLI. Just search for Move Language in the “Preferences” settings.

If you want to spare the search bot, follow these steps:

* Open “Preferences” menu in the IDE Main Menu.
* Navigate to “Languages & Frameworks”.
* Choose “Move Language”.
* Put path to Aptos CLI in your local system. 
* Click “Apply” and save configuration.

## 4. Deploy modules

* Open file containing module, e.g. `Math.move`.
* Right click on the name of the module on the top bar to open a context menu as in the screen below:
    
![IDE Screen 8](/assets/ide_8.png)
    
* Click on Run ‘Publish Math’

![IDE Screen 9](/assets/ide_9.png)

* Module should now be deployed. You can validate this in your console.
* Another option: you can use  the gutter button (the arrow icon) instead of opening the context menu.
    
![IDE Screen 10](/assets/ide_10.png)    

## 5. Write tests

* Create the “tests” directory in the root of the project.
* Create a new test file: In top bar click “New” → “Move”, choose a name (”MathTest”) and choose type “Test”.
* Put the following code in the new test file:

```jsx
#[test_only]
module Sender::MathTest {
    use Sender::Math;

    #[test]
    fun test_add() {
        let a = 20;
        let b = 30;

        let r = Math::add(a, b);
        assert!(r == (a+b), 1);
    }
}
```

* Run a test on the whole module or test individual lines in the gutter with the green Run buttons:

![IDE Screen 11](/assets/ide_11.png)

* Or just right click on the “tests” folder and run all tests.

![IDE Screen 12](/assets/ide_12.png)

# Troubleshooting

## No build configuration available

If you see there is no build configuration e.g. the ”Run” button is disabled, like this:

![IDE Screen 13](/assets/ide_13.png)

Fix it easily by adding a build configuration to your project:

* Click on “Add Configuration”.
* Click on the “Plus” button.
    
![IDE Screen 14](/assets/ide_14.png)
    
* Choose “Move”.
    
![IDE Screen 15](/assets/ide_15.png)
    
* Fill the fields:
  * Name: “Build”.
  * Command: “move compile”.
  * Working directory - your current directory.
  
![IDE Screen 16](/assets/ide_16.png)

* Click “Apply” and close the window.
* “Run” build.
    
![IDE Screen 17](/assets/ide_17.png)
    
## Executable is not specified
    
If you get an error like this:
    
![IDE Screen 18](/assets/ide_18.png)
   
* Open “Preferences” menu.
* Navigate to “Languages & Frameworks”.
* Choose “Move Language”:
  * Put path to Aptos CLI in your local system.
  * Click on “Apply” and close window.
    
![IDE Screen 19](/assets/ide_19.png)
