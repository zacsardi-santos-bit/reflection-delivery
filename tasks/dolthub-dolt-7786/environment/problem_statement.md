## Description

We want to add a Zstandard (zstd) compression library as a new dependency in Dolt. This library uses C bindings, meaning the build must enable CGO and the toolchain needs to support it. To verify that everything compiles and links properly end-to-end, we need a simple hidden admin command that exercises the compression library. This command can be temporary or minimal — its main purpose is to confirm that the dependency is wired up correctly and the binary can be built with CGO enabled.

## Expected Behavior

- A new hidden subcommand named 'zstd' is added under the 'dolt admin' command group
- Running 'dolt admin zstd' (from any directory, no repository needed) completes successfully
- The binary builds correctly with the compression library linked in
- The compression library package is included in the Go module dependencies

## Why This Matters

Without a concrete usage of the compression library in the binary, there is no way to verify that the CGO-based dependency actually compiles and links correctly across the build pipeline. Adding this admin command provides a lightweight integration smoke test and establishes the dependency for future use throughout the codebase.
