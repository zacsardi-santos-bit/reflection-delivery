## Description

The Wasmer C API currently supports compiling WebAssembly modules and instantiating them, but provides no way to inspect a compiled module's import requirements before instantiation. Without this capability, host applications and tooling cannot determine at runtime what a module expects from its environment — making it impossible to dynamically construct the right set of imports without out-of-band knowledge of the module's interface.

## Expected Behavior

- After compiling a WebAssembly module via the C API, users should be able to retrieve the full list of that module's required imports.
- Each import descriptor in the list should expose the import's kind (whether it is a function, memory, table, or global), the import's name, and the module/namespace it belongs to.
- The descriptors collection should be iterable by index and have a queryable length.
- The collection should be owned by the caller and provide a dedicated function for releasing its memory.

## Why This Matters

Many WebAssembly embedding scenarios — such as dynamic plugin systems, tooling, and sandboxed execution environments — need to introspect module dependencies at runtime. Without the ability to query what a module imports, hosts are forced to rely on hardcoded or pre-shared knowledge, which is fragile and limits flexibility. Adding import descriptor inspection makes the C API more complete and brings it to parity with the export inspection functionality that already exists.
