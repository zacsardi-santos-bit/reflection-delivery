## Description

JavaScript has two legacy string methods for extracting substrings that are widely considered outdated and should be avoided in modern code. One of them takes a start index and a **length** as arguments, rather than start and end indices, which makes it easy to misuse and harder to read. The other has surprising edge-case behavior: it treats negative arguments as zero and swaps the start and end arguments when the end is less than the start — neither of which the modern equivalent does.

The modern alternative is more predictable, more commonly used, and better supported. However, there is currently no automated way to catch uses of these legacy methods in the linter.

## Expected Behavior

- Any use of the legacy substring methods — whether as bare property references, simple calls, or calls through optional chaining — should be flagged with a lint warning.
- The warning message should tell the developer to avoid the legacy method and use the modern alternative instead, along with a note explaining why the modern alternative is preferable.
- When a call is made with **no arguments**, an automatic unsafe fix should be offered to rename the method to its modern replacement.
- When arguments are present, no automatic fix should be offered, since the argument semantics differ and require manual migration.
- Calls to the modern alternative should not be flagged.
- The rule should be added to the nursery lint group.

## Why This Matters

Codebases that use these legacy methods are harder to maintain and more prone to subtle bugs. Having a lint rule to flag them encourages developers to migrate to the modern alternative that has consistent, well-defined behavior.
