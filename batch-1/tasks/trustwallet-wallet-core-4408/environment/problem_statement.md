## Description

Several FFI utility functions in the Rust codebase have incorrect memory ownership semantics. Functions that allocate heap memory and return it to the caller are declaring their return values as read-only pointers, which prevents callers from properly taking ownership and freeing the memory. Additionally, the bit-reading utility provides a way to create instances but lacks a corresponding destroy function, meaning every use leaks memory.

These issues prevent safe usage from foreign-language callers and cause the test suite to fail to compile when tests attempt to use correct memory ownership patterns.

## Expected Behavior

- Functions that allocate and return heap strings (UUID generation, hex encoding, base64 encoding) should return mutable (owned) pointers, signaling that the caller is responsible for freeing the memory.
- The bit reader utility should expose a destroy/delete function alongside its create function so callers can release resources after use.
- The base32 encoding and decoding functions should accept read-only pointers for the alphabet parameter, accurately reflecting that the data is only read, not modified.

## Why This Matters

Without correct pointer mutability on returned strings, callers cannot safely take ownership to free the memory, resulting in memory leaks. Without a destroy function for the bit reader, every created instance leaks. These issues would also be caught by address sanitizer and leak sanitizer runs in CI.
