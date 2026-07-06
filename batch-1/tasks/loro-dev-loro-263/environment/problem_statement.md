## Description

The event system in our collaborative document library needs to be redesigned so that a single commit's worth of changes can be delivered as one unified event, rather than requiring separate event objects for each container that changed.

Currently, when multiple containers are modified in a single commit, subscribers receive a flat event object that only directly exposes one container's diff, path, and identity. This makes it hard to correlate changes across containers that happened together. Additionally, document-level metadata — like whether the event came from a time-travel checkout or was generated locally — is mixed in under a property that also holds container-specific data, which is confusing.

## Expected Behavior

- When a commit touches multiple containers, all of those container-level changes should be accessible on a single event object as an ordered collection. Subscribers should be able to iterate over each changed container's diff and path within that one event.
- Document-level metadata (such as whether the event came from a checkout operation or was a local change) should be accessible through a dedicated metadata property on the event, separate from the container-level entries.
- The event type exposed by the library should reflect this batched structure.

## Why This Matters

Without this, subscribers must work around the API to handle multi-container commits consistently. The new shape makes it easy to process all changes from one commit atomically in one callback, and clearly separates document-level context from per-container diffs.
