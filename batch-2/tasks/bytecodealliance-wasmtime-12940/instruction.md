I'm working on the Winch baseline compiler in wasmtime and it doesn't support reference type instructions on x86_64.

*   The Winch compiler backend for x86_64 must support the ref.null instruction for function reference types, producing a zero (null pointer) value in the result register.

*   The Winch compiler backend for x86_64 must support the ref.is_null instruction for funcref values, comparing the value against zero and returning 1 if null or 0 if non-null.

*   The Winch compiler backend for x86_64 must support the ref.func instruction (used with declared element segments), emitting a runtime call to retrieve the function reference for the given function index.

*   The Winch compiler backend for x86_64 must support typed select (select (result funcref)) over two funcref values, using a conditional move instruction to select between them based on an i32 condition.

*   When running under the Winch compiler, ref.null func followed by ref.is_null must return 1 (true), and ref.func followed by ref.is_null must return 0 (false).

*   When running under the Winch compiler, call_indirect through a table populated via elem segments with ref.func must return the correct value from the called function.

*   When running under the Winch compiler, call_indirect on an uninitialized table element must trap with the message 'uninitialized element'.

*   When running under the Winch compiler, out-of-bounds table.get and table.set accesses must trap with the message 'out of bounds table access'.

*   When running under the Winch compiler, typed select between two funcrefs with condition 1 must return the first operand, and with condition 0 must return the second operand.

*   When running under the Winch compiler, table.get must return the correct funcref or null funcref for initialized/uninitialized slots; table.set must correctly store a null or non-null reference into a slot.

*   When running under the Winch compiler, table.grow must increase the table size by the requested amount and return the old size; table.size must return the current table element count.

*   The test for trampolines with declared elements must only require the reference_types and bulk_memory WebAssembly features, not gc_types.


*   Interface details: The tests in this task are disassembly snapshot tests and functional WebAssembly tests. They do not import or call specific Rust functions by name from the test side. Instead, they verify that:

1. The Winch compiler emits correct x86_64 machine code for specific WebAssembly instructions (checked via `.wat` snapshot files with expected disassembly comments).
2. The Winch compiler correctly executes WebAssembly modules using reference type instructions (checked via `.wast` functional test files).

The tests rely on the Winch compiler's internal instruction lowering to handle the following WebAssembly instructions for x86_64:
- `ref.null` (for funcref type)
- `ref.is_null` (for funcref values)
- `ref.func` (with declared element segments)
- `select` with a reference type result (`select (result funcref)`)
- `table.get`, `table.set`, `table.grow`, `table.size` (for funcref tables)
- `call_indirect` through funcref-populated tables

The test files that must be created or modified are:

**New files** (disassembly snapshot tests):
- `tests/disas/winch/x64/ref/func.wat` — snapshot for `ref.func` instruction
- `tests/disas/winch/x64/ref/is_null.wat` — snapshot for `ref.is_null` instruction
- `tests/disas/winch/x64/ref/null.wat` — snapshot for `ref.null func` instruction
- `tests/disas/winch/x64/select/typed.wat` — snapshot for typed select over funcrefs

**New file** (functional test suite):
- `tests/misc_testsuite/winch/ref-types-basic.wast` — comprehensive functional tests for reference types under Winch

**Modified file**:
- `tests/all/func.rs` — the `trampoline_for_declared_elem` test's feature list must only include `reference_types` and `bulk_memory`, not `gc_types`

The disassembly snapshot files use the format `;;! target = "x86_64"`, `;;! test = "winch"` headers, followed by the WAT module, then `;;` comments showing expected x86_64 assembly output per function. The functional test files use `;;! reference_types = true` header and standard WAST assertion syntax.

No new Rust public API is introduced — this is an internal compiler capability addition.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.