## Description

When using two linting rules together — one that flags imports outside the module top level, and another that bans certain modules from being imported at the top level — the linter can produce contradictory and irreconcilable warnings for the same import statement.

Specifically: if a module is configured as "banned at the top level" (because it should be loaded lazily for performance or correctness reasons), and that module is imported inside a function or class body, the linter simultaneously tells the developer:

- "This import should be at the top level of the file"
- "This module must not be imported at the top level"

There is no way to satisfy both rules at the same time. This creates noise and forces developers to suppress one of the rules entirely.

## Expected Behavior

- When an import appears inside a function or class body, and **all** of the imported modules or names are configured as banned from top-level use, the "import should be at the top level" warning should be suppressed — because moving it there would immediately trigger the other rule.
- If an import statement mixes both banned and non-banned names, the warning should still be raised, since the non-banned names could legitimately be moved to the top level.

## Why This Matters

This false-positive conflict makes it impractical to enable both rules simultaneously, even though they serve complementary purposes. Fixing the interaction lets users enforce both lazy-loading requirements and top-level import hygiene without contradictions.
