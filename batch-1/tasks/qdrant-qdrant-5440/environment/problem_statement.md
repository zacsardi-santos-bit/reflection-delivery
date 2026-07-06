## Description

The memory library currently supports typed wrappers over writable memory-mapped files, but there is no equivalent abstraction for read-only memory-mapped files treated as typed slices. Any code that tries to use a read-only mmap as a typed slice currently fails to compile because the necessary type simply does not exist.

## Expected Behavior

- It should be possible to convert a read-only memory-mapped file into a typed slice of any fixed-size element type.
- The length of the resulting typed slice should be automatically computed as the file size divided by the size of the element type.
- Converting a file whose size is a valid multiple of the element type's size should succeed; invalid sizes should return an error rather than causing undefined behavior.

## Why This Matters

Several higher-level components in the codebase need to hold a read-only view of the same memory-mapped data that another handle writes to. Without a read-only typed slice wrapper, these components cannot exist, making the entire memory crate fail to compile when such usage is attempted. Adding the read-only typed slice type unblocks compilation and enables efficient sequential-read patterns backed by memory-mapped files.
