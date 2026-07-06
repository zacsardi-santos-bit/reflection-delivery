## Description

We need to introduce a new codemod package to the Payload CMS monorepo that provides infrastructure for automatically migrating user codebases during major version upgrades. This package needs two foundational pieces: a CLI argument parser and a transform runner, along with an example no-op transform to serve as a reference for contributors.

## Expected Behavior

- The CLI argument parser should accept an array of command-line arguments and return a structured object with sensible defaults. It should support a positional path argument (defaulting to the current working directory when omitted), boolean flags for dry-run mode, listing available transforms, and printing changes, as well as an option to select a specific named transform. The dry-run flag should also be recognizable by an alternative longer form.

- The transform runner should accept a collection of transforms along with a TypeScript project, apply each transform in sequence, and aggregate the results. If an individual transform encounters an error, the runner should continue with the remaining transforms rather than aborting. The overall result should indicate whether any failures occurred, and each per-transform result should capture the list of changed files, any notes, and any error that was thrown.

- An example no-op transform should be included as a template for building real transforms. It should leave source code unchanged and be idempotent.

- A test helper utility should allow individual transforms to be tested against source strings without requiring a full project on disk.

## Why This Matters

Contributors adding new codemods to Payload need clear patterns and infrastructure to build against. Without a shared CLI parser and runner, each migration would have to reinvent argument handling and error recovery. This package establishes the foundation so that future transforms can be added consistently.
