## Description

When an AI assistant is helping a user work with a marimo notebook, it currently has no structured way to understand how the notebook's cells are connected through shared variables. Without this information, the AI cannot easily determine which cells will be affected when a variable changes, identify cells that define the same variable (causing conflicts), or understand the broader data flow before making edits.

## Expected Behavior

A new AI tool should allow querying the notebook's cell dependency graph. The tool should return, for each cell:
- The variables it defines, including the kind of each variable (e.g., whether it is a regular value or a function)
- The variables it references
- Which other cells are its parents (produce variables it consumes) and children (consume variables it produces)

The tool should also provide:
- A global map of which cells own which variables
- A list of variables that are defined in more than one cell (a common source of errors)
- Information about any circular dependency cycles in the notebook

When querying, the user should be able to center the query on a specific cell and optionally limit results to only cells within a given number of dependency hops from that cell. Even when filtering by depth, the full variable ownership map should always reflect the entire notebook. If a non-existent cell identifier is supplied, the tool should fail with a clear, identifiable error.

## Why This Matters

This capability is essential for an AI assistant to reason safely about notebook structure before editing cells that involve shared variables, and to help diagnose dependency-related errors.
