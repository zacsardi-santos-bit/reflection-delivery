## Description

Two related improvements are needed to how Task handles file discovery and configuration loading.

**Separate file search from directory resolution**

Currently, the function used to locate a task definition file also computes and returns the working directory for that task alongside the file path. These are two distinct concerns, and bundling them together makes it harder to handle edge cases where the caller wants different directory logic. The file-search function should be simplified to return only the resolved file path. A new, separate utility function should handle resolving the appropriate directory given the original inputs (the unresolved entrypoint, the resolved file path, and an optional explicit directory).

**Load and merge configuration from multiple locations**

Task currently reads its configuration from a single location. Users expect to be able to set global defaults (in an XDG config directory) that can be overridden at a project level. A new configuration loading function is needed that:

- Reads configuration from the XDG config directory if the XDG configuration home directory environment variable is set
- Walks up the filesystem from the current working directory, collecting any configuration files found in each directory
- Merges all discovered configurations so that more local files take precedence over more global ones — project-level settings override home directory settings, which override the XDG global config
- Returns nothing (cleanly, without error) when no configuration files are found anywhere

## Expected Behavior

- File search returns only the resolved path to the located file
- Directory resolution is a separate operation accepting the original entrypoint, resolved path, and optional dir
- Configuration loading merges all relevant config files with local-first priority
- Missing config files across all locations result in no configuration being returned, without an error
