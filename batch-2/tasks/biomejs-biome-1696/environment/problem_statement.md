## Description

The JavaScript formatter is incorrectly handling empty lines inside member chain expressions. When code has intentional empty lines between parts of a chained sequence of property accesses and method calls, the formatter does not behave consistently.

Currently, there are two problems:

1. **Chains that should be collapsed to a single line** sometimes incorrectly retain or mishandle empty lines between chain members, rather than producing a clean single-line result.

2. **Chains that qualify for multi-line formatting** (because they are complex enough to be broken across multiple lines) do not preserve the developer's intentional empty lines between logical groups within the chain.

## Expected Behavior

- When a member chain is simple enough to fit on a single line, all empty lines between chain members should be removed, and the output should be a single compact expression.
- When a member chain is complex enough that the formatter breaks it across multiple lines, empty lines that appear between groups within the chain in the original source should be preserved in the formatted output.

## Why This Matters

Developers often use empty lines within long method chains to visually separate logical groups of operations. The formatter should respect this intent when it is appropriate (i.e., when the chain is being broken across multiple lines anyway), while still collapsing short chains cleanly. The current behavior is inconsistent and produces unexpected results depending on the chain's structure.
