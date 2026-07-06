## Description

The automated script that upgrades important package versions currently uses a single global cooldown window to decide whether a newly published version is too recent to adopt. This one-size-fits-all approach is inflexible: when a specific package needs to be upgraded sooner than the global window allows — for example, because a critical fix was just released — there is no way to configure a shorter per-package cooldown.

We need to add support for per-package cooldown overrides in the project configuration file. Users should be able to annotate specific packages with a shorter duration, and the upgrade script should respect that override when evaluating whether to upgrade.

## Expected Behavior

- A utility function should parse human-readable duration strings ("12 hours", "1 day", "30 minutes", etc.) into a numeric value representing hours. Unrecognised inputs (ISO timestamps, arbitrary text, empty strings) should return a sentinel value indicating failure.
- A function should read the project's configuration file and extract any manually added per-package cooldown overrides, returning a mapping of package names to their durations in hours. Entries that disable a package entirely (rather than specifying a duration) should be ignored.
- When checking whether a package version falls within the cooldown window, the check should accept an optional per-package cooldown parameter and apply it instead of the global default when provided.
- A function should be able to remove a package's override entry (and any associated expiry reminder comments) from the configuration file. This removal must apply to all relevant sections of the file, be idempotent, and leave all other entries untouched.

## Why This Matters

Without per-package overrides, operators must either wait for the global cooldown to expire or manually intervene to upgrade a specific package sooner. Supporting per-package durations in the configuration file makes the upgrade automation more flexible and reduces manual intervention.
