I'm working on the Cypress installer CLI and we have an issue with architecture detection on Apple Silicon Macs. When someone runs Cypress installation using an Intel Node.js environment under Rosetta emulation, the installer reads the Node.js process's reported architecture instead of the real system hardware architecture. This causes it to download the x64 Cypress binary rather than the native ARM64 binary that the system actually needs.

I need the installer to detect whether the current process is being translated by Apple's compatibility layer, and if so, treat the system as ARM64 and download the corresponding binary. On a native ARM64 setup, the ARM64 binary should also be downloaded, as you'd expect.

The functions that build download URLs currently detect architecture themselves internally. They should be updated to accept the architecture as an explicit parameter instead, so that the architecture detection logic is centralized and the detected value flows through consistently.

There also needs to be a way to cache the detected architecture so we're not re-detecting it on every call, and the cache should be clearable (for example, between test runs).

The test support utilities for mocking spawned child processes also need to be updated — the mock child process objects need to include an additional method stub required by the process spawning library, in addition to the existing stubs.
