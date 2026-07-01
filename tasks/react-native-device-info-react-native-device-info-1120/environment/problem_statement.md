## Description

Several device information retrieval functions in this cross-platform mobile library are inconsistent in their platform support and test coverage. Functions that retrieve core device properties (such as device identifier, model, brand, system version, bundle ID, application name, build number, version, and unique identifier) should work uniformly across iOS, Android, and Windows, but they are not returning correct values in all cases.

Additionally, the tablet detection function is returning a hardcoded default value even on platforms where the native module can and should provide the actual answer (Android, iOS, and Windows). The test mock infrastructure is also misconfigured — it sets up some native module properties incorrectly (as function mocks rather than value properties), and the tablet detection mock is initialized to the wrong value. The platform routing mock also does not handle all supported platforms (Windows and web are missing).

## Expected Behavior

- Device property getter functions for device identifier, model, brand, system version, bundle ID, application name, build number, version, and unique identifier should all support iOS, Android, AND Windows, delegating to the native module on each of those platforms.
- The tablet detection function should return the actual native module value on Android, iOS, and Windows, and fall back to a boolean false only on platforms not supported by the native module.
- The test mock setup must accurately reflect the real native module's behavior: property-based getters return string values (not function mocks), and the tablet detection native property is initialized to a truthy value.
- The platform routing utility used by the test harness must handle all four platforms: iOS, Android, Windows, and web.

## Why This Matters

Developers building apps for Windows devices using this library get incorrect placeholder values for important device properties. Tablet detection also doesn't work correctly, which breaks responsive layout decisions. The incorrect mock setup means that tests do not accurately validate these behaviors, leading to bugs slipping through.
