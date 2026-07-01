## Description

Verus currently has no support for formally verifying properties of standard hash maps. Developers writing verified Rust code cannot reason about whether a map contains a certain key after insertion, whether a lookup returns the expected value, or whether removal properly updates the map's abstract state. This makes it impossible to formally verify any code that uses standard hash maps.

## Expected Behavior

- The verification library should provide specifications for standard hash maps that allow developers to prove properties like key membership, value lookup, insertion, removal, and clearing.
- A standard hash function should have a verifiable abstract state that tracks the sequence of data written to it, and two instances given the same sequence of writes should produce the same result.
- For primitive key types and boxed primitives, the specifications should work automatically. For user-defined key types, users should be able to explicitly state the assumption that their type's hash and equality behavior is consistent enough for the model to apply.
- When that assumption is missing for a custom key type, verification of map content assertions should fail — the verifier should correctly reject such code.
- There should be a wrapper map type for keys that carry a separate logical view, so that proofs can reason about the map at the level of the logical view rather than the concrete key.
- There should be a specialized map type for string keys that uses the character sequence of the string as the abstract key in proofs.

## Why This Matters

Many real-world Rust programs use hash maps heavily. Without verification support for them, Verus cannot be used to verify large classes of programs. This feature unlocks the ability to formally prove correctness of code that reads from and writes to hash maps, bringing Verus closer to being a practical verification tool for ordinary Rust code.
