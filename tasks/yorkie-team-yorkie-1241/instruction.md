Implement a mechanism to serialize and deserialize a collaborative document's state, including CRDT types, into a JSON format. Update the tree initialization API to accept value types instead of pointers for consistency.

*   Update Tree Initialization:
    *   Modify the `SetNewTree` method on `json.Object` to accept a value type for the initial root node with the signature: `SetNewTree(k string, initialRoot ...TreeNode) *Tree`.
    *   Modify the `AddNewTree` method on `json.Array` to accept a value type for the initial root node with the signature: `AddNewTree(initialRoot ...TreeNode) *Tree`.
    *   Update `BuildIndexTree` in `test/helper/helper.go` to accept `json.TreeNode` as a value type.

*   Define YSON Package:
    *   Create a new package `pkg/document/yson` with the following types:
        *   `Object`: `map[string]interface{}`
        *   `Array`: `[]interface{}`
        *   `Counter`: struct with `Type crdt.CounterType` and `Value interface{}`
        *   `TreeNode`: struct with fields `Type string`, `Children []TreeNode`, `Value string`, `Attributes map[string]string`
        *   `Tree`: struct with `Root TreeNode`
        *   `TextNode`: struct with `Value string` and `Attributes map[string]string`
        *   `Text`: struct with `Nodes []TextNode`
    *   Implement `Marshal()` and `Unmarshal(data string) error` methods for `Counter`, `Array`, `Object`, `Tree`, and `Text`.

*   Conversion Functions:
    *   Implement `FromCRDT(elem crdt.Element) (interface{}, error)` in `pkg/document/yson/to_yson.go` to convert CRDT elements to their YSON representations.

*   Serialization Format:
    *   Use type tags for serialization: `0` for Primitive, `1` for Counter, `2` for Array, `3` for Object, `4` for Tree, `5` for Text.
    *   Define value type tags for Primitives: `0` for null, `1` for bool, `2` for int32, `3` for int64, `4` for float64, `5` for string, `6` for bytes, `7` for date.
    *   Implement specific marshaling formats for each type, ensuring empty structures are serialized correctly.

*   Error Handling:
    *   Ensure `Array.Unmarshal` returns errors with 'invalid format' or 'invalid type' for malformed data.
    *   Ensure `Text.Unmarshal` returns errors with 'failed to unmarshal text', 'invalid text node format', or 'invalid type' for incorrect data.
    *   Ensure `Tree.Unmarshal` returns errors with 'failed to unmarshal tree', 'invalid tree value', or 'invalid type' for unexpected structures.

*   Ensure Round-Trip Fidelity:
    *   Verify that converting a document's root CRDT state to YSON and back results in identical serialized output.
    *   Handle special characters and multibyte UTF-8 strings correctly during serialization and deserialization.

*   Type Alias:
    *   Define `json.TreeNode` as a type alias for `yson.TreeNode` in `pkg/document/json/tree.go`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.