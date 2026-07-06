## Description

Gleam source files that begin with a Unicode Byte Order Mark fail to compile, even when the rest of the file is perfectly valid Gleam code. Many editors and tools — particularly on Windows — automatically insert this invisible marker at the start of text files to signal their encoding. Developers using such tools have no way to prevent this behavior, which makes their Gleam files uncompilable without a manual workaround.

## Expected Behavior

- A source file that starts with a Byte Order Mark should compile successfully, as though the marker were not there.
- The marker should be silently ignored at the start of any source input, whether it is a single expression or a full module.
- No parse error should be raised simply because a file begins with this encoding marker.

## Why This Matters

This is a common cross-platform compatibility issue. On Windows, many popular text editors save files with this prefix by default. Gleam developers on Windows (or collaborating with Windows users) should not have to fight their editor's default settings just to write valid Gleam code. Accepting and silently ignoring this marker is standard practice in most modern language parsers.
