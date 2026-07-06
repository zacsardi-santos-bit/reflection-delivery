Implement a migration for macOS Flutter projects to update the framework embedding process in Xcode project files. Remove manual framework link and embedding entries and update the build shell script to use the assembly tool with the embed command. Ensure the migration skips already updated projects and handles non-existent project files gracefully. Report errors and send analytics events for unrecognized leftover framework references.

*   Create the `RemoveMacOSFrameworkLinkAndEmbeddingMigration` class in `packages/flutter_tools/src/macos/migrations/remove_macos_framework_link_and_embedding_migration.dart`.
    *   Extend `ProjectMigrator` from `packages/flutter_tools/src/base/project_migrator.dart`.
    *   Accept `MacOSProject`, `Logger`, and `Usage` as constructor arguments.
    *   Implement `migrate()` method:
        *   Return `true` and log 'Xcode project not found, skipping framework link and embedding migration' if `xcodeProjectInfoFile` does not exist.
        *   Return `true` without modification if `xcodeProjectInfoFile` contains no matching migration patterns.
        *   Return `true` without modification if the shell script already includes 'macos_assemble.sh embed'.
        *   Remove lines containing GUIDs: `D73912F022F37F9E000D13A0`, `D73912F222F3801D000D13A0`, `D73912EF22F37F9E000D13A0`, `33D1A10422148B71006C7A3E`, `33D1A10522148B93006C7A3E`.
        *   Update shell script line to append ' && "$FLUTTER_ROOT"/packages/flutter_tools/bin/macos_assemble.sh embed\n'.
        *   Log 'Upgrading project.pbxproj' on successful migration.
        *   Throw `ToolExit` with message 'Your Xcode project requires migration' if leftover `App.framework` or `FlutterMacOS.framework` references are detected.
        *   Send a usage analytics event with category 'macos-migration', action 'remove-frameworks', label 'failure', value null on failure.
*   Update Xcode project files:
    *   Remove manual `App.framework` and `FlutterMacOS.framework` entries from:
        *   `dev/integration_tests/flutter_gallery/macos/Runner.xcodeproj/project.pbxproj`
        *   `dev/integration_tests/ui/macos/Runner.xcodeproj/project.pbxproj`
        *   `dev/manual_tests/macos/Runner.xcodeproj/project.pbxproj`
    *   Update their shell scripts to include the embed command.
*   Modify CocoaPods configuration:
    *   Remove 'install! cocoapods, :disable_input_output_paths => true' from `dev/integration_tests/flutter_gallery/macos/Podfile`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.