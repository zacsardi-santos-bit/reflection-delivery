Implement a mechanism to automatically select Dart-only Flutter plugins as their own default implementation for desktop platforms, based on their declared minimum Flutter SDK version. Ensure plugins with a version of 2.11 or later are auto-selected, while older or unspecified versions maintain current behavior.

*   Update the `Plugin` class in `packages/flutter_tools/lib/src/plugins.dart`:
    *   Add a new nullable field `flutterConstraint` of type `VersionConstraint?` from the `pub_semver` package.
    *   Modify the constructor to accept `flutterConstraint` as an optional named or positional parameter.

*   Modify the `Plugin.fromYaml` static method:
    *   Accept a new nullable `flutterConstraint` parameter as the 4th positional argument.
    *   Ensure this parameter is inserted between `pluginYaml` and `dependencies`.
    *   Allow `null` to be passed, indicating no version constraint is declared.
    *   Pass the `flutterConstraint` to the `Plugin` constructor.

*   Update the `resolvePlatformImplementation` function in `packages/flutter_tools/lib/src/flutter_plugins.dart`:
    *   Ensure plugins with `flutterConstraint` set to `null` are not selected as their own default inline implementation for desktop platforms (linux, macos, windows). The resolution list should return 0 entries for such plugins.
    *   Ensure plugins with a `flutterConstraint` minimum version below 2.11.0 are not selected as their own default inline implementation for desktop platforms. The resolution list should return 0 entries for these plugins.
    *   Ensure plugins with a `flutterConstraint` minimum version of at least 2.11.0 are selected as their own default inline implementation. The resolution list should return one entry per desktop platform declared by the plugin.
    *   Ensure each `PluginInterfaceResolution` returned includes a map with keys `pluginName`, `dartClass`, and `platform` via the `toMap()` method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.