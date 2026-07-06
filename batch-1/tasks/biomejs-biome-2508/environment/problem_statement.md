## Description

TypeScript supports overloaded function and method signatures — the same name declared multiple times with different parameter types. When overloads for the same name are scattered throughout a type, class, interface, or module body with unrelated members in between, the code becomes harder to read and maintain. There is currently no lint rule in biome that enforces overload signatures to be grouped adjacent to each other.

## Expected Behavior

A new lint rule in the nursery category should detect when overloaded signatures for the same method or function name are not adjacent. The rule should:

- Report a warning when any two signatures for the same name are separated by one or more members with a different name
- Work across all relevant TypeScript declaration contexts: classes, interfaces, type aliases, namespace declarations, top-level exported functions, and function return type annotations
- Produce no diagnostic when all overloads for a given name are already grouped together consecutively

## Why This Matters

Enforcing adjacency of overload signatures makes code easier to understand at a glance and prevents accidental omissions where a developer adds a new overload far from the existing ones. This rule mirrors an equivalent rule from a popular TypeScript-focused linting plugin, making it easier for teams migrating from that toolchain to adopt biome.
