## dove test

Run tests in the package. 
Move test is a function which resides in `tests/` directory and marked with `#[test]` attribute.

```move
// #[test_only] is optional, 
// prevents pollution of the global namespace with test-only modules
#[test_only]
module Sender::MyTests {
    #[test]
    fun test_add() {
        assert!(1 + 1 == 2, 1);
    }
}
```

Alias to the longer `dove package build` command, which calls underlying move-cli implementation. 

Reference: 

TODO
