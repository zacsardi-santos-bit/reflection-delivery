## Description

The capability consistence migration for Cadence 1.0 is not correctly handling account link capabilities that use plain, unauthorized account reference types. In the old Cadence system, it was possible to create account link capabilities where the borrow type was a basic, non-privileged account reference (no specific entitlements). During migration to the new capability controller system, these unauthorized account reference capabilities are not being processed correctly — either failing or producing the wrong output.

Additionally, there is a bug in how migration events are emitted for fully authorized account capabilities: the type string in the event has a malformed format with a doubled reference prefix, which is incorrect.

## Expected Behavior

- When migrating an account link capability that uses a plain (non-privileged) account reference type, the migration should succeed and issue an account capability controller with the fully authorized account type, regardless of whether the original capability was unauthorized or fully authorized.
- When emitting migration events for account capability controllers, the type string must be correctly formatted without a doubled reference prefix.
- Both public and private path domain account links should be handled correctly for the unauthorized account reference case.
- For chains of links where the inner link is an account link and the outer link uses an unauthorized account reference, both links should be migrated and each should emit events with the correct type information.

## Why This Matters

If the migration doesn't handle unauthorized account reference capabilities, state on-chain from before Cadence 1.0 that uses this pattern cannot be migrated. The malformed event type string also indicates that the type information in the new capability controller is incorrect, which could cause downstream issues.
