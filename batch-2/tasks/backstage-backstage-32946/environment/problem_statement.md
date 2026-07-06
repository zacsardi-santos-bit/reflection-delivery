## Description

The catalog backend handles events from source control systems — when files are moved, deleted, or renamed in a repository, it updates the catalog's entity registrations accordingly. However, there is currently no way to observe or measure how many catalog operations are being triggered by these repository events. We have no visibility into how often entities are being added, removed, or moved as a result of SCM activity.

## Expected Behavior

- The SCM events service should accept an external metrics provider so it can report telemetry about its activity.
- The service should expose a way to record that a specific type of action was taken in response to an SCM event (e.g., a deletion, creation, or move of a catalog registration).
- When the catalog processes an SCM event and takes action — such as removing an entity registration because a file was deleted, creating a new registration because a file was moved, or updating registrations after a repository rename — a counter should be incremented to record what happened.
- The metrics produced should include the action type (e.g., 'delete', 'create', 'move') so that platform operators can distinguish between different kinds of catalog changes triggered by repository events.

## Why This Matters

Without this observability, platform operators have no way to monitor the rate or type of catalog operations being triggered by repository events. Adding metrics here enables better operational visibility, anomaly detection, and understanding of how SCM activity affects the catalog.
