## Description

When managing a large Terraform/Terragrunt infrastructure codebase, modules frequently have complex dependency relationships — module A depends on B, B depends on C, and so on. Currently, Terragrunt can tell you what a module's own dependencies are, but there is no built-in capability to answer the reverse question: **which modules depend on a given module?**

This reverse-dependency mapping is essential for operations such as determining which modules need to be re-applied or safely destroyed when a module changes. Without it, engineers must manually trace the dependency graph to understand the full blast radius of any change.

## Expected Behavior

- Given a stack of modules with known dependency relationships, it should be possible to build a complete reverse-dependency index.
- For each module that has dependents, the index should list all modules that depend on it — both directly and transitively.
- The ordering should be intuitive: modules that directly depend on a given module should appear before those that depend on it only indirectly.
- The implementation must correctly handle circular dependency relationships between modules (i.e., it must not hang or crash).

## Why This Matters

This capability is a prerequisite for higher-level features like "graph apply" and "graph destroy" — running a Terraform command not just on a single module, but on the entire set of modules that transitively depend on a given module, in the correct order.
