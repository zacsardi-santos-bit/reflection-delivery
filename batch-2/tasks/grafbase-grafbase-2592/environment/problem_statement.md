## Description

Setting up the test environment for a Grafbase extension currently requires two separate steps that are easy to get wrong. First, the test runner is created with a synchronous call, and then a separate async method must be called to actually start the gateway and mock subgraph servers. Forgetting the second step causes tests to fail with confusing errors that don't make the missing step obvious. Additionally, the test runner variable must be declared as mutable even though user code never mutates it — the mutation happens only internally during server startup.

The generated test scaffolding produced by the CLI extension initialization commands reflects this two-step API, so new extension authors are also exposed to this ergonomics issue from the start.

## Expected Behavior

- Creating the test runner should be a single async operation that automatically starts all servers before returning.
- The test runner variable should not require a mutable binding in user test code.
- No separate server startup call should be needed or available in the public API.
- The mock server utilities should live in their own standalone package, separate from the main SDK test utilities, so they can be depended on independently.
- The CLI-generated extension scaffolding should reflect the new, simpler API pattern and reference the updated SDK version.

## Why This Matters

The current two-step API forces every test author to remember an extra initialization call that could easily be merged into the construction step. Merging these into a single async constructor removes a footgun, simplifies onboarding, and produces cleaner generated scaffolding for new extension projects.
