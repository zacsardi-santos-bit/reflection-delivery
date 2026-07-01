Implement a feature in a Rust build tool to handle git information retrieval failures differently based on the mode. In default mode, emit warnings for each git variable that cannot be set without inserting placeholder values. In idempotent mode, continue using placeholder defaults with warnings.

*   Update the `add_default_map_entry` function in `vergen-lib/src/utils.rs`:
    *   Accept `idempotent: bool` as the first parameter.
    *   If `idempotent` is `true` and no environment variable override exists, insert the default placeholder value into the map and push a warning: `"{key} set to default"`.
    *   If `idempotent` is `false` and no environment variable override exists, do not insert any entry into the map and push a warning: `"Unable to set {key}"`.

*   Modify the `DefaultConfig` struct in `vergen-lib/src/entries.rs`:
    *   Add an `idempotent: bool` field.
    *   Update the `new` constructor to accept `idempotent: bool` as the first parameter.
    *   Implement a public getter method `idempotent(&self) -> &bool`.

*   Update all call sites of `add_default_map_entry` to pass the appropriate `idempotent` boolean:
    *   Files: `vergen-git2`, `vergen-gitcl`, `vergen-gix`, `vergen/src/feature/build.rs`, `vergen/src/feature/cargo.rs`, `vergen/src/feature/rustc.rs`, `vergen/src/feature/si.rs`.

*   Update all call sites of `DefaultConfig::new` in `vergen-lib/src/emitter.rs`:
    *   Pass `self.idempotent` as the first argument.

*   Ensure the `Emitter::default()` behavior:
    *   In non-idempotent mode, emit warnings like `cargo:warning=Unable to set VERGEN_GIT_BRANCH` for each git variable when git data is unavailable.
    *   Ensure the operation does not fail (failed flag returned by `emit_to` is `false`).

*   Ensure `Emitter::default().idempotent()` behavior:
    *   Emit warnings like `cargo:warning=VERGEN_GIT_<FIELD> set to default` and set default placeholder values when git data is unavailable.
    *   Implement the `.idempotent()` builder method on `Emitter` that returns `Self` for method chaining.

*   Ensure idempotent mode is explicitly opted into by calling `.idempotent()` on the emitter builder.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.