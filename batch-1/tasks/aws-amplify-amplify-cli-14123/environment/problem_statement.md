## Description

When migrating an Amplify Gen 1 project to Gen 2, the generated backend code currently hardcodes the Gen 1 environment name as a static value. This causes problems when the same generated code needs to be used across multiple environments (e.g., `dev`, `prod`, sandbox). Additionally, when running migrations in CI pipelines, there is no validation that the required environment name has been set, which leads to silent failures.

Similarly, the Gen 2 migration tooling currently inserts placeholder text instead of the actual GraphQL schema from the Gen 1 project. Developers are forced to manually copy their schema into the generated file, which is error-prone and adds friction to the migration process.

## Expected Behavior

- Generated backend configuration should read the Gen 1 environment name dynamically at runtime rather than using a hardcoded value.
- When running in a CI environment, if the environment name is not set, the generated code should throw a clear error message so the issue is immediately visible.
- When not in CI (i.e., local development), the generated code should fall back to a sensible default value.
- The storage bucket name in generated code should also use the dynamic environment name rather than a hardcoded string.
- The migration tooling should automatically read the actual GraphQL schema from the Gen 1 project directory, supporting both the single-file layout and the multi-file folder layout, and merge them into the generated output.
- If no schema can be found, a clear error should be raised.

## Why This Matters

These improvements reduce manual steps required after migration and make the generated code work correctly across all environments without modification. CI pipelines will fail fast and loudly when misconfigured rather than silently producing broken deployments.
