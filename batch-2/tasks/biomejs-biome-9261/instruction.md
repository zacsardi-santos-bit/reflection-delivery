I'm using a CSS formatter and I've noticed that comments written inline within CSS property declarations are being moved to the wrong place.

*   When a CSS comment appears between a property name and the colon character in a CSS declaration (e.g., `color/* comment */:value`), the formatter must preserve the comment between the property name and the colon in the formatted output (e.g., `color /* comment */: value`), not move it before the property name.

*   When a CSS comment appears between the colon and the value in a CSS declaration (e.g., `color: /* comment */ blue`), the formatter must keep the comment immediately after the colon in the formatted output. The value must then be placed on the next line with one additional level of indentation (e.g., `color: /* comment */\n  blue`).

*   Multiple CSS comments between a property name and the colon must each be preserved in that position; similarly, multiple comments between the colon and the value must be preserved after the colon, with the value still placed on the next line.

*   Comments that already appear on their own line before a CSS property declaration (leading comments) must continue to be formatted correctly and must remain before the property name, not be affected by changes to colon/value comment handling.

*   The same comment-preservation rules for property name/colon/value positions must also apply to CSS declarations appearing inside at-rule conditions (such as feature-query conditions).

*   When no comment appears between the colon and the value, the value must remain on the same line as the property name and colon, with no extra line break introduced.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.