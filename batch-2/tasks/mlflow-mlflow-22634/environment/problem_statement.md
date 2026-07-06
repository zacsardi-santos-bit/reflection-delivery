## Description

Third-party evaluation scorers (backed by various external scoring libraries) currently cannot be serialized to or deserialized from a portable format. This means a configured scorer — including which LLM model it uses and how it was set up — cannot be saved, shared, or restored. Additionally, any attempt to register a third-party scorer fails unconditionally, even when using an open-source backend that could support it.

## Expected Behavior

- Third-party scorers should support a full serialization round-trip: serialize to a structured dict and restore the original scorer object from that representation, preserving the class type, metric name, model URI, constructor arguments, and registered display name.
- The serialized format should include enough information to safely reconstruct the scorer on a different machine or session.
- Deserialization should perform security and integrity checks: only known, trusted scorer modules are allowed, required fields must be present, and any mismatch between stored metadata and the actual class must be detected and reported with clear error messages.
- Third-party scorer registration should be permitted on open-source backends but explicitly rejected on Databricks, with a descriptive error that explains the limitation and suggests an alternative.

## Why This Matters

Without serialization support, third-party scorers are ephemeral — they exist only in memory and cannot be persisted for reuse. Adding serialization and safe deserialization enables workflows where scorer configurations are stored, versioned, and reloaded, and allows registration with backends that support it.
