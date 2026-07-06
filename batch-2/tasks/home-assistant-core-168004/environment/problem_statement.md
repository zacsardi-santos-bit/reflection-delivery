## Description

The Elgato integration currently shows misleading and inconsistent error messages when operations on Elgato devices fail. Specifically, all error messages refer to "Elgato Light" even when the failing entity is a button or a switch — not a light at all. Additionally, there is no distinction between a connectivity failure (e.g., the device is unreachable on the network) and an unexpected/unknown error, so users cannot tell what kind of problem occurred.

## Expected Behavior

- Error messages should refer to "Elgato device" rather than "Elgato Light" across all entity types (buttons, lights, and switches)
- Connection-related failures should produce a specific message indicating that an error occurred while communicating with the Elgato device
- Unknown or unexpected errors should produce a different, clearly labeled message indicating that an unknown error occurred while communicating with the Elgato device
- Button presses, light control operations, switch operations, and identify actions should all follow this two-tier error handling

## Why This Matters

Users interacting with Elgato buttons and switches see confusing error messages that mention "Light" even when no light is involved. More importantly, having a single catch-all error message makes it harder to diagnose whether an issue is a connectivity problem or something else. Splitting the error handling makes the integration more informative and accurate for all device types.
