I'm working with a build system that uses a Python-like language for its configuration files. I've noticed that the lexer doesn't support the modern Python-style octal literal notation — the one that uses a zero followed by the letter "o" before the octal digits. When I try to use this notation in a build file (for something like a file permission mode), the parser fails to recognize it as a valid integer.

The lexer should treat these modern-style octal literals as integer tokens. The token value should be the digit portion starting from the initial zero, with the letter indicator dropped — that is, the "o" character after the leading zero should not appear in the resulting token value. The older-style octal notation (just a leading zero before the digits, without the letter indicator) already works and should continue to do so.

Could you add support for the modern octal notation to the lexer?
