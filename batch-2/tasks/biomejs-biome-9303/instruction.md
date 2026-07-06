I've run into a formatter idempotence bug with switch statements that have trailing comments on case clauses.

*   When formatting a JavaScript switch statement, trailing line comments on case clause lines must remain on the same line as their respective case keyword — they must not be moved into a subsequent block statement.

*   When multiple fall-through case clauses each have trailing line comments and the last such clause is followed by a block statement, all trailing comments must be preserved on their original case lines in the formatted output.

*   The formatter must be idempotent for this pattern: formatting the already-formatted output of such a switch statement a second time must produce exactly the same result as the first format pass.

*   This correct comment placement must apply in both 2-space and tab indentation modes.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.