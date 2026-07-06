## Description

When a query is configured with both the "skip this query" and "don't run during server-side rendering" options at the same time, React raises hydration mismatch errors during client-side initialization.

The server correctly renders the component in a non-loading, ready state because the query is skipped. However, the client's hydration pass presents a different — loading — state, causing React to detect a discrepancy between what the server rendered and what the client initially expects. This results in recoverable hydration errors being logged.

## Expected Behavior

- When both options are set on a query, the component should consistently be in a non-loading, ready state across server-side rendering, hydration, and after client-side mounting.
- No hydration warnings or recoverable errors should be raised by React during hydration.
- The Apollo client cache should remain empty after server-side rendering, since the query was skipped and no data was fetched.
- Every render pass — from SSR through hydration through post-mount — should produce the same state values with no intermediate loading flash.

## Why This Matters

Developers using server-side rendering with Apollo Client who want to skip certain queries (e.g., for queries that should only run on the client under specific conditions) encounter unexpected React hydration errors in production. These errors degrade the developer experience, cause confusing warnings in server logs and browser consoles, and in some cases produce visible UI flashes as React re-renders after detecting and recovering from the mismatch.
