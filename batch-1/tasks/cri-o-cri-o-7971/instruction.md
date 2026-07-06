Upgrade the Container Device Interface (CDI) library to the latest version and update the device injection code to use the new package-level API. Ensure that configuration and refresh operations are handled separately with independent error handling.

*   Update the CDI library dependency:
    *   Modify `go.mod` to change `tags.cncf.io/container-device-interface` from v0.6.2 to v0.7.2.
    *   Modify `go.mod` to change `tags.cncf.io/container-device-interface/specs-go` from v0.6.0 to v0.7.0.
    *   Update `go.sum` to reflect the new CDI library checksums for v0.7.2 and v0.7.0, replacing the v0.6.x entries.

*   Update the device injection implementation:
    *   In `internal/factory/container/device_linux.go`, modify the `specInjectCDIDevices()` function.
    *   Replace the old registry-based pattern:
        *   Remove `cdi.GetRegistry()` and any subsequent calls to `registry.Refresh()` and `registry.InjectDevices(...)`.
    *   Implement direct calls to the new package-level functions:
        *   Use `cdi.Refresh()` and `cdi.InjectDevices(...)` directly.
    *   Ensure that configuration and refresh operations are separate:
        *   Call the configuration function with spec directory options.
        *   Call the refresh function independently.
        *   Handle errors from each operation separately.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.