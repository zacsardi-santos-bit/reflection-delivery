## Description

When using Argo CD's multi-source application feature, Helm applications can reference value files from external repositories using a special ref-name prefix syntax in the file path. However, the application details endpoint does not currently support this pattern — it cannot resolve or read value files that live in external referenced repositories. As a result, the Helm parameters shown in the application details view are incorrect or incomplete for multi-source Helm applications.

## Expected Behavior

- When fetching Helm application details, external repositories declared as ref sources should be checked out at the correct revision so their value files can be read.
- The application details must show the correct Helm parameters sourced from those external value files.
- Ref sources that are declared but not actually referenced in any value files should be ignored and not checked out.
- If a referenced repository's revision conflicts with the main application's revision of the same repository, an appropriate error must be returned.
- If two ref sources reference the same repository at different revisions, an appropriate error must be returned.
- If a referenced repository's revision cannot be resolved, an appropriate error must be returned.

## Why This Matters

Without this fix, multi-source Helm applications that keep their value files in a separate repository cannot display accurate parameter information in the Argo CD UI. Operators relying on the application details view to inspect configuration will see missing or wrong values, making it impossible to reason about the effective Helm configuration for these applications.
