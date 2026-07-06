## Description

The file-fixing capability in jj — which applies automated content transformations to files across commits — is currently implemented entirely at the CLI layer and cannot be reused by other library consumers or tooling built on top of the jj core library. This makes it impossible to write plugins, integrations, or tools that programmatically apply fixes without duplicating the complex commit-graph traversal and tree-rewriting logic.

## Expected Behavior

- A library-level API should be available that accepts a set of starting commits, a file matcher, and a pluggable fixer component, then walks the commit graph applying the fixer to relevant files and rewriting any commits that were changed.
- The API should support controlling whether already-unchanged files (files identical to the parent commit's version) should be re-processed or skipped.
- The API should return a structured result summarizing how many commits were inspected, how many were actually rewritten, and a mapping from original commit IDs to the new rewritten ones.
- Errors from the fixer should be propagated cleanly, stopping the process without applying partial changes.
- A parallel execution implementation should be available so that many files within a commit can be processed concurrently for better performance.

## Why This Matters

Without this library API, all tooling that needs to apply automated fixes across jj commits must either re-implement the commit-graph traversal logic themselves or shell out to the CLI. Exposing this functionality as a stable library API enables reliable programmatic access, makes the functionality testable in isolation, and allows other library consumers to build on top of it with their own custom fixers.
