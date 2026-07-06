I've noticed that when my context files are loaded, the headers and footers that wrap each file's content show relative paths instead of absolute paths.

*   When generating memory content, context file markers must use the absolute file path of each context file, not a path relative to the current working directory. This applies to all memory source categories: global, project, extension, and any custom or included directory context files.

*   The format for each context file block in memory content must be: '--- Context from: <absolute_path> ---' followed by the file content, followed by '--- End of Context from: <absolute_path> ---', where <absolute_path> is the full absolute path to the context file.

*   The context discovery mechanism must produce memory content strings containing absolute paths in context markers, consistent across all scenarios including upward traversal, downward traversal, global context, custom filenames, extension context files, and memory from included directories.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.