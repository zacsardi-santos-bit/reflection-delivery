I'm working on adding data integrity verification to the incremental snapshotting process in rqlite.

*   The WriteCRC32SumFile function must accept a third parameter of type SyncState, in addition to the file path and checksum value. When the SyncState parameter indicates sync, the function must flush (fsync) the written file to disk before returning.

*   A new SyncState boolean type must be defined in the internal/rsum package, with two exported package-level variables: Sync (true) indicating that the file should be synced, and NoSync (false) indicating that it should not.

*   A new CompareCRC32SumFile function must be added to the internal/rsum package. It must accept two file paths: the data file and the CRC file. It must return (true, nil) when the CRC32 of the data file matches the checksum stored in the CRC file, (false, nil) when they do not match (without returning an error), and (false, non-nil error) when either file is missing or cannot be read.

*   When closing an incremental file sink, each WAL file in the source directory must have a corresponding checksum file with the '.crc32' suffix alongside it. If any WAL file is missing its checksum file, Close() must return a non-nil error.

*   When closing an incremental file sink, the CRC32 checksum of each WAL file must be verified against its companion '.crc32' checksum file. If the checksum of any WAL file does not match the stored value, Close() must return a non-nil error.

*   An unexported package-level constant named crcSuffix with value '.crc32' must be defined in the snapshot package and must be accessible to all files in that package.


*   Interface details: Type: Type
Name: SyncState
Location: internal/rsum/crc.go
Description: A boolean type that controls whether a file should be synced (fsynced) to disk after writing.

Type: Variable
Name: Sync
Location: internal/rsum/crc.go
Signature: var Sync SyncState = true
Description: Exported package-level variable indicating the file should be synced after writing.

Type: Variable
Name: NoSync
Location: internal/rsum/crc.go
Signature: var NoSync SyncState = false
Description: Exported package-level variable indicating the file should not be synced after writing.

Type: Function
Name: WriteCRC32SumFile
Location: internal/rsum/crc.go
Signature: WriteCRC32SumFile(path string, sum uint32, sync SyncState) error
Description: Writes a CRC32 checksum to the given path as an 8-character lowercase hex string. The third parameter controls whether the file is fsynced to disk after writing.

Type: Function
Name: CompareCRC32SumFile
Location: internal/rsum/crc.go
Signature: CompareCRC32SumFile(dataPath, crcPath string) (bool, error)
Description: Computes the CRC32 checksum of the file at dataPath and compares it to the expected checksum read from crcPath. Returns (true, nil) if they match, (false, nil) if they do not match, or (false, error) if either file cannot be read or does not exist.

Type: Constant
Name: crcSuffix
Location: snapshot/store.go
Signature: const crcSuffix = ".crc32"
Description: Unexported package-level constant in the snapshot package representing the file extension for CRC32 checksum files. Used by both sink.go and sink_test.go within the same package.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.