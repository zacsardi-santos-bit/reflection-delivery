## Description

When writing logic that calls into external operations — such as reading files, fetching data from a network, or querying a database — developers often need two separate implementations: one for synchronous use and one for asynchronous use. This duplication is error-prone and hard to maintain.

We need a utility that lets developers write the core logic once using a generator-based approach and then run that same logic in either a synchronous or asynchronous context simply by providing an appropriate callback. The generator yields at points where an external operation is needed, and the utility handles resolving those values and driving the generator to completion.

## Expected Behavior

- A synchronous driver function should accept a generator function plus a synchronous callback and any additional arguments, drive the generator by feeding back each callback result, and return the generator's final value.
- An asynchronous driver function should accept a generator function plus a callback (synchronous or async) and any additional arguments, drive the generator by awaiting each callback result, and resolve to the generator's final value.
- If the callback throws or rejects, the error must be propagated back into the generator so the generator logic can catch and handle it.
- Both drivers must support recursive generator delegation, enabling patterns like walking a dependency tree.

## Why This Matters

This utility eliminates the need to write two separate implementations of the same logic for sync and async contexts. It is especially valuable for traversal or pipeline logic where the shape of the computation is fixed but the I/O mechanism may vary — e.g., reading files synchronously in tests vs. asynchronously in production.
