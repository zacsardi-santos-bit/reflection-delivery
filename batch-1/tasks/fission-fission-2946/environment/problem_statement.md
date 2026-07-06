## Description

The message queue trigger manager component in Fission lacks unit tests for its core subscription lifecycle operations. Currently there is no automated way to verify that the manager correctly tracks which triggers have active subscriptions, returns the correct subscription data when queried, or properly removes subscriptions when triggers are deleted.

## Expected Behavior

- A newly created manager should report that no subscriptions are active for any trigger
- After registering a subscription, the manager should confirm the trigger is present and return the correct subscription details including the trigger's identity
- After removing a subscription, the manager should confirm the trigger is no longer active
- All subscription management operations (add, check, retrieve, delete) should complete without errors under normal conditions

## Why This Matters

Without unit tests covering these core operations, regressions in the subscription lifecycle can go undetected. Developers making changes to the trigger management logic have no safety net to confirm that the fundamental operations — registering a new subscription, checking if a trigger is already subscribed, retrieving a subscription, and removing a subscription — behave correctly. Adding this coverage makes the component more maintainable and reliable.
