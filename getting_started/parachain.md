# Parachain

The Pontem network is Polkadot based, so it's built on [Substrate framework](https://github.com/paritytech/substrate) and has connection to Relay Chain via Parachain.

## What is Substrate?

Substrate is a library of pre-built modules (called pallets) which allows anyone to **build their own custom blockchain**. Substrate's goal is to abstract away code-complexity by making available modular components which developers can use to build their custom blockchain.

## Why Substrate?

When the Polkadot team began building at Parity, they realized that **they were repeating a lot of development that had already been done** for the Ethereum and Bitcoin clients, such as writing RPC and database components. Even the most specialized blockchains have many attributes in common: a hashing algorithm, a database, networking, etc.

In order to **efficiently build many different blockchains**, Parity developers put all the functionalities needed to build a blockchain into a framework called Substrate. 

The idea was to take everything learned building Ethereum and Bitcoin implementations and make creating a blockchain as easy and flexible as possible. Substrate was created in a modular way to give technical freedom but also make functionalities like accounts, balances, governance, and smart contracts as easy as plugging in a library.

Substrate’s multilayered architecture lets developers **choose between ease of development and technical freedom.** 

## What are the key components of the Pontem Substrate Architecture?

The Pontem network architecture combines several components to become a unique toolset to create, crowdsale and manage your blockchain projects, include DeFi ones.

To utilize the Pontem network the following components must first be implemented or already implemented:

* [Move VM runtime & pallet](../move_vm/README.md)
* [Compiler & Toolset](../move_vm/compiler_&_toolset.md)
* Parachain
* Collators
* Economic Model
* Governance

## Parachain

The current Pontem node implementation is a [Parachain](https://wiki.polkadot.network/docs/learn-parachains) that requires connection to the Relay Chain to function normally. It is required as we want to have the Move VM pallet working, and No-Code tools working on top of Move language secured by Polkadot/Kusama validators and at the same time allowing our community to become Collators: earn rewards and produce new proof states for Relay Chain.

## Collators
 
[Collators](https://wiki.polkadot.network/docs/learn-collator) collecting parachain transactions and producing state proofs for Relay Chain validators.
Collators rewarded in PONT tokens for running the Pontem network parachain.

## Governance

We are currently in progress with implementation integration of the official [Governance Pallet](https://wiki.polkadot.network/docs/en/learn-governance).
