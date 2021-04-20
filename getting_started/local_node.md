# Local Node

This part explains how to build Move VM pallet locally, and run the node in development mode locally.

We recommend to use [rust toolchain](https://github.com/pontem-network/sp-move/blob/master/rust-toolchain) that we use during development.

Install [Rust](https://www.rust-lang.org/tools/install) before moving to next steps.

Visit repository of [Move Pallet](https://github.com/pontem-network/sp-move) and clone it:

```sh
git clone git@github.com:pontem-network/sp-move.git
```

Navigate to cloned repository and launch init script:

```sh
cd ./sp-move
./scripts/init.sh
```

Run local node:

```sh
make run
```

You will see logs once Substrate node built. To iterate with local node using UI see the next page.
