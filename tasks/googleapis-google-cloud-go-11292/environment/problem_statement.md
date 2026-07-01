## Description

The Google Cloud Pub/Sub Go client library needs to be properly released as a new major version. Currently, code inside the v2 module directory still references the original, unversioned package import paths for its internal packages (the low-level API client, protobuf types, and test server). This means the v2 module cannot compile as a self-contained, independent unit — it still depends on the v1 package paths, which defeats the purpose of having a separate major version.

## Expected Behavior

- All code within the v2 module should import from the versioned v2 package paths, not from the original unversioned paths.
- The sub-packages for the low-level API client, the protobuf types, and the test server should all be available under the v2 module path.
- The call options type for topic administration (used for configuring publish retry behavior, among other things) should be named to reflect its role in topic administration rather than just publishing.
- Tracing and telemetry instrumentation emitted by the library should identify itself using the v2 module's name, not the original module name.

## Why This Matters

Without these fixes, Go developers cannot use the v2 module in isolation. Any project importing the v2 path will encounter compilation failures because the internal dependencies resolve to the wrong packages. Having the correct versioned module path also ensures that tracing and observability data accurately identifies which version of the library is being used.
