## Description

Rails currently has no standardized way to deactivate and remove view reloaders once they have been created. When a reloader is created, it registers a hook with the view path system, but there is no corresponding mechanism to remove that hook later. This means stale hooks can accumulate in the registry even when the associated reloader is no longer needed, which can interfere with the reloading lifecycle.

## Expected Behavior

- A reloader should be creatable via a factory method that both instantiates the reloader and registers it with the view path registry in one step.
- Each reloader should expose its registered hook so that external code can inspect or manage it directly.
- Each reloader should support an explicit deactivation step that removes its hook from the view path registry. Deactivating the same reloader multiple times should be safe and produce no errors.
- A managed collection class for reloaders should be provided. Clearing the collection should deactivate all contained reloaders and empty the collection. Removing an individual reloader from the collection should also deactivate it. The collection should be safely enumerable.

## Why This Matters

Without a lifecycle management mechanism, reloader hooks can outlive their intended use and remain registered permanently. This makes it difficult to rebuild or refresh the reloader set (for example, during engine initialization or test teardown) without risking stale state. Adding explicit deactivation and a managed collection gives developers a clean way to manage the full lifecycle of view reloaders.
