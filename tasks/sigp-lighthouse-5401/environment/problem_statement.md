# Extract Gossipsub into a Standalone Package

## Description

The gossipsub protocol implementation is currently embedded as an internal module inside the main networking library. This makes it hard to work with independently — to build or test gossipsub in isolation you have to compile the entire networking crate, and downstream consumers must reference gossipsub types through deeply nested module paths.

We should extract the gossipsub code into its own dedicated package so it can be built, tested, and maintained independently from the broader networking infrastructure.

## Expected Behavior

- The gossipsub code lives in its own package, separate from the networking library
- All existing gossipsub functionality and tests continue to work after the extraction
- Internal imports within the gossipsub code no longer need to reference the enclosing library's module hierarchy
- Other components that need gossipsub (such as the network service) can declare a direct dependency on it rather than going through the networking library
- An unnecessary dependency on an async runtime library for network address types is replaced with the equivalent type from the standard library

## Why This Matters

Separating gossipsub into its own package improves modularity, makes the codebase easier to navigate, and allows gossipsub tests to be run in isolation. It also reduces the dependency footprint by removing an async runtime dependency that was only needed for a single type already available in the standard library.
