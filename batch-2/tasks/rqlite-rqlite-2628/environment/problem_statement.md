## Description

The snapshot subsystem currently stores CRC checksums alongside data files using a low-level internal utility. This utility writes checksums in a format that is tightly coupled to its implementation and does not record which algorithm was used to produce the checksum. As a result, there is no self-describing record of the checksum type, making it harder to extend or evolve the format in the future.

## Expected Behavior

A new, dedicated package should be introduced to manage these companion checksum files. The companion files should be stored in a structured format (JSON) that records both the checksum value and the algorithm used. The package should expose functions to:

- Create a new in-memory checksum record for a given value
- Write a checksum record to a file on disk
- Read a checksum record back from a file
- Read just the CRC32 value from a checksum file
- Compare a data file's actual checksum against a stored checksum file, returning whether they match

Reading and writing must round-trip correctly — a value written to disk must be recoverable as the same value. Reading a file that does not exist or is not valid structured data should return an error. Comparing files where either the data file or the companion file is missing should also return an error.

The in-memory record should reject attempts to extract a CRC32 value if the algorithm type is unrecognized, or if the stored checksum string is not a properly formatted 8-character hexadecimal value (rejecting strings that are too short, too long, empty, or contain non-hex characters).

## Why This Matters

Having a self-describing checksum file format makes the snapshot integrity system more maintainable and future-proof. Callers across the snapshot subsystem should migrate to using this new package instead of calling lower-level checksum utilities directly.
