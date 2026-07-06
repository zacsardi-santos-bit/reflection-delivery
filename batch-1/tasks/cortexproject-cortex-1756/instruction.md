Implement the necessary changes to enhance the key-value store's watch functionality. Ensure that watching a key without a pre-existing value works correctly, fix prefix-watching issues, and consolidate the string codec implementation.

*   Implement a `String` codec type in the `pkg/ring/kv/codec` package:
    *   Ensure it implements the `Codec` interface.
    *   Provide `Encode(d interface{}) ([]byte, error)` to convert string values to bytes.
    *   Provide `Decode(d []byte) (interface{}, error)` to convert bytes back to strings.
    *   Use `codec.String{}` as a value type, not a pointer.

*   Update the `WatchKey` function in the Consul client:
    *   Allow it to start watching keys without pre-existing values.
    *   Ensure it waits for the first value to be written and reports it to the callback.
    *   Continue watching for subsequent changes after the first value is reported.

*   Modify the `WatchPrefix` function:
    *   Deliver the full key name, including the prefix, to the callback.
    *   Ensure each key is reported only once per actual change.
    *   Prevent duplicate notifications for unchanged keys.
    *   Only report keys that match the specified prefix.

*   Ensure the in-memory Consul client (`NewInMemoryClient` and `NewInMemoryClientWithConfig`):
    *   Functions correctly when `WatchKey` or `WatchPrefix` is called before any key values are written.
    *   Does not require pre-existing values to start watching.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.