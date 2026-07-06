Implement an interactive device selection mechanism for the Flutter tool when multiple devices are connected and no specific target device is specified. Ensure the tool prompts the user to choose a device only in interactive terminal sessions, while maintaining existing behavior for automatic selection when only one ephemeral device is present.

*   Modify the `findTargetDevices` method in `packages/flutter_tools/lib/src/device.dart`:
    *   Automatically return a single ephemeral device if it is the only one among multiple devices.
    *   If multiple devices remain and `globals.stdio.stdinHasTerminal` is true, invoke `globals.terminal.promptForCharInput`.
        *   Pass a list of string indices (e.g., ['0', '1'] for two devices) as valid inputs.
        *   Use `globals.logger` as the logger argument.
        *   Use `globals.userMessages.flutterChooseOne` as the prompt argument.
    *   Return a single-element list containing the device at the index corresponding to the user's input.

*   Update the `UserMessages` class in `packages/flutter_tools/lib/src/base/user_messages.dart`:
    *   Implement the `flutterChooseOne` getter to return "Please choose one:".
    *   Implement the `flutterMultipleDevicesFound` getter to return "Multiple devices found:".
    *   Implement the `flutterChooseDevice` method to format and return a string for a device option using the provided index, device name, and device ID.

*   Ensure the interactive prompt does not appear in CI or non-interactive environments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.