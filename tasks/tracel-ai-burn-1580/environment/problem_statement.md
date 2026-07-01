## Description

The current compute server API requires callers to pass memory handles as references when reading data or dispatching kernel executions. This means that every read and execute call must borrow handles from the caller, tightly coupling the lifetime of those handles to each operation call. This design is awkward when you want to transfer ownership of a memory resource between components, and it makes it harder to reason about when a handle is truly "done" versus still in use.

We should introduce a "binding" concept — a lightweight, owned representation of a memory resource that a caller produces from a handle at the moment of use. Instead of passing references to handles, callers would convert a handle into a binding and pass that binding by value into read and execute operations.

## Expected Behavior

- A new binding type should be available in the server module
- Memory handles should be convertible into bindings via a dedicated method
- The server's read operation should accept a binding by value
- The server's execute operation should accept a collection of bindings by value
- The client-facing read and execute methods should mirror this binding-based interface
- The binding type should be cloneable and should expose the underlying memory resource
- Memory management operations should accept the binding type by value

## Why This Matters

This change makes resource consumption intent explicit: when a caller converts a handle into a binding, it signals that the resource is being "handed off" for a specific operation. This enables better memory lifecycle tracking and makes it easier to build higher-level resource management on top of the compute layer.
