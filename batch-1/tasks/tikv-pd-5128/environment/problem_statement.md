## Description

The distributed GC safe point system currently only tracks a single global safe point value. However, in practice, multiple services (such as backup, change-data-capture, and the primary GC worker) each have their own minimum data boundary — the oldest point in time they need data preserved up to. There is no mechanism to track per-service safe points or to enforce which service holds the current overall minimum.

## Expected Behavior

- Each service should be able to register and update its own GC safe point independently.
- The system should track all registered services and expose which one currently holds the minimum safe point.
- A special reserved service identifier used for the core GC worker must always be registered with a permanent (non-expiring) duration. Attempting to register it with a finite duration should be rejected with an error.
- Registering a safe point with a negative duration should be treated as a request to remove the service from tracking (no error, but the update is not applied).
- Attempting to register a safe point value below the current system-wide minimum should be silently rejected (no error, update not applied), since data below the minimum may already have been collected.
- When no services have registered yet, the core GC worker should be automatically seeded with a safe point of zero to serve as a floor.
- The manager used for service-level safe point operations should be accessible from outside its package (i.e., its constructor should be exported).

## Why This Matters

Without per-service safe point tracking, services cannot safely coordinate garbage collection boundaries. A backup job or CDC pipeline could lose data if GC advances past the point they need. This feature ensures GC only advances past the minimum safe point reported by all active services.
