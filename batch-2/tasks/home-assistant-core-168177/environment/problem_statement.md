## Description

The vacuum clean-area service silently does nothing when it is called with areas that have no segment mappings configured — no error is raised and no feedback is provided to the caller. This makes it very hard to diagnose configuration problems: from the outside, a successful no-op looks identical to a successful cleaning.

## Expected Behavior

- When the clean-area service is called with one or more area IDs that cannot be matched to vacuum segments across any of the targeted vacuum devices, the service should raise a validation error that clearly identifies which areas were not mapped.
- The validation error should include the names/IDs of the unmapped areas in its details so that the caller can understand exactly what went wrong.
- If only some of the requested areas lack mappings, the service should still clean any areas that ARE properly mapped on the relevant devices — a partial configuration should not block the entire operation.
- The internal method responsible for executing area cleaning on a set of vacuum entities should be refactored to accept a list of entities and the full service call object, rather than operating on a single entity instance.

## Why This Matters

Without an error, automations and scripts that call the clean-area service with a misconfigured area silently fail — users have no way to know whether rooms were not cleaned because the vacuum was already done or because the mapping was never set up. Clear error reporting makes the service much easier to use and troubleshoot.
