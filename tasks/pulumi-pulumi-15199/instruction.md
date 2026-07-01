Extend the import plugin system to allow plugins to communicate additional properties about each resource during state conversion. Implement support for specifying whether a resource is a component, a remote component, and its logical name. Ensure these properties are correctly passed through the plugin boundary from server to client.

*   Update the `ResourceImport` struct in `sdk/go/common/resource/plugin/converter.go`:
    *   Add a `LogicalName` field of type `string`.
    *   Add an `IsComponent` field of type `bool`.
    *   Add an `IsRemote` field of type `bool`.

*   Modify the client-side converter plugin:
    *   In the `ConvertState` function located in `sdk/go/common/resource/plugin/converter_plugin.go`, ensure that `LogicalName`, `IsRemote`, and `IsComponent` are mapped from the proto `ResourceImport` message fields to the corresponding fields in the Go `ResourceImport` struct.

*   Modify the server-side converter:
    *   In the `ConvertState` function located in `sdk/go/common/resource/plugin/converter_server.go`, ensure that `LogicalName`, `IsRemote`, and `IsComponent` are mapped from the Go `ResourceImport` struct fields to the corresponding proto `ResourceImport` message fields.

*   Update the proto-generated `ResourceImport` type in `sdk/proto/go/converter.pb.go`:
    *   Include `LogicalName` as a `string` field with proto field number 6.
    *   Include `IsComponent` as a `bool` field with proto field number 7.
    *   Include `IsRemote` as a `bool` field with proto field number 8.
    *   Implement getter methods `GetLogicalName()`, `GetIsComponent()`, and `GetIsRemote()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.