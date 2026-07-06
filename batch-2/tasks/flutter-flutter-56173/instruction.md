Implement support for Microsoft Edge as a web target device in Flutter tooling. Ensure that Edge is detected and listed as an available web device on Windows systems with a compatible Chromium-based version. Exclude Edge from the device list on non-Windows platforms and if the installed version is not supported.

*   Update the `MicrosoftEdgeDevice` class:
    *   Accept a required named parameter `processManager` of type `ProcessManager` in the constructor.
    *   Ensure the `name` property returns the string 'Edge'.

*   Modify the `WebDevices` class:
    *   Implement the `pollingGetDevices()` method to conditionally include `MicrosoftEdgeDevice` based on platform and installed Edge version.
    *   On non-Windows platforms (Linux or macOS):
        *   Ensure `pollingGetDevices()` does not include any `MicrosoftEdgeDevice` instances.
    *   On Windows:
        *   Invoke the registry query command `reg query HKEY_CURRENT_USER\Software\Microsoft\Edge\BLBeacon /v version` to determine the installed Edge version.
            *   Parse the registry output to extract the version number.
        *   Include `MicrosoftEdgeDevice` in the return list if:
            *   The Edge version is at or above the minimum supported major version (version 83.x).
            *   Edge can be run (the process manager confirms it is runnable).
        *   Exclude `MicrosoftEdgeDevice` if:
            *   The Edge version is below the minimum supported major version (e.g., version 72.x).
            *   Edge cannot be run.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.