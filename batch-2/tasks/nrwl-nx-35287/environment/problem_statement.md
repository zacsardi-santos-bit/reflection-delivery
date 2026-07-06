## Description

In an Nx monorepo, Playwright end-to-end test targets do not currently track workspace-level TypeScript configuration files as build inputs. When a project's tsconfig inherits from shared configs that live outside the project directory (for example, at the workspace root), changes to those shared configs do not invalidate the Playwright target's cache. This means tests may skip re-running even when the TypeScript configuration they depend on has changed.

## Expected Behavior

- When a project's TypeScript config file references parent configs outside the project directory, those external configs should be listed as inputs to the Playwright target.
- The workspace root tsconfig (when it is not the file already tracked by the native hasher) should be included as an input.
- The native root tsconfig — the one already handled by Nx's built-in file hasher — should be excluded to avoid redundant tracking.
- Tsconfig files that live inside the project's own directory should not be added (they are already covered by existing inputs).
- When a distributed CI target is configured alongside the primary target, both should receive the same set of tsconfig inputs.

## Why This Matters

Without this, developers relying on shared TypeScript configurations across their monorepo can encounter false cache hits where Playwright tests use stale cached results after a shared config change. This undermines the reliability of incremental builds in large workspaces.
