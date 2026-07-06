## Description

When the persistent task tracking feature is enabled, the AI assistant is given a system prompt that references the location of the task storage directory. Currently, this location is a hardcoded relative path that is the same for every project and every user — it never reflects the actual directory where task files are stored on the user's machine.

This means the AI cannot tell users where their task tracker data actually lives, and any attempt to reference or navigate to the storage location will be incorrect for most setups.

## Expected Behavior

- When the task tracker feature is active, the system prompt should dynamically include the actual absolute path to the task tracker storage directory for the current project.
- The real storage path should be retrieved from the project configuration rather than using a fixed placeholder.
- The storage path should be sanitized before inclusion to prevent directory names containing newlines or special bracket characters from corrupting the system instructions.

## Why This Matters

Users who ask the AI where their task tracking data is stored — or who rely on the AI to reference that location — currently receive a misleading generic path. With the dynamic path, the AI always knows the true storage location and can give users accurate information about where their task files live.
