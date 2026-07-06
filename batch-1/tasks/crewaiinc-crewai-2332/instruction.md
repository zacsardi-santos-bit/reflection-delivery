Implement a fingerprinting system for CrewAI components to uniquely identify agents, tasks, and crews across different runs and sessions. Ensure each component automatically receives a unique identifier upon creation, with options for stable, reproducible identities using a seed string.

*   Create a `crewai.security` module exporting `Fingerprint` and `SecurityConfig` classes.
*   Implement the `Fingerprint` class:
    *   Auto-generate `uuid_str` and `created_at` in the constructor, ignoring any passed values for these fields.
    *   Allow explicit setting of `metadata`, defaulting to an empty dictionary.
    *   Provide a `uuid` property returning a `uuid.UUID` object from `uuid_str`, raising `ValueError` if invalid.
    *   Implement `generate(seed=None, metadata=None)` classmethod for creating `Fingerprint` instances, using deterministic UUIDs when a seed is provided.
    *   Implement `_generate_uuid(seed: str)` classmethod to return a deterministic UUID string for a given seed.
    *   Ensure two `Fingerprint` instances with the same `uuid_str` are equal and have the same hash.
    *   Implement `__str__` to return `uuid_str`.
    *   Implement `to_dict()` to return a dictionary with `uuid_str`, `created_at` (ISO format), and `metadata`.
    *   Implement `from_dict(data)` classmethod to create a `Fingerprint` from a dictionary, preserving fields without auto-generating new values.

*   Implement the `SecurityConfig` class:
    *   Automatically generate a `Fingerprint` upon instantiation if no arguments are provided.
    *   Accept a `Fingerprint` instance or a string as the `fingerprint` parameter, treating strings as seeds for deterministic fingerprints.
    *   Provide a `to_dict()` method returning a dictionary with a `fingerprint` key.

*   Update `Agent`, `Crew`, and `Task` classes:
    *   Accept an optional `security_config` parameter of type `SecurityConfig`.
    *   Automatically create a new `SecurityConfig` with a unique fingerprint if not provided.
    *   Expose a `fingerprint` property returning `self.security_config.fingerprint`.
    *   Ensure shared `SecurityConfig` instances result in the same `Fingerprint` object for multiple components.
    *   Maintain fingerprint stability after component modifications.
    *   Ensure copied components have preserved `security_config` and `fingerprint`, with a new `uuid_str`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.