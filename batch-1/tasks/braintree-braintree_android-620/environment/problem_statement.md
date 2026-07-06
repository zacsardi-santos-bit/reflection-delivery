## Description

The SDK's internal encrypted storage layer can silently fail on certain devices — for example when the device security subsystem reports an unexpected error or when the encrypted storage file becomes inaccessible. When this happens today, all read operations return default values and all write operations are silently dropped, giving callers no way to tell the difference between "nothing has been stored yet" and "the storage system is broken."

This silent swallowing of errors has downstream consequences: the Venmo payment flow may persist vault preferences without knowing the write failed, configuration caching may appear to load correctly when it is actually falling back to a network fetch, and there is no analytics signal to indicate these storage failures are occurring in production.

## Expected Behavior

- When the encrypted storage layer fails (either during initial setup or during individual read/write operations), a specific, typed exception should be thrown to callers rather than silently returning a default value.
- Components that interact with storage — such as the configuration cache and the Venmo vault preference system — should propagate these exceptions to their callers so that failures can be handled explicitly.
- The Venmo payment flow should report storage failures to the result listener via the existing error callback and should emit an analytics event to track the failure.
- The configuration loading path should continue to return a valid configuration fetched from the network even when the local cache is inaccessible, but should emit analytics events to track both cache-read and cache-write failures.
- The Android context object should no longer need to be passed into individual storage read/write calls; it should be provided once at construction time.

## Why This Matters

Users on certain devices with security or keystore issues may silently experience broken Venmo flows or repeatedly fetch configuration from the network because the local cache is inaccessible. Without visible errors and analytics, these failures are invisible in production monitoring and cannot be diagnosed or fixed.
