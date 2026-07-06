I'm working on a Go-based template language parser and I need help extending it to handle more real-world patterns.

Right now, if I try to use a component stored in a slice or map and call it by index inside a template element expression, the parser fails. For example, selecting a template function from a slice or calling a method on a struct retrieved by index from a slice should both be valid, but currently aren't supported.

I also need template parameter lists to be able to span multiple lines — right now the parser only handles parameters on a single line, which gets unwieldy for templates with many parameters.

Similarly, string expressions embedded in templates should support being written across multiple lines, but currently they can't.

Beyond those parser fixes, I need a new package that provides functions to extract the Go expression portion from control-flow statements in template source. Specifically, I need functions that can take a string (like the source starting from an "if", "for", "switch", "case", or "default" keyword with surrounding template body content) and return the byte offsets of just the expression part within that string. I also need a utility function that extracts argument list content as a string. These are all needed for more accurate template source processing.

Finally, when the parser encounters an unterminated statement (like an unclosed comment or a control-flow statement with a missing opening brace), the error position it reports should be normalized to the start of the input rather than pointing to the end of what was consumed.
