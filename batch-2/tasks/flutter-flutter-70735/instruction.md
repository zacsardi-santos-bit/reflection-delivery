Refactor the CocoaPods integration in the Flutter build system to improve how pod installations handle the Flutter engine framework directory. Ensure that the system computes necessary paths internally and enforces updated project configurations.

*   Update the CocoaPods class:
    *   Modify the constructor to accept a required Artifacts parameter, alongside fileSystem, processManager, xcodeProjectInterpreter, logger, and platform.
    *   Change the processPods method to replace the engineDir parameter with a buildMode parameter of type BuildMode. The method signature should be: `processPods({@required XcodeBasedProject xcodeProject, @required BuildMode buildMode, bool dependenciesChanged = true}) -> Future<bool>`.

*   Implement environment handling for pod installations:
    *   For iOS projects, ensure the environment map passed to the pod install command does not include a FLUTTER_FRAMEWORK_DIR key. Only include COCOAPODS_DISABLE_STATS, LANG, and optionally CP_REPOS_DIR.
    *   For macOS projects, compute the FLUTTER_FRAMEWORK_DIR from the Artifacts instance and the given build mode, and include it in the environment map.

*   Enforce updated Podfile patterns:
    *   If a Podfile creates a Flutter engine symlink, throw a ToolExit with the message 'Podfile is out of date'.
    *   If a Podfile parses the old .flutter-plugins file format, throw a ToolExit with the message 'Podfile is out of date'.

*   Adjust Xcode build configurations:
    *   Ensure the macOS Xcode build settings include FLUTTER_FRAMEWORK_DIR, computed from the current build mode and Artifacts instance, for non-module projects.
    *   Ensure the iOS Xcode build configuration does not contain FLUTTER_FRAMEWORK_DIR.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.