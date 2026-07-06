## Description

When a heading is preceded by an empty paragraph and the user presses Backspace at the very beginning of the heading, the editor should remove only the empty paragraph and leave the heading intact. Currently, instead of cleanly deleting the blank line, the editor merges the heading into the preceding block or converts it into a plain paragraph — stripping the heading's formatting entirely.

This is a common editing scenario: the user accidentally creates a blank line before a heading (e.g., by pressing Enter before adding content) and then tries to remove it with Backspace. The expected result matches how most modern word processors behave — only the empty line disappears, and the heading stays a heading.

## Expected Behavior

- Pressing Backspace at the start of a heading that immediately follows an empty paragraph removes the empty paragraph and keeps the heading with its original type and content.
- This behavior applies to other formatted block types as well (e.g., block quotes), not just headings.
- If there are additional blocks after the heading, they must remain unaffected.
- When the preceding block is **not** empty, the existing merge behavior continues to apply as before.
- A heading with no content (empty heading) should still convert to a plain paragraph on Backspace at the start, preserving that existing behavior.

## Why This Matters

Users frequently press Backspace at the start of a heading to remove a preceding blank line. The current behavior destroys heading formatting in that scenario, forcing users to manually reapply the heading style. The fix makes the editor behave predictably: an empty preceding line is simply deleted, and the heading is left untouched.
