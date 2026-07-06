## Description

Langflow's vertex build records accumulate indefinitely in the database. Every time a flow is built, new build records are inserted for each vertex node, but old records are never cleaned up. Over time this causes unbounded database growth, which degrades query performance and wastes storage.

We need a mechanism to cap how many build records are retained — both globally across the entire system and per individual vertex node. When a cap is exceeded, the oldest records should be removed automatically so that only the most recent builds are kept.

## Expected Behavior

- A configurable global cap limits the total number of vertex build records in the database.
- A separate configurable per-vertex cap limits how many build records are kept for each individual vertex node within a flow.
- When either cap is exceeded after a new build is logged, the oldest builds (by timestamp) are deleted to bring the count back within the limit.
- The retention limits should be part of the application settings with sensible defaults.
- The function that logs new build records should accept optional override values for both limits, so callers can bypass the global configuration when needed.
- Concurrent build operations must not produce errors or violate the limits.

## Why This Matters

Without this change, long-running Langflow instances accumulate millions of build records with no way to prune them, leading to slow queries and disk pressure. With configurable limits and automatic pruning, operators can tune build history retention to match their storage budget.
