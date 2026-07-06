# Add Host-Side API for WebAssembly GC Struct Types

## Description

The WebAssembly GC proposal introduces struct types, but the Wasmtime runtime currently has no way for host (Rust) code to interact with them. There is no host API to define struct types, allocate struct instances, read or write individual fields, iterate over all fields, or exchange struct references with guest WebAssembly modules via functions, globals, or tables.

Host code working with WebAssembly GC structs is completely blocked: it cannot create struct values, inspect them, or use them in any host-to-guest or guest-to-host data flow.

## Expected Behavior

- Host code should be able to define struct types with zero or more typed, optionally-mutable fields.
- Host code should be able to allocate struct instances with initial field values.
- Reading a field by index should return the stored value; out-of-bounds access and access on unrooted/expired references should fail with an error.
- Writing a mutable field by index should update the value; writing should fail on out-of-bounds indices, immutable fields, unrooted references, wrong value types, and values belonging to a different store.
- It should be possible to retrieve the struct type from a live struct instance.
- It should be possible to iterate over all fields of a struct and get a count.
- Any reference that belongs to a different store should cause a panic with a message indicating a store mismatch; unrooted references passed at construction time should cause a recoverable error.
- Struct references should round-trip through WebAssembly function calls (typed and untyped), globals, and tables, preserving identity.
- It should be possible to check whether a general-purpose heap reference is a struct, and to unwrap it into a struct reference if so.
- WebAssembly modules should be instantiatable with host-provided globals holding struct-typed references, including both null and non-null initial values.

## Why This Matters

Without this API, the WebAssembly GC proposal's struct feature is essentially unusable from Rust host code. Embedders cannot build applications that exchange structured data with WebAssembly modules using the GC type system.
