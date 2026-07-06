I'm working on a Rust-based Python linter that checks string quote styles and suggests fixes. There's a bug where the linter offers an automatic fix that would actually break the user's code.

The issue occurs with implicit string concatenation — when multiple string literals appear next to each other in Python without an explicit concatenation operator. If one of those string tokens uses the wrong quote style and you try to convert it to the preferred style, you can end up with three consecutive identical quote characters at the boundary between two adjacent strings. Python interprets that as the start of a triple-quoted string, which would completely change the meaning of the code and cause a syntax error.

There are two specific patterns where this happens:
1. An empty string using the wrong quote style, immediately followed by a string using the preferred quote style — converting the empty string creates the dangerous triple-quote sequence.
2. A string that is immediately preceded by two consecutive preferred-style quote characters (from an adjacent string token) — converting this string also creates the triple-quote sequence.

The linter should still report that these tokens use the wrong quote style, but it must not offer an automatic fix for those specific tokens. Other tokens in the same implicit concatenation that can be safely converted should still get auto-fixable diagnostics. This affects both regular inline strings and strings used in docstring positions.

The violations for inline strings and docstring-position strings should reflect that auto-fixing is only sometimes possible, not always.
