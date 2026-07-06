Refactor the `PluginCustomValue` struct in nushell's plugin system to encapsulate its internal fields and add a "notify on drop" feature. Implement a constructor and getter methods to manage access to the internal data, and ensure the plugin can be notified when a custom value is dropped.

*   Implement a `pub(crate)` constructor for `PluginCustomValue`:
    *   Signature: `PluginCustomValue::new(name: String, data: Vec<u8>, notify_on_drop: bool, source: Option<Arc<PluginSource>>) -> PluginCustomValue`
    *   Replace all direct struct literal constructions with this constructor.

*   Implement public getter methods:
    *   `name(&self) -> &str`: Return the type name of the custom value.
    *   `data(&self) -> &[u8]`: Return the serialized binary data.
    *   `notify_on_drop(&self) -> bool`: Return whether the plugin should be notified on drop.

*   Implement a test-only accessor method:
    *   `source(&self) -> &Option<Arc<PluginSource>>`: Return a reference to the optional plugin source.
    *   Ensure this method is `pub(crate)` and only compiled in test builds (`#[cfg(test)]`).

*   Ensure encapsulation:
    *   Internal fields must be private to the `plugin_custom_value` module.
    *   Allow direct access to the `source` field within the module and its submodules.

*   Implement drop notification:
    *   When `notify_on_drop` is true, ensure the plugin receives a notification when the value is dropped.
    *   Emit the message 'DropCheck was dropped: <message>' to stderr exactly once, even if the value is copied.

*   Ensure compatibility with nushell operations:
    *   Support integer and string cell path access, sorting, and appending.
    *   Implement the 'custom-value drop-check' and 'custom-value generate' commands in the `nu_plugin_custom_values` test plugin.
    *   Ensure sorting and appending behaviors match specified outputs.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.