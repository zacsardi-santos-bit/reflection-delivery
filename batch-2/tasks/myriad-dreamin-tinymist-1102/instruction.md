Implement a new Rust library crate for the tinymist language server that can be compiled to WebAssembly and used in JavaScript environments. Ensure the module can be initialized in both Node.js and browser environments and exposes a version query function.

*   Create a new Rust library crate named 'tinymist-core' at 'crates/tinymist-core/'.
    *   Set 'crate-type = ["cdylib", "rlib"]' in the Cargo.toml to support shared library and Rust library compilation.
    *   Define a 'web' feature in Cargo.toml to enable 'wasm-bindgen' when active.
*   Implement the 'web' feature to be activatable with '--no-default-features --features web'.
*   Export a 'version()' function from the WASM package:
    *   Location: crates/tinymist-core/src/web.rs
    *   Signature: `version() -> String`
    *   Annotate with #[wasm_bindgen] for JavaScript accessibility.
    *   Return a multi-line string with build metadata (timestamp, git info, target triple, Typst version/source).
*   Create a build script 'build.rs' at 'crates/tinymist-core/build.rs':
    *   Generate environment variables: VERGEN_BUILD_TIMESTAMP, VERGEN_GIT_DESCRIBE, VERGEN_GIT_SHA (optional), VERGEN_GIT_COMMIT_TIMESTAMP (optional), VERGEN_GIT_BRANCH (optional), VERGEN_CARGO_TARGET_TRIPLE, TYPST_VERSION, TYPST_SOURCE.
    *   Use 'vergen' and 'cargo_metadata' as build dependencies.
*   Ensure the compiled WASM is importable as an ES module:
    *   JavaScript import path: 'pkg/tinymist_core.js'
    *   WASM binary artifact: 'pkg/tinymist_core_bg.wasm'
*   Expose a default-exported initialization function:
    *   Accepts 'module_or_path' as a Uint8Array for direct initialization in Node.js and browsers.
*   Define a static LONG_VERSION in 'lib.rs':
    *   Location: crates/tinymist-core/src/lib.rs
    *   Signature: `pub static LONG_VERSION: LazyLock<String>`
    *   Populate from build-time environment variables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.