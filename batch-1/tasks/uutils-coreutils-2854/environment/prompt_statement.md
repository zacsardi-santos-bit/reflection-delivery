I've found a bug in the ls implementation where error messages for broken symbolic links are being printed twice under certain flag combinations. When I list a directory containing a dangling symlink using inode display, recursive mode, and symlink dereferencing all together, the error about the inaccessible link target appears duplicated in the error output. Each problem should only be reported once.

Related to this, when I use inode display together with symlink dereferencing on a directory that has a broken symlink, the inode column for that broken link should show a question mark to indicate the inode couldn't be retrieved, rather than generating additional errors or showing incorrect output.

Could you fix the ls implementation so that errors for dangling symlinks are reported exactly once (no double-printing), and so that the inode display correctly shows a question mark for symlinks whose target metadata is unavailable?
