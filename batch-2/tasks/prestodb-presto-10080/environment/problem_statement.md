## Description

The Raptor connector currently has no access control mechanism — every user can read and write all data without restriction. We need to add configurable security support so that administrators can enforce appropriate access policies for their deployment.

## Expected Behavior

- The connector should support a configuration property that selects one of three security modes:
  - **Allow all** (default): the current behavior, now made explicit — all users can do anything.
  - **Read-only**: all users are restricted to read operations only; any attempt to write (e.g., create a table) is rejected with an access-denied error.
  - **File-based**: per-user permissions are loaded from an external JSON rules file. Users listed with read privileges can query tables; users not granted access are rejected with an appropriate access-denied error.

- The file-based rules format should support defining which users have specific table privileges (such as read access) and which users own schemas.

- When no security mode is specified, the connector must default to allowing all access so that existing deployments are not broken.

## Why This Matters

Without access control, any user with connector access can read or modify all data stored in the Raptor connector. Adding security modes allows operators to enforce data protection boundaries — for example, making a catalog read-only to prevent accidental writes, or restricting sensitive tables to specific users via a rules file.
