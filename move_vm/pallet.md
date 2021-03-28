# Move Pallet

Technology-wise Pontem aims to bring the Move VM and Move language to Polkadot and its ecosystem while maintaining compatibility with Diem. If economically feasible, the Pontem network will implement its own [Parathread](https://wiki.polkadot.network/docs/en/learn-parathreads) and include a Move VM pallet (A module that enables Move VM to operate on Substrate).

[Move VM pallet](https://github.com/pontem-network/sp-move) is a version of Move VM adopted for [Substrate Runtime WebAssemly](https://substrate.dev/docs/en/knowledgebase/runtime/#:~:text=In%20Substrate%2Dbased%20chains%2C%20the,make%20changes%20to%20this%20state.), which allows us to initialize and run Move VM inside the runtime, execute VM related transactions and has full compatibility. Any Substrate based project can use our pallet inside their blockchain, by including the pallet in their code.

![W3F Grant](/assets/w3f_grant.png)

Pontem received a Web3 Foundation grant for the development of the pallet by [submitting proposal](https://github.com/w3f/Open-Grants-Program/blob/master/applications/pontem.md) to the Web3 Foundation [Open Grant Proposal](https://github.com/w3f/Open-Grants-Program#open-grants-program-) program.


