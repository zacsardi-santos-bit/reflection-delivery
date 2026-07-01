## Description

There are two related security/correctness issues with how cargo unpacks registry packages:

1. **Lock file symlink attack**: If a published crate contains a file that matches the name of cargo's internal extraction lock file (used to track whether a package has been successfully unpacked), and that file is a symlink pointing to one of the crate's own source files, cargo's write to the lock file will follow the symlink and silently corrupt the source file it points to. The lock file should always be created fresh by cargo itself, not extracted from the tarball.

2. **No decompressed-size limit**: When cargo unpacks a compressed registry package, there is currently no limit on how much decompressed data can be read. A maliciously crafted or corrupted package could contain an archive that decompresses to an arbitrarily large amount of data, exhausting disk space or memory without any error or warning.

## Expected Behavior

- When extracting a registry package, any entry in the tarball whose name matches the extraction lock file should be skipped — it must never be extracted from the package.
- The extraction lock file should be opened with an exclusive-create operation, so that it fails rather than silently following a pre-existing symlink.
- After a successful build of a package, the lock file must contain exactly 2 bytes and the package's source files must be intact.
- When unpacking a package, a maximum decompressed-data limit must be enforced. If unpacking would exceed this limit, the operation must abort with a clear, structured error indicating which package triggered the limit and why.

## Why This Matters

A crate author could publish a package that includes a specially named symlink, which upon installation would corrupt a user's local file via cargo's lock file write. Similarly, unbounded decompression means a small compressed package could expand into gigabytes of data. Both issues need to be addressed to make registry package unpacking safe.
