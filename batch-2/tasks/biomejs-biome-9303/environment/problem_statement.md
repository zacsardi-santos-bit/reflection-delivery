## Description

The JavaScript formatter is not idempotent when handling switch statements where case clauses have trailing line comments and are followed by a block statement. On the first format pass the comments stay correctly on the case lines, but on a second pass those trailing comments get moved into the block statement below — producing different output than the first pass.

## Expected Behavior

- Trailing comments on a case clause line must remain on that line after formatting, even when the case (or a fall-through sequence of cases) is followed by a block statement.
- Formatting the output a second time must produce exactly the same result as the first pass (idempotence).

## Reproduction Pattern

A switch statement with multiple fall-through cases each annotated with a trailing line comment, where the last fall-through case is followed by a single block statement, triggers this bug. After the first format pass the output looks correct. Running the formatter again moves the trailing comment from the last case line into the opening of the block.

## Why This Matters

Idempotence is a fundamental property of a formatter — running it multiple times should never change the code. This bug means that users may see their code changed every time they save (triggering format-on-save), which is disruptive and unexpected. Trailing comments in switch statements are a common pattern for annotating case values, and they should be stable across formatting passes.
