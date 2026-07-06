## Description

Python's structural pattern matching syntax can bind variable names through several forms: simple captures, class-pattern sub-captures, star captures in sequence patterns, mapping remainder captures, and as-aliases. Currently, the naming convention rules that enforce lowercase variable names in function scope, disallow mixed-case names in class scope, and disallow mixed-case names in module/global scope do **not** inspect any of these bound names. This is a gap: a developer can introduce a non-conforming name via a pattern-matching clause with no warning from the linter, even though a regular assignment with the same name would be flagged.

## Expected Behavior

- In a function body, a pattern-matching clause that binds a non-lowercase name (through any supported pattern form) should be reported as a naming violation.
- Inside a class body, a pattern-matching clause that binds a mixed-case name should be reported as a naming violation for each bound identifier, including both the capture and the alias when an aliasing clause is present.
- At module/global scope, a pattern-matching clause that binds a mixed-case name should be reported similarly, including mapping remainder bindings and both sides of an aliasing pattern.
- Lowercase captures and the wildcard pattern should continue to be accepted without any violation.

## Why This Matters

Inconsistent naming enforcement makes it harder for teams to rely on the linter as a complete safeguard against convention violations. Developers who prefer pattern matching over traditional assignment should receive the same feedback as those who use classic assignment syntax.
