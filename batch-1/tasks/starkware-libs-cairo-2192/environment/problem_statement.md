## Description

Array element access by index currently requires passing ownership of the array into the operation and receiving it back. This means every index lookup forces an ownership handoff even when the array is never modified. The result is unnecessarily verbose code, more expensive execution, and an API that doesn't reflect the read-only nature of indexing.

The array element access operation should be redesigned to accept an immutable snapshot of the array rather than consuming ownership. Since the array is not modified, there is no reason to return it — callers retain their original reference. The returned element should similarly be a snapshot (read-only reference), rather than a copy of the value.

## Expected Behavior

- Array element access should take an immutable view of the array, not require transferring ownership
- The array does not need to be returned after the access since it was never consumed
- The returned element is a read-only reference (snapshot) to the value in the array, not a copy
- Both the safe element access (returns an optional element) and the panicking element access (panics on out-of-bounds) should follow this convention
- Code that accesses the array after a lookup should still work — the array remains available

## Why This Matters

This makes array indexing simpler to use, cheaper to execute, and more consistent with how developers expect read-only operations to behave. It also reduces the size of generated low-level code by eliminating unnecessary data flow for array ownership.
