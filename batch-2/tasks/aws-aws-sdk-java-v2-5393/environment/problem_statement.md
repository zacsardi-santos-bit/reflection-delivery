## Description

The V1-to-V2 migration tool does not handle two common enum-related code patterns that appear across many AWS services. When migrating code automatically, these patterns are left unchanged, causing compilation errors in the migrated output.

**Problem 1 — Enum constant naming convention:** The older SDK uses a mixed-case style for enum constants, whereas the newer SDK uses an all-uppercase style with underscores. The migration tool changes the type imports but does not update the constant names to match the new convention, resulting in references to constants that do not exist in the new SDK.

**Problem 2 — Enum-returning getter methods:** The older SDK provides getter methods that return enum types directly. The newer SDK replaces these with string-returning variants following a predictable naming convention. For single-value enum fields, the getter is renamed with a string suffix. For list-valued enum fields, the getter is renamed with a plural string suffix. The migration tool does not rewrite these method calls, so the migrated code fails to compile.

## Expected Behavior

- Enum constant references should be automatically renamed from mixed-case to all-uppercase-with-underscores when migrating from V1 to V2.
- Enum-returning getter calls on model objects should be automatically rewritten to their string-returning equivalents.
- The migration should cover these patterns for at least the SQS, SNS, and DynamoDB service models.
- The migration integration tests should correctly apply version substitution to all relevant project files before building.

## Why This Matters

Without these transformations, migrated code will not compile even after the migration tool has run, requiring significant manual effort to fix every enum reference and enum getter call in a large codebase. Adding these automated transformations makes the migration tool produce correct, compilable V2 code in more cases.
