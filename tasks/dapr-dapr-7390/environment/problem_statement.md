## Description

There are two issues to address in this PR:

1. **Internal API struct field visibility**: The universal API layer exposes several internal fields as public (exported) struct fields that should be private. These fields — the logger, the resiliency policy handler, and the component store — are implementation details and should not be part of the public API surface of the struct. Making them private enforces proper encapsulation and prevents consumers from directly accessing or modifying these internals.

2. **Case-sensitive environment variable filtering in secret stores**: The mechanism that determines whether a given environment variable is allowed to be accessed from a secret store performs only case-sensitive comparisons. This means that security-critical restrictions — such as denying access to keys starting with a reserved prefix or matching a sensitive token name — can be trivially bypassed by using a different casing. For example, a key that should be blocked is not blocked if it is provided in all lowercase. This is a security concern.

## Expected Behavior

- The internal fields of the universal API struct should be made private so they are not directly accessible from outside the package.
- Environment variable name filtering must be case-insensitive. Keys matching reserved prefixes or sensitive names should be denied regardless of whether they are uppercase, lowercase, or mixed-case.
- Allow-listed environment variable names should also be matched in a case-insensitive manner so that a key is allowed even if its casing differs from the allowlist entry.

## Why This Matters

These changes improve both code correctness and security. Hiding internal fields reduces the risk of accidental misuse by consumers of the API. Case-insensitive environment variable filtering prevents a bypass where attackers or misconfigured components could access sensitive runtime tokens or internal configuration by simply using a lowercase version of a blocked variable name.
