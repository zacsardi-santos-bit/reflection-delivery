I'm cleaning up the file system module our browser agents use internally, it's got a bunch of API inconsistencies that keep biting me, mostly around save/restore state across sessions being flaky.

First thing, on the file objects, size and line count are currently callable methods but they really should be plain read-only properties (Python idiom, computed values), so convert those. While you're in there, add a method to update file content in place, plus dedicated methods to sync a file's contents to disk in both async and sync variants, and dedicated async methods for writing new content and for appending that also handle the disk sync themselves.

For the file system container class, the constructor should accept both a string path and a path object for the base dir, oh and add a boolean flag controlling whether default files get created on init so callers can opt out. Expose the base directory and the data directory as direct attributes, not just via a method. Add a method to completely destroy the data directory and everything in it, and a method to query which file extensions are allowed.

The read-file op is async right now but it reads from an in-memory cache, not disk, so make it synchronous. Also I want exact, consistent return message strings across all the file operations.

When saving state, the serialized base directory path should point to the parent directory, not the data subdirectory (this is the round-trip bug). When restoring, any file entries whose type isn't recognized should be silently skipped rather than substituted with a default type, since substituting can mask corruption or version mismatches.

Extension parsing should normalize to lowercase so `.TXT` and `.txt` are the same. Filename validation should reject names with multiple extension separators, special characters, and empty base names. For the directory display, large files should show both the start and the end of their content with an indicator for the omitted middle, right now only the beginning shows so you can't see recent content. And finally export two constants directly from the module, one for the data subdirectory name and one for the invalid-filename error message.
