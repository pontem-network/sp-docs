# Events

Each transaction processed by **Pontem** network can have events.

You can see events in [UI](../getting_started/cli.md) when transaction processed or in block explorer: choose `Network` in the top header menu, and then click on `Explorer` in dropdown, find block contains events.

Also you can see events using [CLI](../getting_started/cli.md), when your transaction is processed.

To emit new event use [Event](../move_vm/stdlib.md#events) module.

To emit events you have to create an event handle object that will be used one time and destroyed or can be used multiplay time and stored under account resource for example.
 
Let's write new script firing event with temporary event handle:

```rust
script {
    use 0x1::Signer;
    use 0x1::Event;

    fun emit_event(account: signer) {
        let addr = Signer::address_of(&account);

        // Create new event handle.
        let event_handle = Event::new_event_handle<address>(&account);

        // Emit event.
        Event::emit_event(
           &mut event_handle,
           addr
        );

        // Destroy event handle.
        Event::destroy_handle(event_handle);
    }
}
```

Provided example using temporary event handle, that just created inside script to fire event and destroy later.

Created event handles can fire only events containing account's addresses as data, because during creation of event handle you provide a type that will be used for created event handle, but you can use any other types, even complex one (e.g. structs).

Usually, you will create a event handle for each type of your events and store it inside your account resource, e.g., like in provided example:

```rust
address {{sender}} {
    module MyEventEmitter {
        use 0x1::Signer;
        use 0x1::Event;

        struct MyStruct<T: store + drop> has key, store {
            event_handle: Event::EventHandle<T>,
        }

        public fun initialize<T: store + drop>(account: &signer) {
            let event_handle = Event::new_event_handle<T>(account);
            
            move_to(account, MyStruct<T> {
                event_handle,
            });
        }

        public fun emit<T: store + drop>(account: &signer, val: T) acquires MyStruct {
            let my_struct = borrow_global_mut<MyStruct<T>>(Signer::address_of(account));
            Event::emit_event(
                &mut my_struct.event_handle,
                val,
            );
        }
    }
}
```

Write script for new module:

```rust
script {
    use {{sender}}::MyEventEmitter;

    fun emit_via_emitter(account: signer, x: u64) {
        // You call initialize ONLY ONE TIME, in the future smart contract you don't need to call it again for current signer.
        MyEventEmitter::initialize<u64>(&account);

        // Emit event with existing event handle.
        MyEventEmitter::emit<u64>(&account, x);
    }
}
```

Build module and script:

```sh
dove build
dove tx 'emit_via_emitter(1000)'
```

After deploy module and execute new script (replace `<seed>` with your seed):

```sh
polkadot-js-api tx.mvm.publishModule @./artifacts/modules/0_MyEmitter.mv 100000 --seed <seed> --types ./types.json --ws <ws-endpoint>
polkadot-js-api tx.mvm.execute @./artifacts/transactions/emit.mvt 1000000 --seed <seed> --types ./types.json --ws <ws-endpoint>
```

Example event output:

```json
{
    "method": "Event",
    "section": "mvm",
    "index": "0x3600",
    "data": [
        "0x040000000000000014ca430662463ce9a1a27dd6bb463595f3e3526d118e25541421b954893eca7d",
        "U64",
        "0xe803000000000000"
    ]
},
```

See `data` array and parameters by indexes:

* `0x040000000000000014ca430662463ce9a1a27dd6bb463595f3e3526d118e25541421b954893eca7d` - account emitted event in hex and counter of created event handle.
* `U64` - is `TypeTag` value, describing the `U64` number.
* `0xe803000000000000` - event data, it's indeed our `u64` 1000 value encoded in BCS.
* 
The **data** placed in the events encoded in \(Binary Canonical Serialization\). There is a Diem [description](https://github.com/diem/bcs) of how it works. 

You can use several libraries to parse events results:

* [LCS Rust](https://docs.rs/libra-canonical-serialization/0.1.0/libra_canonical_serialization/index.html)
* [LCS JS](https://github.com/dfinance/lcs-js)
* [LCS Go](https://github.com/the729/lcs)

