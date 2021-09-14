# Addresses

The address format used in Substrate-based chains is SS58. SS58 is a modification of Base-58-check from Bitcoin with some minor modifications. Notably, the format contains an address type prefix that identifies an address as belonging to a specific network.

For example:

* Polkadot addresses always start with the number 1.
* Kusama addresses always start with a capital letter like C, D, E, F, etc...
* Generic Substrate addresses start with 5.

{% hint style="info" %}
🧙‍♂️ Learn more about [SS58 addresses](https://wiki.polkadot.network/docs/en/learn-accounts) in Polkadot documentation.
{% endhint %}

The Pontem network uses the `5` prefix now. To be in compatibility with Diem we also support the [Diem address format](https://developers.diem.com/main/docs/move-primitives-address), it's 16-byte hex. For example, the [Standard Library](../move_vm/stdlib.md) deployed using address `0x01`. Pontem network reaches compatibility by converting SS58 addresses to Diem ones in background, by adding empty bytes to the address if necessary.

SS58 and Diem address format supported on our [Move VM](../move_vm/pallet.md) pallet, [tools](../move_vm/compiler_&_toolset.md) and [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=PontemNetwork.move-language).
