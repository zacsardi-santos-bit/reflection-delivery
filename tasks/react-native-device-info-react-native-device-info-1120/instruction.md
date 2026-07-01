Update the cross-platform mobile library to ensure consistent support for iOS, Android, and Windows across core device property getters and tablet detection. Correct the test mock configuration to reflect accurate native module behavior and platform support.

*   Implement the following functions in `src/index.ts` to support iOS, Android, and Windows:
    *   `getDeviceId()`, `getModel()`, `getBrand()`, `getSystemVersion()`, `getBundleId()`, `getApplicationName()`, `getBuildNumber()`, `getVersion()`, `getUniqueId()`
        *   Return native module property values on supported platforms.
        *   Return 'unknown' on unsupported platforms.
    *   `getDeviceType()` and `getDeviceTypeSync()`
        *   Return native module's `deviceType` property on iOS and Android.
        *   Return 'unknown' on unsupported platforms.
    *   `isTablet()`
        *   Return native module's `isTablet` value on Android, iOS, and Windows.
        *   Return `false` on unsupported platforms.
    *   `getMacAddress()` and `getMacAddressSync()`
        *   Return '02:00:00:00:00:00' on iOS without calling the native module.
        *   Call native module on non-iOS supported platforms.
        *   Return 'unknown' on unsupported platforms.
    *   `getIpAddress()`, `getIpAddressSync()`, `getPhoneNumber()`, `getPhoneNumberSync()`, `getCarrier()`, `getCarrierSync()`
        *   Call native module on supported platforms.
        *   Return 'unknown' on unsupported platforms.
    *   `getManufacturer()` and `getManufacturerSync()`
        *   Return 'Apple' on iOS without calling the native module.
        *   Call native module on Android.
        *   Use memoization.
    *   `getDeviceToken()`
        *   Call native module on iOS.
        *   Return 'unknown' on Android, Windows, and web.
    *   `getSystemAvailableFeatures()` and `getSystemAvailableFeaturesSync()`
        *   Call native module on supported platforms.
        *   Return an empty array on unsupported platforms.

*   Update `jest.setup.ts` for test mock configuration:
    *   Configure native module string property keys, including `deviceType`, as static property values returning 'unknown-test'.
    *   Remove `deviceType` from `stringFnNames` array.
    *   Set `isTablet` boolean key to `true`.
    *   Update `Platform.select` mock to handle 'windows' and 'web' platforms.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.