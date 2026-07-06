Update the Braintree Android SDK to improve error handling in the encrypted storage layer. Implement a mechanism to throw specific exceptions when storage operations fail, rather than silently returning default values or dropping writes. Ensure that these exceptions are propagated properly through the system, and remove the need for passing the Android context object in individual storage operations.

*   Implement a new exception class:
    *   Create `BraintreeSharedPreferencesException` in `com.braintreepayments.api`.
    *   Extend `Exception` and ensure it is constructable with a `String` message and optionally a `Throwable` cause.

*   Update `BraintreeSharedPreferences`:
    *   Support two constructors: one with a `SharedPreferences` instance, and one with a `BraintreeSharedPreferencesException`.
    *   Remove the `Context` parameter from all data-access methods and declare them to throw `BraintreeSharedPreferencesException`.
    *   Wrap and rethrow `SecurityException` as `BraintreeSharedPreferencesException`.
    *   Ensure `getString(String key, String fallback)` returns the fallback value if the key is absent.
    *   Ensure `getBoolean(String key)` returns `false` by default.
    *   Ensure `getLong(String key)` returns `0L` by default.
    *   Implement `clearSharedPreferences()` to clear all stored keys and values without a `Context` parameter.

*   Modify `ConfigurationCache`:
    *   Remove the `Context` parameter from `saveConfiguration` and `getConfiguration`.
    *   Declare these methods to throw `BraintreeSharedPreferencesException`.

*   Create a new interface and class for configuration loading:
    *   Define `ConfigurationLoaderCallback` in `com.braintreepayments.api` with `void onResult(@Nullable ConfigurationLoaderResult result, @Nullable Exception error)`.
    *   Implement `ConfigurationLoaderResult` in `com.braintreepayments.api` with methods to get the configuration and any cache errors.

*   Update `ConfigurationLoader`:
    *   Remove the `Context` parameter from `loadConfiguration`.
    *   Accept `Authorization` and `ConfigurationLoaderCallback`.
    *   Use `ConfigurationLoaderResult` to pass cache load/save errors without failing the operation.

*   Modify `UUIDHelper`:
    *   Remove the `Context` parameter from `getInstallationGUID`.
    *   Accept `BraintreeSharedPreferences` directly and return a new non-null UUID if operations fail.

*   Update `VenmoSharedPrefsWriter`:
    *   Implement a no-argument constructor.
    *   Modify `persistVenmoVaultOption` and `getVenmoVaultOption` to accept `BraintreeSharedPreferences` directly.
    *   Use the key `"com.braintreepayments.api.Venmo.VAULT_VENMO_KEY"` for preferences.

*   Handle Venmo vault option failures:
    *   Forward `BraintreeSharedPreferencesException` to the listener via `onVenmoFailure`.
    *   Emit an analytics event ending with `"pay-with-venmo.shared-prefs.failure"`.

*   Update `BraintreeClient`:
    *   Forward the `Configuration` object to `ConfigurationCallback` on success.
    *   Emit analytics events `"configuration.cache.load.failed"` and `"configuration.cache.save.failed"` for cache errors.
    *   Forward null and the error to `ConfigurationCallback` if the loader fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.