## Description

The tinymist language server currently runs only as a native binary. There is no way to embed or use its core functionality in JavaScript environments such as web browsers or Node.js applications. To enable web-based tooling — for example, running language analysis inside a browser-hosted editor — the tool needs a WebAssembly build target.

## Expected Behavior

- A new core library crate should be created that can be compiled to WebAssembly using standard tooling.
- The compiled WebAssembly module should be packaged as an ES module importable from JavaScript.
- Once the module is initialized, it should expose a version query function that JavaScript callers can invoke to retrieve information about the running build.
- The module should be initializable in Node.js and browser environments by passing the raw binary data directly, without requiring network fetches.

## Why This Matters

Providing a WebAssembly build of the core library makes it possible to integrate tinymist's analysis capabilities into web-based editors and tools. The version query is a simple but important first step — it validates that the module loads correctly and that the WebAssembly JavaScript bindings work as expected. Build metadata (timestamps, git info, target triple) included in the version output helps identify exactly which build of the library is running.
