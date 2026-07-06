I'm using Nx to manage versioning for a monorepo that includes Rust packages.

*   When the Nx release versioning process writes or updates Cargo.toml files, all string values (package name, version number, dependency version strings) must use double-quoted strings, not single-quoted strings.

*   The serialized Cargo.toml content must not begin with a leading empty line before the first section header — the content must start directly with the first section (e.g. '[package]').

*   When a Cargo.toml dependency entry is expressed as an inline table object (e.g. 'dep = { version = "..." }') and the file is re-serialized by the Nx versioning test utilities, it must be expanded into explicit section-header form (e.g. '[dependencies.depName]' followed by 'version = "..."' on the next line).

*   After version bumping updates a dependency entry in Cargo.toml, the resulting entry must use a double-quoted string value (e.g. 'rustLibA = "1.1.0"').

*   When the AI agent setup generator writes or updates a codex config.toml file, MCP command arguments (such as the command name and subcommand tokens) must use double-quoted strings, not single-quoted strings.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.