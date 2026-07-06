## Description

When a case clause matches a list and captures the tail as a variable, and that tail variable is then referenced inside the clause guard (for example, to construct a new list and compare it to something), the compiler silently drops the tail. The guard in the generated Erlang and JavaScript code evaluates as if the tail were empty, which produces completely wrong results at runtime.

## Expected Behavior

- When a list pattern captures its tail and the guard uses that tail to build a new list, the compiled Erlang output should include the tail variable inside the guard's list construction.
- The compiled JavaScript output should extract the captured tail into a local variable before evaluating the guard, and the guard should reference that variable when building the comparison list.
- At runtime, a case clause whose guard references the list tail should correctly accept or reject inputs based on the actual tail contents — not treat the tail as an empty list.

## Example

If a function accepts two lists, matches the first against a pattern that captures the tail, and guards on whether a newly constructed list (prepending fixed elements onto that tail) equals the second argument, then:

- Passing inputs where the constructed list does match the second argument should take the guarded branch (returning true).
- Passing inputs where it does not match should fall through to the default branch (returning false).

Currently, the guard always evaluates against an empty tail, so the match almost never works as intended.

## Why This Matters

This is a correctness bug: code that looks semantically correct compiles to code that behaves incorrectly. Developers relying on list tail variables inside clause guards will get silent wrong behavior with no compile-time warning.
