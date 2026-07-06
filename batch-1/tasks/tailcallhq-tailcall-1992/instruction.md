Implement support for gRPC services using "oneof" fields in protobuf message definitions. Ensure the system correctly deserializes such fields and maps them to GraphQL union types, while maintaining accurate schema representation and query functionality.

*   Accept gRPC configurations with proto messages containing oneof fields in both request and response types.
*   Generate client-facing GraphQL SDL for configurations with multiple GraphQL input types tagged to the same proto message type.
    *   Include all input variants, output types, union types, and query fields as defined.
*   Ensure the merged schema output includes all defined directives (@grpc, @tag, @server, @upstream, @link) on each type and query field.
*   Traverse union types to determine type usage, ensuring they and their member types are not pruned when referenced from query fields.
*   Correctly deserialize gRPC responses containing oneof fields and map the active variant to the corresponding GraphQL union type variant.
    *   For a response with binary body encoding `usual=5` and command oneof variant `{command: 'end'}`, return as `{"usual": 5, "command": {"command": "end"}}`.
*   Return HTTP response status 200 with Content-Type `application/json` and a `data` object for successful gRPC-backed GraphQL queries involving oneof response types.
*   Support GraphQL inline fragments on union type variants, returning fields of the active oneof variant present in the gRPC response.
*   Validate schema for arguments passed to union-typed query fields, allowing queries with structurally valid fields without overly strict schema rules.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.