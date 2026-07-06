Update the vendored OpenTelemetry HTTP tracing library to version 0.45.0 to ensure consistency with other OpenTelemetry components in the project. Modify the vendor directory, module manifest, and checksums to reflect this update. Implement a version verification check to prevent future accidental downgrades.

*   Update the vendored OpenTelemetry HTTP trace instrumentation library:
    *   Ensure the `Version()` function in `vendor/go.opentelemetry.io/contrib/instrumentation/net/http/httptrace/otelhttptrace/version.go` returns "0.45.0".
    *   Modify the vendor dependency manifest (`vendor.mod`) to declare the OpenTelemetry HTTP trace instrumentation package at version v0.45.0.
    *   Ensure the vendor directory contents for the OpenTelemetry HTTP trace instrumentation package match the v0.45.0 release.
        *   Update any source files and ensure the version string in `version.go` is correct.

*   Implement a version verification check:
    *   Add a mechanism to verify that the library reports the expected version number to catch any accidental regressions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.