# Introduction

![Move Langugage](/assets/move_lang_new.png "Move Language")

Many crypto developers are interested in using the Move language. The reasons for this are its high security and the emerging use cases that will grow as soon as crypto and non-crypto users are merged.

{% hint style="info" %}
🧙‍♂️ The Move Language is the most secure smart contracts language and will probably become a standard after Solidity. 
{% endhint %}

It combines security by design (__formal verification from box, atomic resource model__) with ease of use, being Turing complete, safe and flexible. It is developed and used by ex-Diem team transformed to Aptos.

Although it has built-in security by design such as resource-oriented architecture and formal verification, Move VM still severely lacks toolsets and documentation. Therefore more research and development is still needed in this area and Pontem will help seed this ecosystem.

Unique features implemented in Move Language:

* **Access Control** - In Move, any custom asset such as a token can be declared as a resource type, making it safe and access-controlled by default. This feature allows for maintaining ownership information and privileges of digital assets within smart contracts. If an asset is sent to a smart contract, ownership is not changed. For example, if a hacker gets access to a Move smart contract, he would not be able to withdraw the assets to his own wallet unless that functionality is a feature of the smart contract.
* **Atomic resource architecture** - Prevents developers from making basic mistakes that are common in smart contract development such as reentrance errors or double-spending errors. In Move, resources can never be copied or implicitly discarded, only moved between storage locations.
* **Modules** - Are akin to smart contracts but more similar to banks using object oriented programming. Each resource (object) is stored in an individual vault, controlled by the owner’s account. Resource operations are limited by the functions supported by the specific module(class), which may be called from outside the module. Developers can deploy new modules to the network.
* **Scripts** - Each transaction on the network may contain a script that can call several modules or initiate several actions. Developers can use one transaction to engage various actions which significantly reduces the number of smart contracts required for an application. This results in safer applications, better user experience, and significantly more flexibility.
* **Bytecode verifier** - The verifier is an integral part of Move which checks new modules and scripts for security purposes before these are published. Once verified, the Bytecode interpreter module executes the code. This feature reduces the number of runtime errors.
* **Formal verification** - Modules can be verified using formal verification automatically before being deployed.
* **Gas system** - Similar to the Solidity gas usage system, users can set gas prices for their transactions to be competitively processed by validators.

Because the Move language is very young, there is not much information about it, and this is why we are ready to present available documentation so you can learn Move language through examples and using existing books/tutorials.

List of recommended resources:

* [Move Documentation](https://developers.diem.com/docs/welcome-to-diem) - official Move language documentation.
* [Aptos Documentation](https://aptos.dev/)
