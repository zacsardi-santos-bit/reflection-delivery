## Description

When Storybook starts, external tools and AI-powered workflows have no reliable way to discover which Storybook instances are currently running, what URLs they are accessible at, which version is in use, or whether an AI integration addon is active and on which endpoint. There is currently no shared registry on disk that tracks this runtime metadata.

## Expected Behavior

- A utility module should be created that writes a small JSON record to a configurable directory when a Storybook instance starts up.
- Each record should capture the instance's origin URL (without navigation path parameters), port, process ID, working directory, version, and information about whether the AI integration addon is installed and which endpoint it exposes.
- If the AI integration addon is not in the project's configuration, the record should reflect that it is not installed.
- If the AI integration addon is configured with a custom endpoint, that endpoint should be recorded; otherwise a default endpoint value should be used.
- The record should be written atomically (via a temporary file and rename) to avoid partial reads by other processes.
- When a Storybook instance stops, it should be possible to clean up (delete) the record it wrote.
- A higher-level helper should handle the full lifecycle: creating the record, writing it, and returning both the path and a cleanup function.

## Why This Matters

Without this registry, external tools — such as AI coding assistants — cannot discover running Storybook instances or determine whether the AI integration addon is available, which prevents automated workflows from connecting to live Storybook servers.
