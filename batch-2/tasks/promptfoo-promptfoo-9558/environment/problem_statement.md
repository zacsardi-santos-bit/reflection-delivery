## Description

Several utility functions related to evaluation processing are spread across different layers of the codebase. Some live inside the server layer and others inside the commands layer, but they are general-purpose utilities with no inherent dependency on either layer. This makes it awkward to reuse them from other parts of the application — for example, non-server code that needs to generate CSV exports or filter test cases must reach into server or command-specific directories, creating undesirable coupling.

## Expected Behavior

- Eval table utilities (CSV/JSON export helpers) should live in a shared utility directory accessible to any part of the codebase, not inside the server utilities folder.
- Filtering utilities for tests, prompts, and providers should similarly move out of the commands layer into a shared utility space.
- Existing import paths should continue to work via backward-compatible re-exports so that enterprise integrations and any other consumers are not broken.

## Why This Matters

Consolidating these utilities into a dedicated, layer-agnostic location reduces coupling between application layers and makes the utilities available to a wider set of consumers. It also makes the codebase easier to navigate — developers looking for eval-related utilities have one clear place to look.
