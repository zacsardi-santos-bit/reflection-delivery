## Description

The BBR and CUBIC congestion controllers currently use hard-coded internal constants for key parameters like the initial congestion window size. For BBR, the loss detection threshold and bandwidth probing gain are also fixed. There is no way for application developers to adjust these values without modifying the library itself.

We need to introduce an application-level settings mechanism that lets callers provide optional overrides for these congestion control parameters when constructing a controller. If overrides are provided, the controller should use them; otherwise, it should fall back to its existing default behavior unchanged.

## Expected Behavior

- Both the BBR and CUBIC congestion controllers should accept an application settings object at construction time.
- The initial congestion window should be overridable by application settings for both controllers. If the provided value is zero (or not set), the controller falls back to a safe minimum.
- BBR's loss threshold and congestion window gain during bandwidth probing should be configurable through the same settings mechanism.
- When no overrides are set (the default), both controllers behave exactly as they did before.
- The relevant public constants used as defaults (e.g., the loss threshold) should be exposed so callers can reference them.

## Why This Matters

Different deployments operate in very different network environments. Allowing these parameters to be tuned at the application level — without requiring library changes — makes the congestion controllers usable across a wider range of scenarios while keeping sane defaults for the common case.
