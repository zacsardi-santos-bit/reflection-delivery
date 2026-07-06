# Clean up public API surface by moving internal rendering classes to internal modules

## Description

Several classes that are internal implementation details of the rendering pipeline are currently exported through the main public module. This unnecessarily exposes internal rendering structures to external consumers and makes it harder to maintain API stability. These classes should be moved to a dedicated internal module so they are no longer part of the public surface.

Additionally, a settings class for plan projection views has a constructor that is supposed to be internal-only, but is still technically accessible from outside. It already has a static factory method that is the intended public creation mechanism — the constructor should be made truly private so the factory method is the only supported way to create instances. The factory method should return nothing when given an empty configuration, allowing callers to distinguish between "no settings" and "settings with default values."

Finally, the feature overrides class has several internal backing fields that are marked as accessible to subclasses, but the class already exposes equivalent public read-only properties for some of them. The backing fields that already have proper public equivalents should be made private so subclasses can no longer bypass the public API.

## Expected Behavior

- Internal rendering table classes should be importable only from a dedicated internal module path, not from the main public feature table module.
- The plan projection settings class should not be directly constructible; only the factory method should work.
- The factory method for plan projection settings should return nothing when the input has no meaningful properties.
- The always-drawn and never-drawn element ID sets on the feature overrides class should be accessible as public properties directly on the base class without needing subclass workarounds.

## Why This Matters

Keeping internal details out of the public API makes it easier to evolve the implementation without breaking changes. It also prevents consumers from accidentally depending on implementation details that may change in future versions.
