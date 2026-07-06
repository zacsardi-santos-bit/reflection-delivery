I'm working on a performance improvement for the Wasmtime WebAssembly runtime.

*   When a WebAssembly `memory.copy` or `array.copy` instruction has a compile-time-constant length whose byte count is 128 or fewer, the compiler must expand the copy inline (as a sequence of load and store instructions) instead of calling the `memory_copy` libcall.

*   The inline expansion must cover the byte range greedily using the widest available integer/vector type in this order: 16-byte (`i8x16`), 8-byte (`i64`), 4-byte (`i32`), 2-byte (`i16`), 1-byte (`i8`). For example, 28 bytes must decompose into one i8x16 (16 bytes) + one i64 (8 bytes) + one i32 (4 bytes), not a longer sequence of narrower types.

*   All chunk loads must be emitted before any chunk stores, so that overlapping source and destination ranges are copied correctly (memmove semantics). The generated IR must show all loads appearing before any stores for the same inline copy.

*   Memory flags for every inline-copy load and store must explicitly use little-endian byte order. This is required for correctness on big-endian targets (such as big-endian Pulley), where SIMD (v128) load/store instructions only encode a little-endian variant and will assert if given a big-endian flag.

*   For an `array.copy` with a compile-time-constant element count, the byte length passed to the inline threshold check must be `element_count * element_size_in_bytes`. Arrays of i32 (4 bytes each) with 7 elements is 28 bytes, which is within the 128-byte threshold and must be inlined.

*   Copies with a statically-known byte length greater than 128 bytes must continue to use the `memory_copy` libcall path rather than the inline path.

*   The inline copy optimization must produce functionally correct results for all WebAssembly array element types: i8, i16, i32, i64, f32, f64, and v128. The bit pattern of each element must be preserved exactly.

*   Overlapping `array.copy` operations on the same array must produce correct results in both forward-overlap (destination index less than source index) and backward-overlap (destination index greater than source index) cases, because the load-before-store ordering guarantees memmove semantics.

*   The threshold boundary must be exact: copying 16 i64 elements (128 bytes total) must use the inline path, while copying 17 i64 elements (136 bytes total) must use the libcall path. Both paths must produce correct results.


*   Interface details: NO INTERFACES NEEDED

The tests in this task are compiler disassembly tests (`.wat` files with expected IR output in comments) and functional WebAssembly tests (`.wast` files). They do not import or call specific Rust function names directly. Instead, they verify that the Wasmtime Cranelift compiler produces specific IR or assembly output when compiling WebAssembly bulk copy instructions with constant lengths.

All implementation work lives inside the compiler internals in `crates/cranelift/src/func_environ.rs`. The tests exercise observable compiler behavior — what machine code or IR is emitted for specific WebAssembly inputs — rather than calling named Rust APIs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.