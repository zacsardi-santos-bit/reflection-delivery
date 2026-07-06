# Silent Paragraph Demotion for Fenced Code Blocks with Backticks in Info String

## Description

When authoring Markdown, if you open a fenced code block using backticks as the fence character and include a backtick in the info string (the language tag), the CommonMark specification says the line is **not** treated as a code fence at all — it silently falls through and becomes part of a paragraph. Currently the parser accepts this without any warning or error, which means authors get a confusing silent failure: they wrote what looks like a code block, but it renders as a paragraph.

## Expected Behavior

- When a backtick-fenced code block has a backtick character in its info string, the parser should emit a clear error pointing to the stray backtick, explaining that the fence is invalid and the line will be treated as a paragraph instead.
- The error should suggest using a tilde-based fence as an alternative, since tilde fences **are** allowed to have backticks in their info strings and should parse successfully without any error.

## Why This Matters

Without this diagnostic, the issue is completely invisible to users: they write what they believe is valid Markdown, see no errors, and then wonder why their code block doesn't render correctly. A proper diagnostic makes the problem immediately actionable — it tells the author exactly where the backtick is and how to fix it.
