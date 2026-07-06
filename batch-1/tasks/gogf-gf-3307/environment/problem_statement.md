## Description

The DAO code generation tool has two related issues when using a project configuration file with multiple generation entries that have different per-table overwrite settings.

**Issue 1: File path reporting bug**

When the overwrite setting is disabled for a table and a DAO index file already exists, the generator skips writing that file (as expected), but it also fails to record the file path in the list of generated files. This causes the reported count of generated files to be lower than the actual number of files managed by the tool, which breaks code that relies on the complete list of generated file paths.

**Issue 2: Per-table overwrite configuration not respected**

When a project configuration file contains multiple DAO generation entries — each targeting different tables with different overwrite settings — the per-table overwrite setting needs to be honored independently for each entry. One table's DAO index file should be preserved if its entry has overwriting disabled, while another table's DAO index file should be regenerated if its entry has overwriting enabled.

## Expected Behavior

- The list of generated (or managed) file paths must always include the dao index file path for every table, even when the existing file is not being overwritten.
- When a table's configuration says not to overwrite the existing dao index file, the file's current contents must be fully preserved.
- When a table's configuration says to overwrite the existing dao index file, the file should be regenerated fresh.

## Why This Matters

Projects often customize their DAO index files with hand-written code and do not want these customizations erased during regeneration. Supporting per-table overwrite control in the configuration file gives developers fine-grained control. The file path reporting bug makes it impossible to accurately track which files the tool is managing.
