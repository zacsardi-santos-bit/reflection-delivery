Implement stack filtering for the plain file generation block in the code generation system to match the existing HCL-type generation block capabilities. Allow users to specify path pattern filters to control which stacks receive generated files, ensuring consistency and flexibility in stack-specific file generation.

*   Update the `GenFileBlock` struct in `hcl/hcl.go`:
    *   Add a `StackFilters` field of type `[]StackFilterConfig` to hold parsed stack filter configurations.

*   Modify the `validateGenerateFileBlock` function in `hcl/hcl.go`:
    *   Include "stack_filter" as a valid sub-block type within a `generate_file` block.

*   Update the `parseGenerateFileBlock` function in `hcl/hcl.go`:
    *   Handle "stack_filter" sub-blocks within a `generate_file` block.
    *   Reject `stack_filter` when the block's `context` attribute is "root" with an `ErrTerramateSchema` error.
    *   Populate `GenFileBlock.StackFilters` with parsed `StackFilterConfig` values.

*   Enhance the `Load` function in `generate/genfile/genfile.go`:
    *   Apply stack filter logic when loading `generate_file` blocks.
    *   Ensure a stack matches at least one filter block (OR logic) if `StackFilters` is non-empty.
    *   Within each filter block, ensure all specified path matchers match (AND logic).
    *   Record a `File` with `condition=false` and skip generation for stacks that do not match.

*   Ensure glob pattern support:
    *   Match `project_paths` against the stack's project-relative directory path.
    *   Support patterns: `*` for a single path segment, `**` for zero or more path segments, and leading `/` for root-anchored patterns.

*   Implement error handling for invalid configurations:
    *   Reject `generate_file` blocks where `project_paths` is not a list with an `hcl.ErrTerramateSchema` error.
    *   Reject `generate_file` blocks where `project_paths` contains non-string list elements with an `hcl.ErrTerramateSchema` error.
    *   Reject `generate_file` blocks with both `context = root` and a `stack_filter` sub-block with an `hcl.ErrTerramateSchema` error.

*   Export the `MatchAnyGlob` function from `hcl/hcl.go`:
    *   Signature: `MatchAnyGlob(globs []glob.Glob, s string) bool`
    *   Return true if the string `s` matches any of the provided glob patterns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.