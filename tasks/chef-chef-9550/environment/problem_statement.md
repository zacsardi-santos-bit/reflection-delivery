## Description

The macOS build tools resource currently relies on an external system binary to detect whether the Xcode Command Line Tools are already installed. This approach fails in environments where the binary is not available (e.g., Linux CI runners, containers) and can produce incorrect results on some macOS setups. The detection logic should be updated to instead read from the operating system's built-in installation history file, which is a more reliable and portable approach.

Additionally, there is no existing way to query the software update service to determine what Command Line Tools package is currently available for installation or upgrade. This capability is needed to support both initial installation and future upgrade workflows.

## Expected Behavior

- Checking whether the Xcode Command Line Tools are installed should work by reading the system's installation history file rather than invoking an external command.
- A new capability should be available to query the software update service and return the label of the available Command Line Tools package.
- The software update query should return the appropriate label string on both older macOS releases (pre-10.15) and newer releases (10.15+), since these versions use different output formats.
- When no Command Line Tools package is listed as available, the query should return nothing.

## Why This Matters

The current detection mechanism is fragile and breaks in non-macOS environments used for testing. Switching to the installation history file makes the check self-contained and reliable. Adding the ability to look up the available package label is a prerequisite for implementing automated upgrade support for Xcode Command Line Tools in the build tools resource.
