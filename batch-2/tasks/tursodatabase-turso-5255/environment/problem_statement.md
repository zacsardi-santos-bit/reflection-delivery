## Description

The SQL statement splitter used in the JavaScript database bindings incorrectly handles trigger definitions. When a trigger body contains multiple statements separated by semicolons (inside a begin/end block), the splitter treats each semicolon as a statement boundary, fragmenting the trigger into multiple invalid pieces instead of keeping the entire definition together as one statement.

## Expected Behavior

- A trigger definition with a multi-statement body should be returned as a single, self-contained SQL statement by the splitter.
- Any additional SQL statements that follow the trigger definition should be split out correctly as separate statements.
- String literals inside a trigger body that happen to contain the word that closes a trigger block should not be misinterpreted as a terminator — the splitter must look past string contents.
- Trigger variants that include a "temporary" modifier or are prefixed with an explain keyword should be recognized just like ordinary triggers, with their bodies kept intact.

## Why This Matters

Multi-statement trigger definitions are valid and common SQL. When the splitter breaks them apart, the resulting fragments are individually invalid and will fail when executed against the database. This prevents any application that relies on this splitter from creating or managing triggers reliably.
