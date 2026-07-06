## Description

The project currently uses a deprecated function for establishing gRPC client connections throughout the codebase. This deprecated function has been removed in newer releases of the gRPC library, which means attempting to upgrade gRPC or the Go runtime to current versions causes compilation failures across several packages.

## Expected Behavior

- All gRPC client connection setup code should use the modern replacement function, which accepts the same parameters but is the currently supported API
- The Go module files should be updated to target a newer version of Go and use the current gRPC library release
- The dependency installation scripts should reference an updated Go release for all supported architectures
- After the migration, the existing tests in the gRPC server package and the e2e instrumentation tooling must continue to pass

## Affected Areas

- Container runtime enricher code (containerd and CRI-O integrations)
- The e2e instrumentation data-store writer utility
- Module dependency declarations and checksums

## Why This Matters

Staying on outdated, deprecated APIs blocks the project from adopting security fixes and improvements in Go and gRPC. The deprecated connection function has been removed upstream, so this migration is a prerequisite for any dependency upgrade and for keeping the build working as library versions advance.
