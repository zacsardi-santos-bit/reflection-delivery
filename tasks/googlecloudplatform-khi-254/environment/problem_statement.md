## Description

The project needs two new general-purpose utility packages that can be reused across the codebase.

The first is a pattern-matching utility for efficiently finding pre-registered strings within larger text. The primary use case is scanning log lines for known identifiers (such as container IDs) that typically appear after specific delimiter characters like quotes or spaces. The utility should support two implementations: a simple one that works well for small pattern sets, and a more efficient prefix-tree-based one suited for large sets (thousands of patterns). Both should share a common interface and support adding, retrieving, and deleting patterns, as well as matching the longest registered pattern at the beginning of a given input.

The second is a type-safe, generic, concurrent key-value dictionary. Currently the codebase has no reusable map abstraction that preserves Go's type system across goroutines. The new dictionary should allow storing and retrieving values by string key without losing type information or requiring manual type assertions, and must be safe for concurrent reads and writes. It should support the standard operations (set, get, delete) as well as convenience operations like returning a default value for missing keys and an atomic "get-or-initialize" operation.

## Expected Behavior

- Patterns can be registered once and then efficiently searched for across many text inputs.
- When scanning text, matches are found only after designated delimiter runes (or, optionally, at the start of the text).
- Longest-prefix matching is used when multiple registered patterns could apply.
- After a match, scanning skips past it to avoid overlapping results.
- The concurrent dictionary preserves type safety and is free of data races under concurrent access.
- The "get-or-initialize" operation does not call the initializer function when the key is already present.

## Why This Matters

These utilities serve as foundational building blocks for higher-level log-parsing features. Without them, each consumer must implement its own concurrent map wrapper and string-search logic, leading to duplication and potential concurrency bugs.
