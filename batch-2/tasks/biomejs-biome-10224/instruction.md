I'm working on the Markdown parser and noticed that when you use backticks as the fence delimiter for a code block and put a backtick character in the info string, the parser currently just silently treats the whole thing as a paragraph instead of a code block — no warning, no error, nothing.

*   When a Markdown code fence opened with triple backticks has a backtick character in its info string, the parser must emit a parse diagnostic with the exact error message: "Backtick fence info string cannot contain backtick characters."

*   The diagnostic must include a detail annotation pointing to the exact position of the stray backtick with the text: "stray backtick here"

*   The diagnostic must include a hint with the exact text: "Use a tilde fence instead, or remove the backtick. Otherwise, the line is parsed as a paragraph, not a code block."

*   After emitting the diagnostic, the offending line must be parsed as a paragraph (not a code block), consistent with the CommonMark §4.5 rule that backtick fences cannot have backticks in their info strings.

*   Code fences opened with triple tildes (~~~) must parse successfully as fenced code blocks even when their info string contains backtick characters — no diagnostic should be emitted for tilde fences with backticks in the info string.

*   The backtick-in-info-string diagnostic must be emitted both at the block level (when a line that looks like a fenced code block appears at block scope) and in inline continuation contexts (when such a line appears within an existing paragraph).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.