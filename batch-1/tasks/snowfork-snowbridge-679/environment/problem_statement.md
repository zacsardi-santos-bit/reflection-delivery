## Description

A helper function in the parachain relayer that computes sibling hash directions for Merkle tree proofs currently requires callers to pass a full proof object, even though it only ever uses two fields from that object: the leaf's position within the tree and the total number of leaves. This tight coupling makes the function harder to test in isolation and prevents it from being called in contexts where the rest of the proof data isn't available.

## Expected Behavior

- The function should accept two simple integer parameters — the zero-indexed position of the leaf and the total count of leaves in the tree — rather than a composite proof object.
- For a tree with only one leaf, the function should return an empty result (no sides needed).
- For multi-leaf trees, the function should correctly compute the sibling-hash side directions at each level of the proof path.
- If the provided leaf position is equal to or greater than the total leaf count, the function should return a descriptive error rather than computing an incorrect result.

## Why This Matters

Having the function depend on a full proof object makes unit testing awkward since a minimal test must construct an entire proof struct just to exercise the logic. Decoupling the function to accept plain integer inputs makes it independently testable, and proper bounds checking prevents silent errors when invalid leaf positions are passed.
