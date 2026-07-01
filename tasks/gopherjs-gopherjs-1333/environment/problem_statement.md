## Description

GopherJS has native overlay files that replace or supplement standard library packages when compiling Go code to JavaScript. When the Go standard library reorganized its internal elliptic curve cryptography packages into a new location in a recent release, the GopherJS native overlays for those packages were left pointing at the old path. This causes compilation to fail because the old paths no longer exist in the updated standard library.

Additionally, several tests in those packages rely heavily on 64-bit arithmetic, which is significantly slower when emulated under GopherJS. There is currently no mechanism to automatically cap the number of property-based test iterations without disabling the tests entirely — the only option was to skip the test outright.

## Expected Behavior

- The GopherJS native overlay files for the elliptic curve cryptography packages should be moved to match the current standard library layout.
- Old native overlay files at the outdated paths should be removed to avoid conflicts.
- A new mechanism should be added to the GopherJS-specific property-based testing support that allows a test override to set an upper bound on the number of test iterations. This allows slow tests to run with a reduced iteration count rather than being skipped entirely.
- The cryptographic field arithmetic package should compile successfully under GopherJS once the overlays are in the correct location and the new iteration-cap mechanism is available.

## Why This Matters

Developers using GopherJS with a newer version of Go currently cannot compile or test code that depends on internal elliptic curve packages because the GopherJS compatibility layer is out of date. Fixing the overlay paths and adding the iteration-cap utility restores the ability to compile and run these tests under GopherJS without either breaking the build or waiting unreasonably long for tests to complete.
