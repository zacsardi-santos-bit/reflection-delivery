## Description

The repository migration tool currently supports two modes for supplying migration archives: it can generate them automatically from a GitHub Enterprise Server source, or accept pre-hosted archive URLs that point to externally accessible storage. However, there is no way for users who have already downloaded or prepared migration archives on their local machine to feed those files directly into a migration. Currently they would have to manually upload the archives to a hosting location and then provide the resulting URLs — an unnecessary extra step.

## Expected Behavior

- Users should be able to point the migrate-repo command at local archive files on disk (one for the git data archive and one for the metadata archive)
- When local paths are provided, the tool should upload those files to the configured blob storage (Azure, AWS, or GitHub Storage) automatically and proceed with the migration
- The tool should validate that both archive path options are always provided together — supplying only one of the two should produce a clear error message indicating both are required
- If a user mistakenly provides both a pre-hosted URL and a local path for the same archive, the tool should reject the combination with a clear error

## Why This Matters

Users who have pre-downloaded archives or who manage archives locally should have a streamlined path to migrate repositories without needing an intermediate hosting step. This reduces friction and makes the tool more flexible for offline or restricted environments.
