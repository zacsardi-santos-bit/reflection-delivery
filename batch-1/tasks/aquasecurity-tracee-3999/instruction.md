Update the project to use the modern gRPC client connection function and ensure compatibility with the latest Go and gRPC library versions. Replace deprecated function calls, update module files, and modify installation scripts to reflect the new versions.

*   Replace all instances of the deprecated gRPC connection function `grpc.Dial` with the modern `grpc.NewClient` function across the entire codebase.
    *   Specifically update the following files:
        *   `pkg/server/grpc/server_test.go` — modify the `grpcClient` helper.
        *   `tests/e2e-inst-signatures/scripts/ds_writer/ds_writer.go` — update the `main` function.
        *   `pkg/containers/runtime/containerd.go` — replace the connection call.
        *   `pkg/containers/runtime/crio.go` — replace the connection call.

*   Update the Go module files to ensure compatibility with newer versions:
    *   Modify `go.mod` to declare compatibility with Go version `1.22` and update the gRPC library to version `v1.64.0`.
    *   Update `go.sum` to reflect new checksums for the updated dependencies.

*   Revise the dependency installation script to use the latest Go version:
    *   In `tests/e2e-install-deps.sh`, change the Go download URLs to version `1.22.3` for both `x86_64` (amd64) and ARM64 architectures.

*   Ensure that all changes maintain the integrity of existing tests:
    *   Verify that the package at `pkg/server/grpc` compiles without errors and that all existing tests, including event conversion tests and the gRPC server integration test, pass successfully.
    *   Confirm that the `ds_writer` utility compiles cleanly after the updates.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.