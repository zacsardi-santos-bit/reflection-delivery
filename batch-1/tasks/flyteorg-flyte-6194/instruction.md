Implement a function to convert interface values to Flyte core Literal_Scalar primitives, ensuring proper type handling for various numeric types, strings, and booleans. Define a new error code for invalid primitive conversions and ensure the resolver returns typed primitives instead of binary blobs.

*   Define a constant:
    *   Name: `InvalidPrimitiveType`
    *   Type: `ErrorCode`
    *   Value: `"InvalidPrimitiveType"`
    *   Location: `flytepropeller/pkg/controller/nodes/errors/codes.go`

*   Implement the function `convertInterfaceToLiteralScalar`:
    *   Location: `flytepropeller/pkg/controller/nodes/attr_path_resolver.go`
    *   Signature: `convertInterfaceToLiteralScalar(nodeID string, obj interface{}) (*core.Literal_Scalar, error)`
    *   Convert signed integers (int8, int16, int32, int64, int) to `Scalar_Primitive` with `Primitive_Integer` (int64).
    *   Convert unsigned integers (uint8, uint16, uint32) to `Scalar_Primitive` with `Primitive_Integer` (int64).
    *   Handle `uint64`:
        *   If exceeding `math.MaxInt64`, return an error with "InvalidPrimitiveType".
        *   Otherwise, convert to `Scalar_Primitive` with `Primitive_Integer`.
    *   Handle `uint`:
        *   If exceeding `math.MaxInt`, return an error with "InvalidPrimitiveType".
        *   Otherwise, convert to `Scalar_Primitive` with `Primitive_Integer`.
    *   Convert `float32` to `Scalar_Primitive` with `Primitive_FloatValue` (as float64).
    *   Convert `float64` directly to `Scalar_Primitive` with `Primitive_FloatValue`.
    *   Convert `string` to `Scalar_Primitive` with `Primitive_StringValue`.
    *   Convert `bool` to `Scalar_Primitive` with `Primitive_Boolean`.
    *   Return an error with "InvalidPrimitiveType" for unsupported types.

*   Ensure the attribute path resolver returns a `Scalar_Primitive` literal for primitive types in binary-encoded dataclass literals, avoiding re-encoding as `Scalar_Binary`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.