## Description

When context files are loaded by the memory discovery system, each file's content is wrapped in markers that identify where the content came from. Currently these markers show paths relative to the current working directory, which means the same file appears under a different label depending on where the tool is invoked. This inconsistency makes it hard to trace which file contributed what content.

## Expected Behavior

- The "Context from" and "End of Context from" markers in memory content should always display the full absolute path to each context file, not a relative path.
- This should apply consistently to all types of context files: global context files, project-level context files discovered by upward or downward directory traversal, extension context files, custom-named context files, and context files from included directories.
- The behavior should be consistent regardless of the working directory from which the tool is invoked.

## Why This Matters

Using relative paths in context markers is ambiguous — the same file will be shown with different labels in different environments or when invoked from different directories. Absolute paths make it immediately clear which file contributed each piece of context, simplifying debugging and understanding of how the AI's memory is being populated.
