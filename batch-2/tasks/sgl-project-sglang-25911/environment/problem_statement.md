## Description

The debug comparator utilities use dimension names on tensors to track which axis corresponds to tokens, batch, hidden state, etc. The current implementation is built on top of an experimental framework feature for named tensors that has well-known limitations: many ordinary tensor operations fail when applied to a named tensor, requiring the names to be explicitly stripped before the operation and then reattached afterward. This creates scattered boilerplate throughout the codebase and makes the code fragile.

## Expected Behavior

- The mechanism for attaching dimension names to a tensor should not interfere with any standard tensor operations (reshaping, rearranging, arithmetic, etc.). Named tensors should work transparently in all contexts without requiring manual name removal first.
- A dedicated function for **reading** the dimension names from a tensor should be added to the public API. It should return a tuple of name strings, with absent entries for dimensions that have no name attached.
- The function for **removing** names from a tensor should be renamed to better reflect its semantics (it returns a new view without names, rather than mutating the original). The old name should be removed from the public API.
- Both new public API functions should be importable from the same module-level public namespace as the rest of the comparator utilities.

## Why This Matters

The current approach forces every caller that wants to do arithmetic or reshape a named tensor to first strip its names, perform the operation, and optionally reattach them — adding noise and risk of forgetting a step. Replacing the mechanism with one that doesn't impose these restrictions would eliminate all that boilerplate and make the dimension-naming system genuinely ergonomic.
