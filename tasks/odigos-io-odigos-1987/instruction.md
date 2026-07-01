Implement support for musl-based containers in the instrumentation system by adding a C library type concept. Update the naming functions to optionally incorporate the C library variant into generated names, ensuring correct handling of musl-prefixed names.

*   Define a new type `LibCType` in `common/libc_types.go`:
    *   Implement `type LibCType string`.
    *   Export constants `Glibc` with value `"glibc"` and `Musl` with value `"musl"`.

*   Update the `InstrumentationPluginName` function in `common/instrumentationdevice.go`:
    *   Modify the signature to `InstrumentationPluginName(language ProgrammingLanguage, otelSdk OtelSdk, libc *LibCType) string`.
    *   When `libc` is nil or points to `Glibc`, return `"{language}-{sdkType}-{sdkTier}"`.
    *   When `libc` points to `Musl`, return `"musl-{language}-{sdkType}-{sdkTier}"`.

*   Update the `InstrumentationDeviceName` function in `common/instrumentationdevice.go`:
    *   Modify the signature to `InstrumentationDeviceName(language ProgrammingLanguage, otelSdk OtelSdk, libc *LibCType) OdigosInstrumentationDevice`.
    *   Pass `libc` to `InstrumentationPluginName`.
    *   When `libc` is nil or points to `Glibc`, return the unchanged device name.
    *   When `libc` points to `Musl`, include the musl prefix in the device name.

*   Update the `InstrumentationDeviceNameToComponents` function in `common/instrumentationdevice.go`:
    *   Ensure it correctly parses device names with a `"musl-"` prefix.
    *   Return the correct `ProgrammingLanguage` and `OtelSdk` values, ignoring the musl prefix.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.