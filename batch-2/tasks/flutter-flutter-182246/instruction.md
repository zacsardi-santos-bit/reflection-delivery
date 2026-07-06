Implement a static method to validate Swift Package Manager support in Flutter plugins when building the example app. Ensure the method emits warnings for missing or incomplete support and logs these warnings appropriately, while suppressing them in non-relevant contexts.

*   Implement the `validatePluginSupportsSwiftPackageManager` static method in `packages/flutter_tools/lib/src/macos/darwin_dependency_management.dart`.
    *   Return a non-null warning string containing 'does not have Swift Package Manager support' and `kSwiftPackageManagerDocsUrl` if the plugin supports the platform, has a podspec file, but lacks a `Package.swift` manifest.
    *   Return null if the plugin supports the platform and a `Package.swift` manifest exists containing 'FlutterFramework'.
    *   Return a non-null warning string containing 'is missing a dependency on FlutterFramework' and `kSwiftPackageManagerDocsUrl` if the `Package.swift` exists but lacks 'FlutterFramework'.
    *   Include 'macos' in the warning string for macOS platform checks.
    *   Return null if the plugin does not declare support for the queried platform.
    *   Support plugins using `sharedDarwinSource` by checking the 'darwin' directory and return appropriate warnings for both iOS and macOS.

*   Define and export the constant `kSwiftPackageManagerDocsUrl` in `packages/flutter_tools/lib/src/macos/darwin_dependency_management.dart`.
    *   Set its value to 'https://docs.flutter.dev/packages-and-plugins/swift-package-manager/for-plugin-authors'.

*   Modify the `DarwinDependencyManagement` class in `packages/flutter_tools/lib/src/macos/darwin_dependency_management.dart`.
    *   Extend the `setUp` method to invoke the plugin validation logic.
    *   Log any returned warning unless:
        *   The current project directory path does not end with 'example'.
        *   The parent directory lacks a `pubspec.yaml` file.
        *   The parent `pubspec.yaml` is malformed or cannot be parsed.
        *   The parent `pubspec.yaml` does not declare the project as a Flutter plugin.
        *   The plugin name in the parent `pubspec.yaml` does not match any known plugin.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.