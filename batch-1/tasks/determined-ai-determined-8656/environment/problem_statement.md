## Description

The agent has several configuration-related issues that need to be addressed:

1. **GPU visibility helpers are in the wrong package.** Functions and constants related to detecting GPU visibility from environment variables are currently defined as unexported symbols inside the agent command package. Other packages that need this information cannot access them. These should be moved to the shared agent options package and exported.

2. **Agent options struct is missing fields and has incorrect field names.** The options struct used to store agent configuration is missing a log configuration field, and TLS certificate/key fields use incorrect names that don't match the YAML configuration file format. YAML round-trip serialization for the full set of agent options does not work correctly.

3. **No default configuration function exists.** There is no single place to retrieve the default agent configuration values, making it difficult to ensure consistent defaults across tests and runtime initialization.

4. **Configuration merging does not enforce proper priority.** When combining agent configuration from multiple sources — config files, environment variables, and command-line flags — there is no clear mechanism to ensure the correct precedence is applied (flags should win over environment variables, which should win over config file values, which should win over defaults).

## Expected Behavior

- GPU-related environment variable constants and the function that reads them should be exported from the agent options package so other packages can use them.
- A function should exist to retrieve a complete set of default agent options.
- The agent options struct should correctly serialize and deserialize all configuration fields from YAML, including a log configuration block and correctly named TLS certificate fields.
- A configuration merge function should merge YAML config bytes into the running configuration while respecting the layered override precedence (flags > environment variables > config file > defaults).

## Why This Matters

These changes make the agent configuration system more testable, more consistent, and more correct. External packages can now reference GPU-visibility helpers without duplicating code. The correct precedence for configuration layering ensures that operators can reliably override settings via environment variables or flags without unexpected behavior.
