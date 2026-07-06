Update the workspace configuration to include the PEM certificate encoding library as a shared dependency. Ensure all relevant crates within the workspace reference this library through the workspace dependency mechanism instead of specifying it locally.

*   Modify the root workspace `Cargo.toml`:
    *   Add `pem = "3"` under `[workspace.dependencies]`.
*   Update individual crate `Cargo.toml` files to use the workspace dependency:
    *   In `mirrord/agent/Cargo.toml`, change the dev-dependency from `pem = "3"` to `pem.workspace = true`.
    *   In `mirrord/auth/Cargo.toml`, change the dependency from `pem = "3"` to `pem.workspace = true`.
    *   In `mirrord/tls-util/Cargo.toml`, change the dev-dependency from `pem = "3"` to `pem.workspace = true`.
*   Ensure the workspace resolves its dependency graph without errors.
*   Verify that the `mirrord-protocol` crate compiles successfully and its codec unit tests run and pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.