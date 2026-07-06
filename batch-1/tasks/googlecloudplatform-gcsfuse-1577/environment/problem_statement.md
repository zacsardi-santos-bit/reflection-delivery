## Description

The in-memory type cache used by directory inodes currently cannot be size-limited through configuration. There is no way for operators to control how many entries the cache holds, nor to disable it entirely without changing code. Additionally, the constants used to classify filesystem entry types (regular files, directories, symlinks, etc.) are defined in an internal layer that is tightly coupled to the inode implementation, making them unavailable for reuse in more general metadata utilities.

This issue tracks two related improvements:

1. **Configurable type cache size**: Introduce a configuration option (alongside the existing TTL setting) to set a maximum number of entries the type cache may hold. Setting this to zero should disable the cache entirely. Negative values should allow unlimited entries. The configuration field and its default value should be documented and validated, with a clear error returned for invalid values.

2. **Dedicated metadata type package**: Move the filesystem entry type constants into a shared metadata package so they can be referenced by both the inode layer and any cache or utility code. Two new entry types should be added: one for symbolic links and one for entries confirmed to be absent (supporting negative caching). The type cache should use this shared package.

## Expected Behavior

- When the configuration specifies a maximum entry count, the type cache should respect it, evicting the oldest entry when full.
- When the maximum entry count is zero, the type cache should be disabled regardless of TTL.
- When the maximum entry count is not set, a sensible default should be used.
- After every directory lookup, listing, or child creation/deletion operation, the type cache should be updated consistently with the observed type of each affected entry.
- Entry type constants (regular file, explicit dir, implicit dir, symlink, nonexistent) should be accessible from a shared metadata package.

## Why This Matters

Without a configurable entry limit, the type cache could consume unbounded memory in directories with many children. Operators need a knob to tune or disable caching independently from TTL.
