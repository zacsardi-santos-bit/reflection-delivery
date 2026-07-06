I'm running into a crash in the file discovery service when I specify certain custom ignore file paths.

*   FileDiscoveryService.getIgnoreFilePaths() must filter out paths that resolve to directories on disk. When customIgnoreFilePaths contains an entry that matches a directory name, that directory's resolved absolute path must NOT be included in the returned array.

*   FileDiscoveryService.getAllIgnoreFilePaths() must also filter out directory paths from customIgnoreFilePaths. If a customIgnoreFilePaths entry resolves to a directory, it must be excluded. Regular ignore files that exist as files (such as .gitignore) must still be included in the returned array.

*   Instantiating FileDiscoveryService with customIgnoreFilePaths entries that are directory names — including entries with trailing slashes — must not throw an error, even when some of those directories exist on disk and some do not.


*   Interface details: Type: Class
Name: FileDiscoveryService
Location: packages/core/src/services/fileDiscoveryService.ts
Description: Service for discovering and filtering files within a project, with support for custom ignore file paths via the customIgnoreFilePaths option.
Signature:
  constructor(projectRoot: string, options?: { customIgnoreFilePaths?: string[], ... })
  getIgnoreFilePaths(): string[]   — returns paths to custom ignore files (excluding directory entries)
  getAllIgnoreFilePaths(): string[] — returns all ignore file paths including .gitignore (excluding directory entries from customIgnoreFilePaths)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.