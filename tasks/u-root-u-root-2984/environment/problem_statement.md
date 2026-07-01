## Description

The project is missing a topological sort utility. Topological sorting is a classic operation for ordering items according to their dependency relationships — given a set of pairs where each pair means "item A must come before item B", produce an ordering of all items that respects those constraints. Many Unix-based build tools rely on such a utility.

## Expected Behavior

- The tool should read whitespace-separated pairs of names from standard input (or from a file given as an argument), where each pair represents a dependency edge.
- It should output the names in topological order, one per line, such that each name appears before any names that depend on it.
- Input that uses any combination of whitespace (spaces, tabs, newlines, carriage returns, etc.) as delimiters should be handled correctly.
- If the input has an odd number of tokens (i.e., an incomplete pair), the tool should report an error.
- If the dependency graph contains cycles, the tool should report each cycle to standard error (prefixed with "tsort:") and still output all the nodes, returning a non-fatal error status.
- If a file argument is given but the file does not exist, the tool should return an error that identifies the missing file.
- Self-referencing pairs (where both elements of a pair are the same name) should be treated as declaring a single node with no dependency.

## Why This Matters

This utility is a standard part of POSIX-compatible environments and is commonly used in build systems and package management pipelines. Adding it to the project makes the toolkit more complete for users who need to manage ordered dependencies in embedded or minimal Linux environments.
