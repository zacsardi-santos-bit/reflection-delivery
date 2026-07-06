## Description

The Winch compiler backend in Wasmtime does not support the SIMD byte shuffle instruction, causing compilation to fail with an error indicating the instruction is not yet implemented when a WebAssembly module uses it. This means developers who rely on Winch for fast-startup or low-latency JIT compilation cannot run programs that use SIMD to rearrange or select bytes across two 128-bit vectors.

## Expected Behavior

- When Winch is used to compile a WebAssembly module that contains the SIMD byte shuffle instruction, compilation should succeed without errors on x86_64 systems with the required vector extension support.
- The resulting native code should correctly select bytes from either of the two 128-bit source vectors according to the shuffle lane pattern, using the appropriate vector instructions for the target architecture.
- The new SIMD lane shuffle test suite file should be recognized as requiring the necessary vector extensions to run, so that test infrastructure skips it gracefully on machines without that support.

## Why This Matters

Winch is meant to be a fast-compilation alternative to Cranelift, so it needs to progressively gain coverage over the WebAssembly SIMD instruction set. Programs that rely on byte-level vector rearrangement (a common SIMD operation) currently cannot be compiled by Winch at all, making the backend less practical for real-world use.
