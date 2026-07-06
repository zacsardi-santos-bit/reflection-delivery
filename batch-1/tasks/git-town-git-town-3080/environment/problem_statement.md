## Description

This repository includes several internal tooling scripts for enforcing code style: one that standardizes the name used for method receivers across the codebase, one that ensures test files have consistent formatting, and one that checks whether struct fields are declared and initialized in alphabetical order. These tools currently contain their own hand-rolled testing code — each tool embeds a custom test runner invoked via a special command-line argument — rather than relying on the standard language testing infrastructure.

This means the tools' own correctness cannot be verified using the same standard test command used for the rest of the codebase. Their embedded test runners are harder to maintain and don't integrate with the normal development workflow.

## Expected Behavior

- The core logic inside each tool should be exposed through well-defined, publicly accessible functions so they can be imported and tested conventionally.
- Each tool should live in a properly declared standalone module, and a workspace configuration file should tie all tool modules together with the main module so a single test command covers everything.
- The embedded custom test runners inside each tool should be removed in favor of standard test files that exercise the newly exposed functions.
- The struct-sorting linter and the receiver-name formatter must each continue to handle the same categories of inputs (empty structs, certain exempt type names, already-sorted or already-formatted content) and produce the same outcomes as before.

## Why This Matters

Bringing these tools under the standard test runner improves maintainability and consistency. Contributors get the same familiar feedback loop for tool code as for production code, without needing to understand a bespoke internal test protocol.
