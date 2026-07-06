Implement a new package to provide a unified tree representation for structured data, allowing conversion of Go runtime values and YAML/JSON documents into a traversable node model. Ensure each node is typed and traversable with a consistent API.

*   Define a `Node` interface in `pkg/common/structurev2/` with:
    *   `Type() NodeType` to return the node type.
    *   `NodeScalarValue() (any, error)` to return scalar values.
    *   `Children() func(func(NodeChildrenKey, Node) bool)` for child iteration.
*   Implement `NodeType` with constants: `ScalarNodeType`, `SequenceNodeType`, `MapNodeType`.
*   Create `NodeChildrenKey` struct with `Index int` and `Key string` fields.
*   Implement `FromGoValue` function in `pkg/common/structurev2/`:
    *   Convert scalar Go values to `ScalarNodeType`.
    *   Convert slices to `SequenceNodeType`, iterating with correct index.
    *   Convert maps to `MapNodeType`, iterating with correct key.
    *   Handle nested structures recursively.
*   Implement `AlphabeticalGoMapKeyOrderProvider` in `pkg/common/structurev2/`:
    *   `GetOrderedKeys(path string, m map[string]any) ([]string, error)` returns sorted keys.
*   Implement `appendPath` function in `pkg/common/structurev2/`:
    *   Construct dotted path strings with proper dot escaping.
*   Implement `StandardScalarNode[T]` struct in `pkg/common/structurev2/`:
    *   Store scalar value and return it via `NodeScalarValue()`.
*   Implement `StandardSequenceNode` struct in `pkg/common/structurev2/`:
    *   Store nodes in a slice and iterate with `Children()`.
*   Implement `StandardMapNode` struct in `pkg/common/structurev2/`:
    *   Store nodes and keys, iterating with `Children()`.
*   Implement `FromYAML` function in `pkg/common/structurev2/`:
    *   Parse YAML/JSON strings into node trees.
    *   Map scalar types correctly (null, bool, string, int, float, timestamp).
    *   Handle sequences and mappings with correct node types and child access.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.