Implement a plugin persistence and garbage collection system for nushell's plugin infrastructure to improve performance and manage resources effectively. Ensure plugins remain active between invocations, allow users to manage them, and automatically stop them after a configurable idle period.

*   Replace `PluginIdentity` with `PluginSource`:
    *   Implement `new_fake(name: &str) -> PluginSource` and `name(&self) -> &str` methods.
    *   Update `PluginCustomValue.source` to `Option<Arc<PluginSource>>`.
    *   Ensure source equality is checked via `Arc` pointer identity.

*   Update `PluginCustomValue` methods:
    *   `add_source(val: &mut Value, source: &Arc<PluginSource>)` to attach a shared source.
    *   `verify_source(val: &mut Value, source: &PluginSource) -> Result<(), ShellError>` to verify source origin.

*   Modify `PluginInterface`:
    *   Rename `.state.identity` to `.state.source` with type `Arc<PluginSource>`.

*   Add `PluginOption` enum in protocol:
    *   Include `GcDisabled(bool)` variant.
    *   Ensure `PluginOutput::Option(PluginOption)` survives JSON and msgpack serialization.

*   Handle errors in `consume_all`:
    *   Propagate IO or message errors to subsequent plugin calls without requiring interface drop.

*   Configure plugin garbage collection in nushell config:
    *   Add `plugin_gc` section with `default.enabled` (bool) and `default.stop_after` (duration).
    *   Support per-plugin overrides: `plugin_gc.plugins.<name>.enabled` and `stop_after`.
    *   Reject negative `stop_after` durations with an error message containing 'must not be negative'.

*   Implement `plugin list` command:
    *   Return a list with fields: `name` (string), `is_running` (bool), `pid` (int or null).

*   Implement `plugin stop <name>` command:
    *   Stop a running plugin and update `is_running` to false.
    *   Ensure the process no longer appears in the system process list.

*   Ensure plugin processes remain running after command invocation:
    *   Maintain the same process for multiple commands within a session.
    *   Assign a new pid after stopping and re-invoking a plugin.

*   Ensure custom values remain usable after plugin stops:
    *   Automatically re-spawn the plugin when needed.

*   Implement garbage collection behavior:
    *   Stop plugins after `stop_after` duration of idleness.
    *   Prevent stopping when `plugin_gc.default.enabled` or per-plugin `enabled` is false.
    *   Allow plugins to prevent GC with `PluginOption::GcDisabled(true)`.
    *   Do not stop plugins with active streaming output.

*   Ensure all plugin processes exit when nushell exits.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.