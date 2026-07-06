## Description

Developers building new inspection plugins for the KHI log-parsing framework currently have to implement several common log-processing patterns from scratch each time. These include filtering log entries down to a relevant subset, grouping logs by resource identity, reading structured fields from each log's payload, and translating those grouped logs into timeline history entries. Each of these patterns must also correctly distinguish between full-run mode (where results are persisted to the inspection history) and dry-run mode (where no changes should be written). Writing this boilerplate repeatedly across plugins is error-prone and slows feature development.

## Expected Behavior

A set of reusable task factory functions should be provided in the inspection taskbase package, covering:

- **Log filtering**: given a source list of logs and a predicate, produce only the logs that match, or an empty list in dry-run mode
- **Log grouping**: given a source list of logs and a classification function, produce a map of named groups of logs, or an empty map in dry-run mode
- **Field-set reading**: given a source list of logs and a set of field readers, concurrently attach parsed field data to each log in run mode, or skip all processing in dry-run mode; must correctly handle batches larger than any internal concurrency limit
- **History modification**: given a grouping of logs and a modifier that processes each log sequentially within a group (carrying per-group state forward), write the results to the history builder; if a single log produces an error, skip flushing its change set but continue with remaining logs; in dry-run mode, skip all history writes

Additionally, the history builder and timeline builder types should expose test-utility methods that allow tests to inspect the raw history state and retrieve copies of timeline events.

## Why This Matters

Standardizing these common patterns into well-tested primitives reduces duplication across plugin implementations, makes each plugin easier to review, and ensures consistent handling of run-mode vs dry-run semantics throughout the inspection pipeline.
