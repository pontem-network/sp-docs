# Move Pallet

Pontem aims to bring the Move VM and Move language to Polkadot and other layer 1s while maintaining compatibility with Diem. If economically feasible, the Pontem network will implement its own [Parachain](https://wiki.polkadot.network/docs/learn-parachains) with a Move VM [pallet] (https://substrate.dev/docs/en/knowledgebase/runtime/pallets) which is a Rust module of the Substrate framework that enables the Move VM to work on Substrate based blockchains.

Pontem built the [Move VM pallet for Substrate](https://github.com/pontem-network/pontem). This version of the Move VM was adopted for the [Substrate WebAssembly (WASM) Runtime](https://substrate.dev/docs/en/knowledgebase/runtime/#:~:text=In%20Substrate%2Dbased%20chains%2C%20the,make%20changes%20to%20this%20state.). This allows us to initialize and run the Move VM inside the runtime, execute VM related transactions and has full compatibility with any Substrate based project. Projects can use our pallet inside their blockchain by including the pallet in their code.

![W3F Grant](/assets/w3f_grant.png "Project Supported By Web3 Foundation Grants Program")

Pontem successfully delivered all milestones for a Web3 Foundation grant to develop the pallet in this [proposal](https://github.com/w3f/Open-Grants-Program/blob/master/applications/pontem.md) as part of the Web3 Foundation [Open Grant Proposal](https://github.com/w3f/Open-Grants-Program#open-grants-program-) program.
