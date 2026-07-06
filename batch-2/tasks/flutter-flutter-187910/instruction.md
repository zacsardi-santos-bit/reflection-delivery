I'm working on Flutter's Xcode integration tooling and need to update how it locates the simulator or device management application within an Xcode installation.

*   The `getSimulatorPath()` method on the `Xcode` class must first check for a DeviceHub.app directory at `{parent_of_xcodeSelectPath}/Applications/DeviceHub.app` (i.e., one level up from the xcode-select path, then into Applications/DeviceHub.app), and return that path if the directory exists.

*   If DeviceHub.app does not exist at the above location, `getSimulatorPath()` must fall back to checking for Simulator.app at `{xcodeSelectPath}/Applications/Simulator.app` and return that path if the directory exists.

*   If neither DeviceHub.app nor Simulator.app directories exist, `getSimulatorPath()` must return null.

*   If the xcode-select command is unavailable or throws a process exception, `getSimulatorPath()` must return null.


*   Interface details: Type: Method
Name: getSimulatorPath
Location: packages/flutter_tools/lib/src/macos/xcode.dart
Signature: String? getSimulatorPath()
Description: Returns the path to the simulator or device management application within the Xcode installation. Checks for DeviceHub.app at dirname(xcodeSelectPath)/Applications/DeviceHub.app first; falls back to Simulator.app at xcodeSelectPath/Applications/Simulator.app. Returns null if neither exists or if the Xcode select path is unavailable.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.