Set up a gRPC-based admin shell service in the `libsql-server` crate by adding a protocol buffer definition file and generating the corresponding Rust client and server stubs. Ensure the generated code is committed and remains in sync with the proto definition through a bootstrap test.

*   Create a protobuf definition file:
    *   Add `libsql-server/proto/admin_shell.proto`.
    *   Ensure it contains a valid proto3 service definition compilable with `tonic_build` version 0.11.
*   Generate and commit Rust stubs:
    *   Compile `admin_shell.proto` using `tonic_build::configure().build_client(true).build_server(true).build_transport(true)`.
    *   Use `prost_build::Config::new()` for prost configuration.
    *   Place the generated Rust files in `libsql-server/src/generated/`.
    *   Commit these files to the git repository.
    *   Ensure `git diff --exit-code` on `src/generated/` returns 0 to confirm files are in sync.
*   Update `Cargo.toml`:
    *   Add the following to `[dev-dependencies]` in `libsql-server/Cargo.toml`:
        *   `tonic-build = "0.11"`
        *   `prost-build = "0.12"`
*   Ensure bootstrap test setup:
    *   The test file `libsql-server/tests/bootstrap.rs` will recompile the proto file and verify the generated code is up-to-date.
    *   The test will fail with the message "You should commit the protobuf files" if the generated files are out of sync.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.