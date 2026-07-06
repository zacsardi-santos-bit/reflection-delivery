I'm seeing two bugs related to how the linter handles regex patterns that contain closing parentheses.

First, when I write a regex call where the pattern includes a closing parenthesis character, the linter incorrectly suggests I should replace the entire call with a plain string operation. That suggestion is wrong — a closing parenthesis is a valid regex metacharacter, and treating the pattern as a "plain" string would change behavior.

Second, in the opposite direction, when a test uses a plain (non-raw, unescaped) string as a match pattern that contains a closing parenthesis, the linter doesn't warn me that the closing parenthesis might be ambiguous. It should — just like it warns about other metacharacters in plain strings — because the closing parenthesis has special regex meaning.

Both problems seem to have the same root cause: the closing parenthesis is not included in whichever internal list or check is used to decide whether a pattern contains regex-significant characters. Adding it to that list should fix both the false positive (bad suggestion) and the false negative (missed warning) at once.
