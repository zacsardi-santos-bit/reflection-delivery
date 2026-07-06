## Description

Pulumi's infrastructure incorrectly strips certain map property keys whenever they appear at **any** nesting level within a resource's property map. Specifically, keys that begin with a special internal prefix are silently removed not only at the outermost level (where filtering is intentional) but also inside nested map values (where filtering is wrong). This means that when a provider or a Pulumi program uses a map whose nested entries happen to start with that prefix — or uses an empty string key — those entries are silently lost during the resource lifecycle, causing subtle data corruption bugs.

## Expected Behavior

- Keys starting with the internal prefix must only be filtered at the **top level** of a resource property map.
- Nested maps (i.e., map values inside a top-level property) must pass through completely intact, with all keys preserved regardless of prefix.
- The Go SDK map-value unmarshaling path must similarly stop stripping internal-prefix keys when they appear in nested positions; such keys and their values must survive the round-trip.
- The system should include a conformance test that validates this behavior end-to-end across a real program, including resource creation, invocations, and stack outputs — using a map that includes adversarial keys such as empty strings, internal-prefix keys, and keys containing special escape sequences.

## Why This Matters

Providers and programs may legitimately use map properties whose keys look like internal keys. Before this fix, those values are silently dropped, making it impossible to reliably use such maps. A conformance test ensures all language SDKs respect this contract consistently.
