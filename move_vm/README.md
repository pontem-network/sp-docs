# Move VM

Pontem uses the Move Virtual Machine (VM) as an operating system to implement smart contract functionality. This allow users to develop, publish and execute smart contracts using the Move language compatible with Facebook's Diem Blockchain. 

![Move VM Introduction](/assets/illustrations/move_vm.png "Move VM Introduction")

**Pontem maintains compatibility with Diem, so developers can use tooling and smart contracts on both networks.**

 ![Move <-> Diem compatibility](/assets/illustrations/move_compatibility.png "Move <-> Diem compatibility")

The [Move language](../lang/README.md) and the Move Virtual Machine, both developed by the [Facebook-backed Diem Association](https://diem.com/), is safe, scalable and flexible with built-in security features such as resource-oriented architecture and formal verification.

Unique features implemented in Move VM and language include:

* **Access Control** - In Move, any custom asset such as a token can be declared as a resource type, making it safe and access-controlled by default. This feature allows for maintaining ownership information and privileges of digital assets within smart contracts. If an asset is sent to a smart contract, ownership is not changed. For example, if a hacker gets access to a Move smart contract, he would not be able to withdraw the assets to his own wallet unless that functionality is a feature of the smart contract.
* **Atomic resource architecture** - Prevents developers from making basic mistakes that are common in smart contract development such as reentrance errors or double-spending errors. In Move, resources can never be copied or implicitly discarded, only moved between storage locations.
* **Modules** - Are akin to smart contracts but more similar to banks using object oriented programming. Each resource (object) is stored in an individual vault, controlled by the owner’s account. Resource operations are limited by the functions supported by the specific module(class), which may be called from outside the module. Developers can deploy new modules to the network.
* **Scripts** - Each transaction on the network may contain a script that can call several modules or initiate several actions. Developers can use one transaction to engage various actions which significantly reduces the number of smart contracts required for an application. This results in safer applications, better user experience, and significantly more flexibility.
* **Bytecode verifier** - The verifier is an integral part of Move which checks new modules and scripts for security purposes before these are published. Once verified, the Bytecode interpreter module executes the code. This feature reduces the number of runtime errors.
* **Formal verification** - Modules can be verified using formal verification automatically before being deployed.
* **Gas system** - Similar to the Solidity gas usage system, users can set gas prices for their transactions to be competitively processed by validators.
