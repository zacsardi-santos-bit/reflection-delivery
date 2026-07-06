## Description

When combining configuration from one or more sources, providers that are listed more than once using the same reference should be deduplicated. This already works for plain string provider identifiers, but fails silently for provider configuration objects that contain function-valued properties (such as a custom response transform).

## Current Behavior

If the same provider config object — one that includes a function property — is referenced twice in a providers list, both entries appear in the final combined output. The user ends up with a duplicate provider that should have been collapsed to one.

## Expected Behavior

- When the exact same provider config object (with or without function-valued properties) is referenced multiple times, the combined output should contain only one copy of that provider.
- When the exact same provider instance (a class instance implementing the provider interface) is referenced multiple times, the combined output should contain only one copy.
- Two *different* provider objects or instances that each happen to carry function properties must still be preserved as separate entries, since they represent distinct configurations.

## Why This Matters

Users sometimes define a shared provider variable and reference it in multiple places within their config, or they spread a provider list that accidentally includes the same entry twice. In both cases the intention is a single provider, but the current behavior silently duplicates it. Aligning the deduplication logic so that reference equality is respected for all provider types — including those with function fields — prevents confusing redundant evaluation runs.
