## Description

The database server is gaining a new gRPC-based admin shell service, and the protocol buffer definition file along with its auto-generated client/server stubs need to be formally introduced to the repository. Currently, neither the proto definition file nor the generated Rust code exists in the repository, which means the new service cannot be built or used.

## Expected Behavior

- A protobuf definition file for the admin shell service must be added to the libsql-server/proto/ directory under the name admin_shell.proto.
- The corresponding auto-generated Rust code (client and server stubs) must be compiled from that proto file and committed to libsql-server/src/generated/.
- A bootstrap test must be added that re-compiles the proto file and verifies the committed generated code is still in sync — the test should fail with a clear message if the committed files are out of date.
- The build tooling dependencies needed to compile proto files must be declared in the package's dev-dependencies.

## Why This Matters

Without committed generated stubs, the admin shell service cannot be referenced from other Rust source files. The sync-check test ensures that future edits to the proto definition do not silently diverge from the committed generated code, catching the common mistake of modifying the proto file without regenerating and committing the output.
