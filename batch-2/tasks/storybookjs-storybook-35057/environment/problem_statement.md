## Description

Currently, every query and command registered on a service is visible in the service discovery APIs, and every registered service appears in the service registry listing. There is no way to mark certain operations or services as "implementation detail only" — hidden from external consumers browsing the discovery surface, but still usable at runtime by code that already has a direct reference.

This gap is a problem when building helper operations or internal services that are necessary for the system to work but should not be part of the public API. For example, a debug utility or a low-level state mutation command might be invoked internally during a static build pass or by a coupled module, but exposing it in the discovery listing would clutter the public surface and invite unintended usage.

## Expected Behavior

- It should be possible to flag individual operations (queries or commands) as internal. Internal operations must be excluded from service descriptors and from the operation name lists in the service listing.
- It should be possible to flag an entire service as internal. Internal services must be excluded from the service listing but remain reachable through direct lookup by id.
- Internal operations and services must remain fully functional at runtime — they must still be callable through a service handle.
- Internal queries that contribute to static file generation must continue to participate in that build process even when hidden from discovery.

## Why This Matters

Without visibility control, there is no clean separation between the public API surface and internal implementation details. Teams need a lightweight way to add helper operations without leaking them to discovery consumers.
