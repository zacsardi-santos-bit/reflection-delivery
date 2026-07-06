I've noticed a bug in the Gleam formatter where it incorrectly moves or drops comments I've placed inside type annotations.

*   When the formatter encounters a comment placed before a type argument in a function type annotation (e.g., `fn(... ) -> ...`), it must preserve that comment in its position before the type argument — it must not drop or reposition the comment.

*   When the formatter encounters a comment placed before an element in a tuple type annotation, the comment must appear before that element in the formatted output, not after it.

*   Trailing comments at the end of a function type's argument list must be preserved after formatting.

*   Nested function types that themselves contain type arguments with preceding comments must format correctly, with each comment remaining before its associated type argument.

*   Multiple consecutive comment lines appearing before a single type argument must all be preserved together in their position before that argument.

*   Function type annotations that have no comments between their type arguments must continue to be formatted as they were before (no regression for the comment-free case).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.