## Description

The codebase has test rendering helper functions duplicated across many individual test files, and a native library mock is declared redundantly in each test file that needs it. This creates maintenance overhead and inconsistency — each team member who writes a new test must copy-paste these boilerplate helpers, and if the underlying setup changes, all copies need to be updated individually.

## Expected Behavior

- A single shared file should provide centralized test rendering utilities that wrap components with the internationalization provider, the Redux store provider, or both — with sensible defaults.
- Individual test files should be able to import these helpers directly instead of defining their own local versions.
- The native image picker library mock should be declared once in the global test setup so it is available to all tests automatically, without each test file needing to redeclare it.

## Why This Matters

Centralizing test utilities eliminates duplicated setup code, reduces the risk of inconsistencies between test files, and makes it easier for contributors to write new tests following a consistent pattern. Removing the per-file mock declarations means that adding or modifying the mock only requires changing one place, reducing the chance of tests breaking due to divergence.
