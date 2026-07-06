Implement support for local archive files in the `migrate-repo` command by adding two new hidden optional command-line options. Ensure that these options allow users to specify local file paths for git and metadata archives, which are then uploaded to the configured blob storage for migration.

*   Update `MigrateRepoCommand` class:
    *   Add two new hidden optional options: `--git-archive-path` and `--metadata-archive-path`.
    *   Ensure the total registered options count is exactly 26.

*   Modify `MigrateRepoCommandArgs` class:
    *   Add two new string properties: `GitArchivePath` and `MetadataArchivePath`.

*   Implement validation in `MigrateRepoCommandArgs.Validate()`:
    *   Throw `OctoshiftCliException` with the message 'you must provide both --git-archive-path --metadata-archive-path' if only one of the path options is provided.
    *   Throw `OctoshiftCliException` with the message '--git-archive-url and --git-archive-path may not be used together' if both URL and path are provided for the git archive.
    *   Throw `OctoshiftCliException` with the message '--metadata-archive-url and --metadata-archive-path may not be used together' if both URL and path are provided for the metadata archive.

*   Update `MigrateRepoCommandHandler.Handle()`:
    *   When both `GitArchivePath` and `MetadataArchivePath` are set, open each file using `FileSystemProvider.OpenRead()` and upload to Azure Blob Storage.
    *   Ensure the blob name ends with the original filename from the archive path.
    *   If the same file path is used for both archives, upload once and use the URL for both archives in the migration.

*   Ensure `FileSystemProvider.OpenRead()`:
    *   Returns a `Stream` to allow flexibility in testing with different stream implementations.

*   Add extension methods in the `OctoshiftCLI.Extensions` namespace:
    *   `ToBytes` on `string` to convert to a UTF-8 byte array.
    *   `GetString` on `byte[]` to convert to a UTF-8 string.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.