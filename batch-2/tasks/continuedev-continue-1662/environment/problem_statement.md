## Description

The codebase currently relies on a built-in IDE method to list workspace files, but this method has limited support for ignore patterns and directory traversal. We need a standalone, testable directory traversal utility that correctly handles all common ignore file patterns found in real projects.

Additionally, the code chunking feature doesn't properly handle structured code files — when a file contains multiple classes and functions, they should each appear as their own independent chunk rather than being lumped together or split arbitrarily.

## Expected Behavior

**Directory traversal:**
- A new directory traversal utility should walk a directory recursively and return file paths
- It should read and respect ignore files (such as standard version-control ignore files) at every directory level, not just the root
- It must handle negation patterns, wildcard patterns, directory-level patterns, and root-anchored patterns correctly
- Ignore files in subdirectories should apply only to their own subtree, not to parent directories
- It should also respect a tool-specific ignore file in addition to the standard one
- It should support returning either relative or absolute paths
- It should support a mode that returns only directory paths instead of files

**Code chunking:**
- When a file is empty, no chunks should be returned
- When a file fits within the chunk size limit, the entire file should be one chunk
- Each top-level code structure (class, function) in a structured file should appear as its own chunk when they fit within the size limit
- When a class is too large, it should be represented both as a summary chunk (with placeholder content for method bodies) and as individual method chunks

## Why This Matters

Without proper ignore file support, the tool may index files that developers have explicitly excluded, leading to noise in search results and wasted processing. The code chunking improvements ensure that semantic code units are preserved as meaningful retrieval units rather than being cut at arbitrary line boundaries.
