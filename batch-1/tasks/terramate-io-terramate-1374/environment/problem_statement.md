## Description

The code generation system supports two types of generation blocks: one for generating HCL content and one for generating arbitrary file content. The HCL-type generation block already supports a stack filtering feature that lets users restrict code generation to only stacks matching certain path patterns. However, the file-type generation block does not have this capability, which creates an inconsistency in the system.

Users who want to generate plain files only for a specific subset of stacks (for example, only stacks under a particular directory path) have no built-in way to do this using file-type generation blocks. They are forced to rely on workarounds like conditional expressions or separate configuration files per directory.

## Expected Behavior

- The file-type generation block should support the same stack filtering mechanism that the HCL-type generation block already has.
- Users should be able to specify one or more filter blocks, each containing path pattern lists, to control which stacks receive the generated file.
- Multiple path patterns within a single filter block should all have to match (AND logic).
- Multiple filter blocks should match if any one of them is satisfied (OR logic).
- Glob patterns should be supported, including wildcards for single path segments, multi-segment wildcards, and root-anchored patterns.
- Using a filter block in combination with a non-stack generation context should result in a schema validation error, since filters are stack-specific.

## Why This Matters

Without this feature, users cannot declaratively scope file-type code generation to specific stacks based on path patterns. Adding this capability makes the two generation block types consistent and gives users a clean, declarative way to control which stacks receive generated files.
