## Description

New end-to-end tests were added for TLS traffic stealing. These tests depend on a PEM-format certificate encoding library that is also used by several other internal crates in the workspace. The new tests reference this library through the workspace dependency mechanism (i.e., they expect it to be declared at the workspace level), but the library has never been added to the workspace's shared dependency list.

As a result, when Cargo tries to load the workspace manifest, it cannot find the library in the workspace's shared dependencies section and fails immediately — before compiling a single package. This means every package in the workspace, including the core protocol crate with its independently-written codec unit tests, fails to compile and all tests fail.

## Expected Behavior

- The PEM encoding library should be declared as a workspace-level dependency.
- All crates in the workspace that currently specify this library locally should instead reference the workspace entry, keeping the version consistent.
- The workspace should load and compile successfully.
- The protocol crate's codec unit tests should run and pass.

## Why This Matters

A dependency version conflict at the workspace configuration level blocks compilation of the entire project. Even tests that have nothing to do with TLS or certificates cannot run because the workspace cannot resolve its dependency graph. Promoting the shared library to workspace-level resolves the conflict and unblocks all downstream compilation.
