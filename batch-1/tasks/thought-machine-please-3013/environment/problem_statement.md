# Lexer Does Not Support Modern Octal Literal Notation

## Description

The build system uses a Python-like scripting language for build configuration files. Python supports two ways to write octal integers: the modern style (a zero followed by the letter "o" and then octal digits) and the legacy style (just a leading zero followed by octal digits). The lexer currently does not handle the modern octal notation, so any build configuration file that uses it will fail to parse.

This is especially relevant when build files need to specify file permission modes or other numeric values that are naturally written in octal. Without support for the modern notation, authors cannot use it in their build files even though it is standard Python syntax.

## Expected Behavior

- Octal literals written in the modern style should be recognized as valid integer tokens.
- The integer value produced should contain only the numeric digits (without the prefix characters).
- Both the modern notation style and the legacy notation style should work correctly.

## Why This Matters

The build system's configuration language aims to be compatible with a subset of Python. Failing to parse valid Python octal literals means configuration files that use this notation will be rejected with a parse error, which is unexpected and confusing for users familiar with Python syntax.
