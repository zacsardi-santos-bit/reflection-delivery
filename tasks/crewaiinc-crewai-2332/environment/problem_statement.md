## Description

When building multi-agent workflows with CrewAI, there is currently no built-in way to uniquely identify individual agents, tasks, or crews. Once you create a component, you have no stable identifier to track it, compare it across runs, or verify its identity. This makes auditing and tracing workflows significantly harder.

We need a fingerprinting system where each component automatically receives a unique identity at creation time. Developers should also be able to opt into stable, reproducible identities by providing a seed — so that recreating the same component in a new session or run yields the same identifier.

## Expected Behavior

- Every agent, crew, and task automatically receives a unique fingerprint when created, without any extra configuration required.
- Each component exposes a fingerprint property giving access to its unique identifier, its creation timestamp, and any custom metadata the developer wishes to attach.
- Fingerprints are stable: modifying a component's properties after creation must not change its fingerprint.
- Developers can pass a seed string to generate a deterministic fingerprint — the same seed always produces the same identifier, even across different sessions.
- A string seed can also be provided directly when setting up a component's security configuration, and it is automatically converted into a deterministic fingerprint.
- The fingerprinting system must be accessible as a dedicated security submodule of the crewai package.
- When a component is copied, the copy must have its own new unique fingerprint, but any custom metadata attached to the original fingerprint must be preserved in the copy.
- Fingerprints can be serialized to and from dictionaries (including ISO-formatted timestamps) and round-trip through JSON correctly.

## Why This Matters

Without stable component identities, there is no reliable way to track which agent ran a particular task, audit component usage across sessions, or deduplicate components. The fingerprinting system provides the foundational building block for observability, auditing, and security in complex multi-agent workflows.
