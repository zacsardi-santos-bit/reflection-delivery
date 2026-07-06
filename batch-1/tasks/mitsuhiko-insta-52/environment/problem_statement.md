## Replace inline snapshot indentation markers with regular whitespace

### Description

Inline snapshots currently use a special vertical ellipsis character (⋮) as a prefix on every line of multi-line snapshot content embedded directly in source code. This was a workaround for tracking indentation, but it produces visually cluttered snapshots that are awkward to read and edit. Standard text editors, formatters, and diff tools don't handle this character well, and it makes copy-pasting or manually editing snapshots unnecessarily difficult.

### Expected Behavior

- Multi-line inline snapshots stored directly in source code should use regular whitespace indentation aligned with the surrounding code, not a special Unicode prefix character.
- When the library reads back an inline snapshot, it should automatically strip the largest common rectangle of leading whitespace from all lines so the content compares correctly regardless of how deeply it is indented in the source file.
- Leading empty lines before the content and trailing whitespace at the end should be ignored during normalization.
- Existing snapshots that still use the old character-prefixed format should continue to work correctly, so users are not forced to migrate everything at once.
- When the library writes or updates an inline snapshot, it should produce the new space-indented format.
- All snapshot assertion variants (debug output, YAML, RON, YAML with redactions) must work consistently with the new format.

### Why This Matters

Snapshots are most useful when they are easy to read and maintain alongside the test code. Removing the special prefix character makes inline snapshots look like ordinary indented text that is consistent with the rest of the source file and compatible with standard editor tooling.
