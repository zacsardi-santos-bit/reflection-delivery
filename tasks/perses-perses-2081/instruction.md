Implement a utility package that converts CUE schema definitions into structured tree representations and combines them with discovered proxy endpoints to produce complete datasource plugin configurations. Ensure the utility supports Prometheus and Tempo datasource schemas, capturing necessary field details and injecting proxy configurations correctly.

*   Define a `NodeType` string-based type in `internal/api/discovery/cuetils/node.go` with constants:
    *   `StringNodeType` with value "string"
    *   `BoolNodeType` with value "bool"
    *   `IntegerNodeType` with value "integer"
    *   `FloatNodeType` with value "float"
    *   `StructNodeType` with value "struct"

*   Create a `Node` struct in `internal/api/discovery/cuetils/node.go` with:
    *   Exported fields: 
        *   `Type` of type `NodeType` with JSON and YAML tags "type"
        *   `FieldName` of type `string` with JSON and YAML tags "field_name"
        *   `ConcreteValue` of type `string` with JSON and YAML tags "concrete_value"
        *   `Nodes` of type `[]*Node` with JSON and YAML tags "nodes"
    *   An unexported `sort()` method to recursively sort `Nodes` by `FieldName`.

*   Implement `NewFromSchema` function in `internal/api/discovery/cuetils/cuetils.go`:
    *   Accepts a `cue.Value` and returns a `[]*Node` tree, excluding optional fields.
    *   Returns an error if the top-level value is not a struct.
    *   For Prometheus and Tempo schemas, ensure the tree includes:
        *   A `StringNodeType` node with `FieldName="kind"` and appropriate `ConcreteValue`.
        *   A `StructNodeType` node with `FieldName="spec"` containing specific nodes for "directUrl" and "proxy".

*   Implement `BuildPluginAndInjectProxy` function in `internal/api/discovery/cuetils/cuetils.go`:
    *   Accepts a `[]*Node` tree and an `http.Config` proxy value.
    *   Returns a `common.Plugin` with the `Kind` field set from the "kind" node's `ConcreteValue`.
    *   Injects the proxy configuration into the schema tree at the location marked by the HTTPProxy kind.
    *   Returns an error if the schema does not contain exactly two root nodes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.