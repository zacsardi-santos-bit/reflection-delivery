I'm working with Wasmtime's async component model and I need to add support for passing streams and futures across component instance boundaries.

*   A new WIT interface named 'cross-instance' must be added to the test WIT file, containing an async function 'make' that returns a tuple of stream<u8> and future<u8>.

*   A new WIT world named 'cross-instance-source' must be added to the test WIT file, exporting the 'cross-instance' interface.

*   A new WebAssembly component implementing the 'cross-instance-source' world must be created and made available as the 'ASYNC_CROSS_INSTANCE_SOURCE_COMPONENT' constant in the test program artifacts module.

*   The component's 'make' function must return a stream<u8> that produces the byte values [2, 4, 6, 8, 9] in order, and a future<u8> that resolves to the value 10.

*   The stream and future returned by the 'make' function of the source component must be transferable to a different component instance (the existing closed-streams component) for consumption, with both the stream read and future read completing successfully.


*   Interface details: ## WIT Additions

The following additions must be made to `crates/misc/component-async-tests/wit/test.wit`:

**New interface** (name: `cross-instance`):
```
interface cross-instance {
  make: async func() -> tuple<stream<u8>, future<u8>>;
}
```

**New world** (name: `cross-instance-source`):
```
world cross-instance-source {
  export cross-instance;
}
```

The existing WIT file uses the package `local:local`. The exported `cross-instance` interface will therefore be accessed as `local:local/cross-instance` in generated bindings, which produces the method name `local_local_cross_instance()` on the generated bindings struct.

---

## Test Program Artifact

Type: Constant
Name: `ASYNC_CROSS_INSTANCE_SOURCE_COMPONENT`
Location: auto-generated test program artifacts (the `test_programs_artifacts` crate/module, typically populated via a build script that scans `crates/test-programs/src/bin/`)
Description: File path to the compiled WebAssembly component that implements the `cross-instance-source` world. The constant name is derived from the source filename. To get a constant named `ASYNC_CROSS_INSTANCE_SOURCE_COMPONENT`, the source file must be named `async_cross_instance_source.rs`.

---

## Component Implementation

A new Rust WebAssembly component source must be created at:
`crates/test-programs/src/bin/async_cross_instance_source.rs`

This file implements the `cross-instance-source` world using `wit_bindgen`. Its `make` async function must:
- Return a `stream<u8>` that produces the byte values `[2, 4, 6, 8, 9]` (in that order)
- Return a `future<u8>` that resolves to `10u8`
- Both values must be produced concurrently (spawned as a background task while returning the readers)

The component WIT path binding uses:
```
path: "../misc/component-async-tests/wit"
world: "cross-instance-source"
```

The `Guest` trait to implement is at the path `exports::local::local::cross_instance::Guest` in the generated bindings.

A `fn main() {}` stub is required since the file is built as a Rust `bin` target.

---

## Bindgen-Generated Bindings

The test uses bindgen-generated code from the `cross-instance-source` world:

```rust
wasmtime::component::bindgen!({
    path: "wit",
    world: "cross-instance-source",
});
```

This generates:
- Module: `cross_instance_source`
- Struct: `cross_instance_source::CrossInstanceSource`
  - Constructor: `CrossInstanceSource::new(&mut store, &instance) -> Result<Self>`
  - Method: `.local_local_cross_instance()` — returns an accessor for the `cross-instance` interface
  - Interface accessor method: `.call_make(accessor) -> impl Future<Output = Result<(stream_type, future_type)>>`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.