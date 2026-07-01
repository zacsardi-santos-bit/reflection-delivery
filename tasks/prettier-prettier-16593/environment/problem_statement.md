## Description

Prettier incorrectly formats JavaScript logical expressions that contain inline comments when those expressions appear inside a unary expression (such as a negation or void-type operation).

When a developer writes a multi-part condition where each clause is followed by a comment explaining its purpose — a common and readable pattern — Prettier currently misplaces the trailing comment on the last clause. Instead of keeping it attached to the last operand, Prettier incorrectly hoists it up to the enclosing expression, adding unwanted extra parenthesization in the process.

## Example

A simple chain of OR conditions inside a negation, where each condition has a trailing comment, ends up with the comment on the last condition moved outside the group and unnecessary extra wrapping parentheses added. This produces valid but visually different (and arguably less readable) output compared to what was written.

## Expected Behavior

- Trailing inline comments on operands in a logical chain inside a unary expression should stay with the operand they annotate.
- No extra parenthesization should be introduced solely because of the presence of trailing comments.
- Mixed AND/OR chains with inline comments should still be correctly grouped by precedence, with comments staying at their respective positions.
- The fix should work consistently regardless of semicolons being enabled or disabled, and across all supported parsers.

## Why This Matters

This is a formatting correctness bug. When developers add inline comments to logical expressions and run the formatter, the output should preserve the intent and readability of the original code. Misplaced comments make it harder to read and can confuse reviewers who expect comments to be adjacent to the code they describe.
