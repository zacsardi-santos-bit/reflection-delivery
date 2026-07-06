I'm working on making a disk usage tool work correctly across platforms, and I've found a few issues with how paths are handled in the codebase.

The first problem is that the code uses hardcoded forward-slash characters throughout for path separation — splitting display names, counting directory depth, and more. This breaks things on Windows where backslash is the standard separator. These places need to be updated to use the platform's native path utilities instead.

The second problem is with how the tool decides if one path is an ancestor of another. Right now it's doing a string prefix check, which means a directory whose name happens to start with another directory's name would be incorrectly treated as a child of it. Those are sibling directories, not parent and child — they just share a name prefix. The check also doesn't handle cases where the same path is given with a trailing slash or a trailing current-directory component, so a path and that same path with a trailing slash might be treated as different directories when they should be considered the same.

The third issue is with path deduplication. The existing helper that strips trailing slashes from paths only handles a narrow set of cases. When users give paths with redundant separators or paths that include current-directory components in the middle, these should be recognized as equivalent to the normalized form, but currently they are not.

The fix should introduce a proper path normalization function (using the OS's standard path library) that handles repeated separators, interior current-directory components, and trailing slashes and dot segments. The parent-of check should be rewritten to use proper path hierarchy comparison rather than string prefix matching. The various places that split or analyze paths should also be updated to use platform-aware separator detection.

Some existing tests that depend on Unix-only commands also need to be marked to skip on Windows so they don't cause false failures on that platform.
