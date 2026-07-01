I'm working on the SQL server configuration system in Dolt and need to add version-awareness to the YAML configuration structure. The problem is that when we run mixed-version clusters — where one node is newer and another is older — the newer node might try to share configuration that contains options the older node has never heard of. This causes failures on the older side.

I need a mechanism where each configuration field can be annotated with the minimum version of Dolt that supports it. When serializing configuration for an older version, any fields introduced after that version should be automatically removed. Fields that haven't been assigned a final version yet (still in development) should also be omitted during serialization.

There's also a constraint I need to enforce: any field that carries a version annotation must be a nullable type (so it can actually be omitted) and must be marked as omit-when-empty in the YAML output. If a developer accidentally puts a version annotation on a non-nullable field, that should be caught immediately.

Finally, I want a validation file that records the version metadata for every field in the configuration struct. Tests should fail if anyone adds a new field without a proper version annotation, or if the validation file doesn't reflect the current state of the struct. Fields that are still pending a final version assignment are allowed to exist but are excluded from the validation file comparison.

The existing configuration struct is missing some required serialization tags on certain fields — those need to be added as well so all fields can be properly unmarshalled and validated.
