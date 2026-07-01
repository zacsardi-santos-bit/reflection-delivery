## Description

When installing Cypress on an Apple Silicon Mac (M1/M2), the installer may download the wrong binary architecture. Specifically, if a developer is running an Intel-architecture Node.js environment under Rosetta emulation, the installer detects the Node.js process's reported architecture (x64) instead of the actual system hardware architecture (arm64), and proceeds to download the x64 Cypress binary. The native ARM64 binary should be downloaded instead.

## Expected Behavior

- When installing on an Apple Silicon Mac running a native ARM64 Node.js binary, the ARM64 Cypress binary should be downloaded.
- When installing on an Apple Silicon Mac running an Intel (x64) Node.js binary under Rosetta emulation, the installer should detect the Rosetta translation layer and still download the ARM64 Cypress binary.
- The functions responsible for constructing download URLs should accept architecture as an explicit input parameter rather than detecting it internally, so the detected architecture is used consistently throughout the download process.

## Why This Matters

Developers on Apple Silicon Macs who use Rosetta-emulated Node.js environments currently end up with the wrong Cypress binary. This can cause compatibility issues or prevent Cypress from running correctly. The installer should always download the binary that matches the actual hardware, not just the Node.js process's reported architecture.
