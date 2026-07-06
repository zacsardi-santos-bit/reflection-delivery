I've been using the linter's auto-fix feature to clean up unnecessary constructor calls in Python code. It usually works great — things like wrapping a plain integer literal in the integer constructor get simplified to just the literal. But I'm running into some cases where the fix it applies is actually broken.

When the constructor call wraps a number with a positive or negative sign, and that expression appears in certain positions — like as the left operand of an exponentiation, as the function being called, as the thing being subscripted, or after an await keyword — the generated fix either creates a syntax error or changes what the code means. For example, applying the fix to something like a negatively-signed number in an exponentiation can produce code with subtly wrong operator precedence, and some combinations seem to create outright syntax errors.

There's also a related issue: if the constructor call being simplified contains an inline comment between the parentheses, the fix is applied as if it were fully safe, but it actually drops that comment silently. That kind of fix should be flagged as unsafe.

I'd expect the linter to add parentheses around the replacement whenever necessary to preserve correct meaning and valid syntax, and to mark fixes as unsafe whenever they would silently remove inline comments.
