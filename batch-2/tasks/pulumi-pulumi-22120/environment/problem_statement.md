## Description

When Pulumi stores resource state to a snapshot (checkpoint), output values that have actually resolved to known values are being serialized incorrectly. Specifically, all output values — regardless of whether they are known or unknown at serialization time — are stored as a "computed" placeholder. This means the actual resolved values are lost in the snapshot even when they are available.

## Expected Behavior

- When an output value is **unknown** (not yet resolved), it should continue to be stored as a computed placeholder, since there is nothing meaningful to record yet.
- When an output value is **known** (has a resolved value), its actual inner value should be preserved in the snapshot rather than being discarded and replaced with a placeholder.
- When a known output value is also **secret**, it should be stored as an encrypted secret containing the actual inner value.

## Why This Matters

Losing known output values in the snapshot means the state file no longer accurately reflects the shape of the resource data. This can cause downstream issues where the engine has to treat known values as unknown during subsequent operations. Correctly preserving known output values keeps the snapshot accurate and consistent with the actual resource state.

The round-trip behavior (serialize to snapshot, then deserialize back) should correctly reconstruct values: unknown outputs become computed placeholders, known outputs become their inner values, and known secret outputs become encrypted secrets.
