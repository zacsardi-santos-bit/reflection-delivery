Implement distinct filenames for autoinclude configurations in infrastructure stacks by differentiating between unit and stack components. Ensure that the correct filename is used based on the component kind, and prevent stack-level autoinclude files from being merged into unit configurations.

*   Update the `hclparse` package:
    *   Expose a string constant `AutoIncludeStackFile` with the value `"terragrunt.autoinclude.stack.hcl"`.
    *   Define string constants `KindUnit` and `KindStack` with values `"unit"` and `"stack"`, respectively.
    *   Add a new exported function `AutoIncludeFileNameForKind(kind AutoIncludeKind) string`:
        *   Return `"terragrunt.autoinclude.hcl"` for `KindUnit`.
        *   Return `"terragrunt.autoinclude.stack.hcl"` for `KindStack`.
        *   Panic with the message `hclparse.AutoIncludeFileNameForKind: unknown kind %q (expected "unit" or "stack")` for any other kind.
    *   Ensure the `AutoIncludes` map in `ParseStackFile` results includes a `Kind` field:
        *   Set `Kind` to `KindStack` for stack blocks.
        *   Set `Kind` to `KindUnit` for unit blocks.
    *   Modify `GenerateAutoIncludeFile` to use the `Kind` field:
        *   Write to `"terragrunt.autoinclude.hcl"` for `KindUnit`.
        *   Write to `"terragrunt.autoinclude.stack.hcl"` for `KindStack`.
        *   Ensure no cross-writing between unit and stack filenames.

*   Update the `pkg/config` package:
    *   Expose a string constant `DefaultAutoIncludeStackFile` with the value `"terragrunt.autoinclude.stack.hcl"`.
    *   Ensure that when parsing a unit's configuration file, any sibling file named `"terragrunt.autoinclude.stack.hcl"` is not merged into the unit's configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.