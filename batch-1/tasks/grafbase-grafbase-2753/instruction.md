Update the Grafbase CLI to ensure that new extension projects reference the current SDK version. Fix any compilation issues in the codebase to ensure that all components build successfully.

*   Update the generated project templates:
    *   For resolver extension projects:
        *   Ensure `Cargo.toml` includes `grafbase-sdk = "0.5.4"` in the `[dependencies]` section.
        *   Ensure `Cargo.toml` includes `grafbase-sdk = { version = "0.5.4", features = ["test-utils"] }` in the `[dev-dependencies]` section.
    *   For authentication extension projects:
        *   Ensure `Cargo.toml` includes `grafbase-sdk = "0.5.4"` in the `[dependencies]` section.
        *   Ensure `Cargo.toml` includes `grafbase-sdk = { version = "0.5.4", features = ["test-utils"] }` in the `[dev-dependencies]` section.
*   Resolve any compilation issues:
    *   Ensure the entire codebase, including the `grafbase-sdk` crate and all dependent packages, compiles without errors after making the necessary updates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.