## Description

The system currently does not correctly support gRPC services whose protocol buffer message definitions use the "oneof" construct. When a gRPC service has messages where a field can be one of several possible sub-message types (a common protobuf pattern), the system fails to handle the response correctly. Specifically, when the gRPC backend returns a binary response that includes a oneof field, the system either crashes or returns incorrect data instead of mapping the active variant to the appropriate GraphQL union type.

## Expected Behavior

- The system should accept configurations where proto messages contain oneof fields (both in request and response message positions).
- When a gRPC response is received containing a oneof field, the system should correctly deserialize it and identify which variant of the oneof was set.
- The identified variant should be mapped to the correct GraphQL union type member, and all relevant fields should be returned in the response.
- The generated schema output (both the client-facing view and the merged configuration view) should correctly represent all defined input variants, output union types, and their associated query fields.
- GraphQL inline fragments on union type members should work correctly, returning fields from the active oneof variant.

## Why This Matters

Many real-world gRPC services use oneof fields to represent discriminated unions — for example, a response that could be either an error payload or a success payload. Without support for these, developers cannot expose entire categories of gRPC services through the GraphQL gateway. This is a significant limitation for any project that relies on gRPC services using this common protobuf feature.
