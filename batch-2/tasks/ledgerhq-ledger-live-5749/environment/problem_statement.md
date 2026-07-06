## Description

The Ledger Live Desktop application uses an internal process to manage communication with hardware devices over USB/HID. Currently, the transport logic that sits between the application's internal process and the renderer process is not cleanly separated into a dedicated, well-defined module. This makes it difficult to maintain, reason about, and test in isolation.

We need a dedicated transport handler module that exposes well-scoped functions for each transport operation: opening a connection to a device, sending a single low-level command, sending a batch of low-level commands, listening for device plug/unplug events, unsubscribing from those events, and closing a device connection.

## Expected Behavior

- Opening a device connection should succeed when a compatible transport module is registered, and return a structured error when none is available.
- Sending a command to a device should return the response as a hex string on success, or a structured disconnection error if no connection exists or the device becomes unavailable.
- Sending a batch of commands should emit one response per command, with individual failures surfaced inline rather than aborting the entire stream.
- Listening for device events should continuously emit structured objects describing each plug or unplug event.
- Unsubscribing from device event listening should immediately stop further events from being emitted and complete the listening stream.
- Closing a device connection should invoke the underlying close routine, confirm success, and prevent any further commands from being sent to that device.

## Why This Matters

Without this module, the transport logic is not independently testable, errors are inconsistently surfaced, and the IPC layer is harder to maintain. A clean, observable-based transport handler makes the internal process more robust and ensures predictable behavior for all hardware device interactions within Ledger Live Desktop.
