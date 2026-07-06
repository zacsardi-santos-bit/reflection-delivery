## Description

When Nx processes TOML-format files — specifically Rust project manifests during version bumping and AI coding agent configuration files — the output incorrectly uses single-quoted strings for all string values. The TOML specification reserves single-quoted strings for literal strings (which suppress escape sequences), while standard double-quoted strings are the expected default for ordinary string values. This means the files Nx generates are technically non-standard and may be misinterpreted or displayed oddly by TOML-aware tooling.

There are also two related issues with the serialized output:

- The content begins with a spurious blank line before the first section header, making the file look malformed.
- Nested dependency table entries (expressed as inline tables) are not expanded into the more readable explicit section-header form when re-written.

## Expected Behavior

- Cargo.toml files written or updated by Nx during version bumping must use double-quoted strings for all string values: package name, version, and dependency version references.
- The serialized content must start directly with the first section header — no leading blank line.
- Dependency entries that were originally inline tables should be expanded to explicit section-header form when the file is re-serialized.
- AI agent configuration files generated or updated by Nx must use double-quoted strings for command arguments in MCP configuration entries.

## Why This Matters

Generating TOML files with single-quoted strings can cause subtle compatibility issues with other TOML tools and linters in the Rust ecosystem. Developers using Nx to manage Rust package versioning or set up AI coding agents would receive files that deviate from standard TOML conventions, potentially causing confusion or tooling failures downstream.
