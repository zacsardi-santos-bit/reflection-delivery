I'm working on extending the linter to catch unnecessary escape sequences in string literals, similar to the existing check for regular expressions. Right now the tool flags useless escapes inside regexes, but nothing flags them in ordinary strings like single-quoted, double-quoted, or template literal strings.

For example, if someone writes a backslash before a plain letter inside a string where that letter has no special meaning, or escapes a quote character that isn't actually the delimiter of that string, the linter should warn them. The fix should simply remove the extra backslash. The warning message should explain that only the enclosing quote and recognized special sequences need escaping.

A few important exemptions: tagged template literals and strings inside JSX attribute values should be completely ignored by this check, since those contexts are special. Recognized escape sequences like null characters, control characters, and unicode escapes should also not be flagged.

Additionally, the existing check for useless escapes in regular expressions should be updated so its severity is shown as a warning rather than an error, to keep the output consistent with the new string check.
