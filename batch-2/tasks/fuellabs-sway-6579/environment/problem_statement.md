## Description

Two external libraries that the project depends on have released new versions with breaking API changes, and the codebase has not yet been updated to match. As a result, the project no longer compiles, and all tests fail to run.

The TOML document manipulation library renamed its primary mutable document type in a recent release. Any code that parses TOML files for editing now references a name that no longer exists.

The EVM execution library underwent a much more extensive overhaul. VM instantiation moved to a builder pattern, the way transaction fields are accessed and mutated changed, execution results were restructured into a new enum hierarchy under a primitives module, and the success-reason type was replaced by a new enum with updated variant names.

## Expected Behavior

- The workspace dependency versions must be bumped to the current releases of both libraries.
- All code that mutably parses TOML content must use the new type name provided by the updated library.
- The EVM test harness must use the builder-based construction pattern and the new result types to correctly initialize an EVM, deploy a smart contract, issue a follow-up call, and interpret execution outcomes.
- All previously passing tests — including deploy, submit, encode, and node URL tests in the client tooling — must compile and pass again.

## Why This Matters

Without these updates the project cannot compile at all, blocking every developer and every CI pipeline. Getting these library migrations in place restores a working build and keeps the project on currently supported dependency versions.
