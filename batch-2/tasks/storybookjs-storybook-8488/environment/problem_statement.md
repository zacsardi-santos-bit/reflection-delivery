## Description

Currently, the version update checker makes a live HTTP request to a remote server every time Storybook starts, in order to find out whether a newer version is available. This approach has several downsides: it adds startup latency, it requires a network connection, it introduces complex caching logic to avoid checking too frequently, and the results need to be stored permanently in local state between sessions.

We should instead fetch version information once at build/startup time on the server side, bake it into the application bundle, and have the client read it directly from that pre-fetched data. This eliminates the client-side network request entirely.

## Expected Behavior

- Version information (including the latest stable release and the latest pre-release) should be available to the client immediately from build-time data, without any runtime network calls.
- The state should include both the latest stable version and the next (pre-release) version from the build-time data, right from initialization — no need to wait for an async fetch.
- The version data written to the store should not require permanent persistence, since it is always freshly available from the bundle.

## Update Notification Logic Improvements

Along with this change, the update notification logic should be refined:

- **Patch-only updates should not trigger a notification.** If the only difference between the current version and the latest is a patch increment, users should not be notified. Only minor or major version updates warrant a notification.
- **Prerelease latest versions should not trigger a notification.** If the latest available version is itself a prerelease, users should not be notified regardless of how much higher the version number is.
- **Users on a prerelease build** should have their base version compared against the latest stable release, so they are correctly notified about a newer stable version but not misled by patch/prerelease comparisons.

## Why This Matters

This change simplifies the architecture, removes a runtime network dependency from the client, and makes update notifications less noisy by only surfacing meaningful upgrades.
