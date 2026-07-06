## Description

The lint rule that flags string literals used directly in exception raise statements also offers an automatic fix. The fix works by extracting the string into a local variable before the raise. However, the fix has a bug: it always generates a new local variable with the same name, regardless of whether that name is already in use in the current function scope.

If a function already has a variable with the same name that the fix would introduce, applying the fix silently overwrites (shadows) the existing variable. Code that used the original variable after the fix is applied may now reference the newly introduced variable instead, changing the program's behavior in a way that is hard to detect.

## Expected Behavior

- When the auto-fix for a "string literal in exception" violation is generated, it should first check whether the default variable name is already bound in the current scope.
- If the name is already taken, the fix should use an alternative name that does not conflict — for example, by appending a numeric suffix until a free name is found.
- The existing variable with the conflicting name must remain completely unchanged after the fix is applied.

## Example

A function that defines a variable and then raises an exception with a string literal inside a try block should receive a fix that uses a fresh, non-conflicting variable name rather than reusing the existing one.

## Why This Matters

Applying an auto-fix should never introduce a correctness issue. Silently shadowing an existing variable can lead to subtle bugs that are much harder to diagnose than the original style violation the fix was meant to address.
