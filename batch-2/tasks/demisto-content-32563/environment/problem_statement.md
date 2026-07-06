## Description

The Trend Micro Vision One V3 integration needs to be updated to align with a newer version of the underlying API client library. The library has reorganized its client methods into logical namespaces (e.g., account-related operations, endpoint-related operations, email-related operations), and several response model classes have been renamed. The integration currently uses the old flat method names and old class names, which no longer work with the updated library.

Additionally, the integration is missing support for managing custom scripts — a feature available through the platform's Response Management interface. Users should be able to add, update, delete, list, run, and download custom scripts through the integration.

## Expected Behavior

- All existing commands (enable/disable account, isolate endpoint, quarantine email, sandbox operations, etc.) must work with the new namespaced client API methods.
- The renamed response model classes from the API library must be used in place of the old names throughout the integration.
- Six new commands must be added:
  - **Run custom script**: Execute a named script against one or more endpoints (by hostname or agent ID), returning a task status and task ID per endpoint.
  - **List custom scripts**: Retrieve available scripts filtered optionally by filename and/or file type.
  - **Download custom script**: Fetch the text content of a script by its ID.
  - **Add custom script**: Upload a new script (bash or PowerShell) with a name, type, and optional description, returning the new script's ID.
  - **Update custom script**: Replace the content or metadata of an existing script by its ID.
  - **Delete custom script**: Remove a script by its ID and return a success status.

## Why This Matters

Without this update, the integration breaks entirely because the old method names no longer exist in the new library version. The new custom script commands also unlock automation workflows that previously required manual platform access.
