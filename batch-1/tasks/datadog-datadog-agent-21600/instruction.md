Implement improvements to the secrets resolution system in the Datadog Agent to support accurate path tracking for secrets within YAML arrays and enable periodic secret refreshes. Update the configuration and subscription mechanisms to accommodate these enhancements.

*   Update the `Configure` method:
    *   Add an `int` parameter for `refreshInterval` between `maxSize` and `groupExecPerm`.
    *   Signature: `Configure(command string, arguments []string, timeout int, maxSize int, refreshInterval int, groupExecPerm bool, removeLinebreak bool)`.
*   Modify the `secretContext` struct:
    *   Replace `yamlPath string` with `path []string`.
    *   Ensure each YAML key or array index is a separate element in `path`.
*   Implement path tracking for secrets in YAML arrays:
    *   Include numeric string indices for array elements in `path`.
    *   Ensure different array positions have distinct path entries.
*   Replace `ResolveWithCallback` with `SubscribeToChanges`:
    *   Register a `SecretChangeCallback` for notifications on secret resolution or value change.
    *   Signature: `SubscribeToChanges(callback SecretChangeCallback)`.
    *   Callback type: `type SecretChangeCallback func(handle, origin string, path []string, oldValue, newValue any)`.
*   Implement `Refresh` method:
    *   Signature: `Refresh() error`.
    *   Re-fetch all known secret handles and notify subscribers for changed values.
    *   Use `allowlistHandles` to control which handles can be updated.
*   Manage `allowlistHandles`:
    *   Default value: `[]string{"api_key"}`.
    *   Only update and notify for handles in this list during `Refresh`.
*   Ensure `fetchSecret` does not update the cache with fetched values.
*   Update debug output for secrets:
    *   Use the new path format, joining elements with '/'.
    *   Include numeric segments for array indices.
*   Add `configAssignAtPath` function:
    *   Signature: `configAssignAtPath(config pkgconfigmodel.Config, settingPath []string, newValue any) error`.
    *   Navigate config using path segments, treating numeric strings as array indices.
    *   Return specific errors for unknown config keys and out-of-range indices.
*   Implement `MockSecretResolver`:
    *   Replace `ResolveWithCallback` with `SubscribeToChanges` and `Refresh`.
    *   Add `SetBackendCommand(command string)` method.
    *   Add `SetFetchHookFunc(f func([]string) (map[string]string, error))` method for testing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.