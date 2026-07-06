## Description

The extension loading system has two related problems that need to be fixed.

First, if extension loading is triggered from multiple places simultaneously (for example, during startup when multiple components initiate loading), the system starts independent loading operations for each caller instead of sharing a single in-progress load. This can cause duplicate work and inconsistent state. Concurrent callers should all participate in the same loading operation and receive the same result.

Second, the system does not currently detect when two different extension directories provide extensions that share the same name. When this happens, the conflict is silently ignored rather than raising an informative error.

There is also a related need: a method that currently loads a single extension from a given directory is internal-only and cannot be called from outside the class. Making it accessible would allow external callers (such as an install flow) to load a newly installed extension safely, coordinating with any in-progress bulk load.

## Expected Behavior

- When extension loading is initiated concurrently from multiple places, all concurrent calls join the same operation and receive an identical result.
- After extension loading has completed, attempting to load again raises a clear error indicating extensions have already been loaded.
- If the extensions directory is missing, loading completes successfully and returns no extensions.
- If two extension directories declare extensions with the same name, loading fails with a clear error identifying the conflicting name.
- A single-extension loader method is accessible externally and waits for any in-progress bulk load to finish before adding a new extension.

## Why This Matters

Without these fixes, concurrent initialization can lead to extensions being loaded multiple times or not at all. The duplicate-name detection prevents ambiguous configurations from silently passing through. Making the single-extension loader accessible allows the install flow to add extensions safely without racing against startup.
