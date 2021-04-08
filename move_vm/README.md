# Move VM

![Move VM Introduction](/assets/move_vm.png "Move VM Introduction")


Pontem uses Move VM to implement smart contracts functional: allow users to develop, publish and execute their smart contracts in Move language. 
Pontem keeps full compatibility with Diem, so developers can use tools and their smart contracts on both networks.

 ![Move <-> Diem compatibility](/assets/move_compatibility.png "Move <-> Diem compatibility")

The [Move language](./../lang/README.md) and the Move Virtual Machine, both developed by [Facebook Diem](https://diem.com/), are among the safest technologies out there that enable the creation of smart contracts. While having built-in security by design such as resource-oriented architecture and formal verification.

Unique features implemented in Move VM and language include:

* **Access Control** - In Move, any custom asset can be declared as a resource type, making it safe and access-controlled by default. This feature allows for example to maintain ownership information and privileges of digital assets even within smart contracts. If an asset is sent to a smart contract, ownership is not changed. For example, if a hacker gets access to a Move smart contract, he would not be able to withdraw the assets to his own wallet unless that functionality is a feature of the smart contract.
* **Atomic resources architecture** - Prevents developers from making basic mistakes that are common in smart contracts development such as reentrance errors or double-spending errors. In Move, resources can never be copied or implicitly discarded, only moved between storage locations.
* **Modules** - Are similar to banks. Each resource is stored in an individual vault, controlled by the owner’s account. Resource operations are limited by the functions supported by the specific module, which may be called from outside the module. Developers can deploy new modules to the network.
* **Scripts** - Each transaction on the network may contain a script that can call several modules or initiate several actions. Developers can use one transaction to engage a number of actions which significantly reduces the number of smart contracts required for an application. This results in safer applications, better user experience, and significantly more flexibility.
* **Bytecode verifier** - An integral part of Move which checks new modules and scripts for security properties before these are published. Once verified, the Bytecode interpreter module executes the code. This feature reduces the number of runtime errors.
* **Formal verification** - Modules can be verified using formal verification automatically before being deployed.
* **Gas system** - Similar to the Solidity gas usage system, users set gas and gas prices for their transactions.
