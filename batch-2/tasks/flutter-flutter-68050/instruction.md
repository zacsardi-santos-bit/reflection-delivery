Implement architecture-aware command invocation for Apple's developer tools in Flutter's iOS build tooling to ensure native execution on ARM64 Macs. Update the system to detect the host architecture and adjust tool invocations accordingly. Simplify the detection of available sub-tools and update simulator management to use the correct command prefix.

*   Update the Xcode class:
    *   Implement the `xcrunCommand()` method in `packages/flutter_tools/lib/src/macos/xcode.dart`.
        *   Return `['/usr/bin/arch', '-arm64', 'xcrun']` on ARM64 macOS.
        *   Return `['xcrun']` on x86 macOS or if ARM64 detection fails.
        *   Detect ARM64 by executing `sysctl hw.optional.arm64` and checking for exit code 0 and stdout ending with '1'.
    *   Ensure `Xcode.eulaSigned` uses `xcrunCommand()` for clang invocations.
    *   Ensure `Xcode.sdkLocation()` uses `xcrunCommand()` for `xcrun --sdk` invocations.
    *   Ensure `xcodebuild` invocations in the iOS build pipeline use `xcrunCommand()`.

*   Update the SimControl class:
    *   Modify the constructor in `packages/flutter_tools/lib/src/ios/simulators.dart` to accept a required named `xcode` parameter of type Xcode.
    *   Ensure all `simctl` operations (list, install, uninstall, launch, takeScreenshot, isInstalled, spawn) use `xcode.xcrunCommand()` as the command prefix.

*   Update the XCDevice class:
    *   Modify the `isInstalled` getter in `packages/flutter_tools/lib/src/macos/xcode.dart` to return true only if Xcode is installed and meets version requirements.
    *   Remove the `xcdevicePath` property and associated process call for `xcrun --find xcdevice`.

*   General updates:
    *   Ensure all xcrun-based commands throughout the iOS build system (framework creation, lipo, xcodebuild, simctl operations, xcdevice list/observe) use `xcode.xcrunCommand()` as the command prefix.
    *   Ensure `Xcode.isInstalledAndMeetsVersionCheck` returns false on non-macOS platforms without additional process calls.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.