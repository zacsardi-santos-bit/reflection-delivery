## Description

The Aladdin Connect garage door integration does not currently support diagnostics, which is a standard Home Assistant feature that lets users and developers inspect the live state of connected devices for troubleshooting purposes. Without diagnostics support, it's much harder to debug integration issues without accessing raw logs.

## Expected Behavior

- The integration should expose a diagnostics report accessible through the standard Home Assistant diagnostics interface.
- The diagnostics report should include the current configuration entry details, with any sensitive authentication tokens automatically masked so they do not appear in plain text.
- The report should also include the current state of all registered garage doors, with each door identified by its unique device and door number combination, and reporting its name, open/close status, connection link status, and battery level.

## Why This Matters

Diagnostics support makes it possible for users to share troubleshooting information with developers or support staff without the risk of exposing sensitive credentials. It also makes it much easier to inspect the real-time state of garage door devices without manually digging through logs or using developer tools.
