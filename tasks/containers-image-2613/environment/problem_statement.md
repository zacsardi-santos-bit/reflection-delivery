## Description

When container image layers are written to local storage, each layer is assigned a unique identifier ("chain ID") that determines how the layer is found and deduplicated in future operations. This ID computation currently lives inline inside the commit logic, making it hard to test and reason about in isolation.

Additionally, the system currently does not verify that the uncompressed-layer-digest values declared in an image's configuration actually match the real uncompressed content of each layer. This creates an ambiguity: a single compressed blob can be "seen" as two different layers — one decoded traditionally via the full tar stream, and another via a partial-pull TOC-based path — without any check to enforce consistency.

## Expected Behavior

- The layer identity computation should be extracted into a dedicated function so it can be tested independently with known inputs and outputs.
- For layers identified by their uncompressed digest (traditional pull), the chain ID should follow the established Docker/OCI chain-ID derivation rule (or simply return the uncompressed hex for a root layer).
- For layers identified by their table-of-contents digest (partial pull), the chain ID should incorporate a prefix that distinguishes it from the traditional form, and must always be hashed rather than returned raw.
- The blob digest associated with a layer must not influence the chain ID.
- When storing an image, the system must reject any image whose configuration declares DiffID values that do not match the actual uncompressed content of the layers being committed.

## Why This Matters

Without these guarantees, it is possible to store an image whose configuration misrepresents the actual layer contents, or to create storage ambiguities when the same blob is accessible via both a full-pull and a partial-pull path. Enforcing DiffID consistency and making the ID derivation explicit and testable closes these gaps.
