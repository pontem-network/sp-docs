# Events

Each transaction processed by **Pontem** network can have events.

You can see events in [UI](../getting_started/cli.md) when transaction processed or in block explorer: choose `Network` in the top header menu, and then click on `Explorer` in dropdown, find block contains events.

Also you can see events using [CLI](../getting_started/cli.md), when your transaction is processed.

To emit new event use [Event](../move_vm/stdlib.md#events) module. Let's write new script firing event:

```rustc
script {
    use 0x01::Event;

    fun emit(account: &signer, value: u128) {
        Event::emit(account, value);
    }
}
```

Also you can emit events with complex types, even with [resources](../lang/learn_resources.md) or structs:

```rustc
module MyEmitter {
    use 0x01::Event;

    struct Foo { x: u64, y: bool }

    public fun emit_event(account: &signer, x: u64, y: bool) {
        let foo = Foo { x , y };

        Event::emit(account, foo);
    }
}
```

Write script for new module:

```rustc
script {
    use {{sender}}::MyEmitter;

    fun emit(account: &signer, x: u64, y: bool) {
        MyEmitter::emit_event(account, x, y);
    }
}
```

Build module and script:

```sh
dove build
dove tx 'emit(1000, true)'
```

After deploy module and execute new script (replace `<seed>` with your seed):

```sh
pontem-cli tx.mvm.publishModule @./artifacts/modules/0_MyEmitter.mv 100000 --seed <seed>
pontem-cli tx.mvm.execute @./artifacts/transactions/emit.mvt 1000000 --seed <seed>      
```

Example event output:

```json
{
    "method": "Event",
    "section": "mvm",
    "index": "0x0800",
    "data": [
        "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty",
        "0x078eaf04151687736326c9fea17e25fc5287613693c912909cb226aa4794f26a48244d79456d69747465720c466f6f00",
        "0xe80300000000000001",
        {
            "address": "5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty",
            "name": "MyEmitter"
        }
    ]
}
```

See `data` array and parameters by indexes:

* `5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty` - account emitted event.
* `0x078eaf04151687736326c9fea17e25fc5287613693c912909cb226aa4794f26a48244d79456d69747465720c466f6f00` - is `TypeTag` value, describes `Foo` structure.
* `0xe80300000000000001` - event data, it's indeed our `u64` 1000 and boolean `true` value encoded in LCS.

The **data** placed in the events encoded in \(Libra Canonical Serialization\). There is a community [description](https://github.com/librastartup/libra-canonical-serialization/blob/master/DOCUMENTATION.md) of how it works. 

You can use several libraries to parse events results:

* [LCS Rust](https://docs.rs/libra-canonical-serialization/0.1.0/libra_canonical_serialization/index.html)
* [LCS JS](https://github.com/dfinance/lcs-js)
* [LCS Go](https://github.com/the729/lcs)

