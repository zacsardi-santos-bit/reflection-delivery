## Description

We need a new integration test suite for Wasmtime's WebAssembly compilation pipeline that tests code generation using Wasmtime's own code generator, rather than the lower-level Cranelift filetest infrastructure.

Currently, tests for WebAssembly-to-native-code compilation are written against a lower-level testing framework that requires test authors to manually specify virtual machine globals, heap configurations, memory bounds, and other low-level details inline in the test file. This means tests don't go through Wasmtime's actual code generation path, so they can miss issues specific to Wasmtime's calling conventions, memory model, and runtime configuration.

## Expected Behavior

- A new test directory should exist where developers can place WebAssembly text-format files as test cases.
- Each test file should declare a target architecture and optionally a test mode (raw output, optimized output, or final machine code) using simple header directives embedded as comments at the top of the file.
- Tests should optionally accept Wasmtime CLI flags as directives to configure the compilation environment.
- Expected output should be embedded in the test file as comments, and the test system should verify the actual compilation output matches.
- The test system should use Wasmtime's real code generator rather than a lower-level stub, so the tests reflect real-world behavior.

## Why This Matters

This allows testing the full Wasmtime compilation pipeline end-to-end — including Wasmtime-specific memory layouts, calling conventions, and optimizations — without having to manually replicate low-level IR details in each test file. It makes tests easier to write, more accurate, and directly comparable to what Wasmtime actually produces at runtime.
