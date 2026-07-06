I've been looking at the compiler's error and warning output and noticed that the formatting of "Hint" sections is inconsistent.

*   When a compiler diagnostic message has both a non-empty body text and a hint, there must be a blank line (empty line) between the body text and the 'Hint:' line in the rendered output.

*   Hint text in operator-type-mismatch messages must begin with an uppercase letter: the phrasing must use 'The [operator] operator can be used with [type]s' (capital 'T'), not 'the [operator] operator can be used with [type]s' (lowercase 't').

*   Hint text must be line-wrapped to fit within the standard display width used by the rest of the compiler output; long hint strings that would exceed this width must wrap to the next line at a word boundary.

*   When two consecutive error blocks appear in a single diagnostic output, there must not be an extra blank line within the body of the first error block beyond what the message requires.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.