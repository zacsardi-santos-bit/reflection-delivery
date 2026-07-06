## Description

Tool specification factories — the functions that define how each tool is described to the language model (its name, parameters, and schema) — currently live in a separate utilities crate rather than alongside the handler implementations in the core crate. This means that to understand any given tool you have to look in two separate places: the handler logic in the core crate and the specification factory in a different crate. This makes the codebase harder to navigate and maintain.

## Expected Behavior

- Each tool's specification factory function should live in the same crate as its handler, organized in clearly named spec modules (e.g., a shell spec module, an apply-patch spec module, an MCP resource spec module, a test-sync spec module).
- New spec modules should be added for code-execution-mode tools (covering both the execute and wait operations) and for hosted tools such as image generation.
- The spec factories for code execution mode must produce a freeform tool definition that uses a formal grammar to constrain valid inputs.
- The spec factory for the wait operation must produce a function-call tool definition with parameters for cell identifier, yield time, max tokens, and a terminate flag.
- The image generation spec factory must accept an output format string and produce an image-generation tool spec with that format.

## Why This Matters

Co-locating the specification and the handler makes each tool a cohesive unit, reduces cross-crate coupling, and makes it straightforward to add or modify tools without jumping across the codebase. The new spec modules for code-execution and hosted tools fill gaps where no spec factories previously existed in the core crate.
