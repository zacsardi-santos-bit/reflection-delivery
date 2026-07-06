## Description

The workspace routing and navigation system has several gaps that cause a frustrating experience when switching between or navigating to workspaces.

**Unwanted redirects when the workspace list is unavailable.** When a user navigates to a URL that includes a workspace identifier, the router sometimes redirects them away — even when the workspace list is still loading or failed to fetch. A valid workspace in the URL should always be honored, not thrown away just because the list didn't return it yet.

**No way to manually enter a workspace name.** The workspace selector only lets users pick from the fetched list. If a user knows the name of a workspace they want to access, they should be able to type it directly and navigate there — even if it doesn't appear in the dropdown.

**Stale workspace state persists after the workspace is deleted.** When a user's previously remembered workspace no longer exists, the home page should detect this, show a clear error explaining that the workspace was not found, and clean up the stale workspace from memory. Currently, no such error page exists.

**Workspace name length limits are not enforced.** Workspace names shorter than 2 characters or 64 or more characters long are technically invalid but currently pass client-side validation.

**Workspace-prefixed links in error pages may be unwanted.** Error views that link back to the home page always apply workspace prefixing, but in some cases (such as the workspace-not-found error page) the link should go to the plain root path.

## Expected Behavior

- A workspace name in the URL should be preserved as the active workspace without redirecting, regardless of whether the workspace list has loaded or returned an error.
- The workspace selector should allow typing an arbitrary valid workspace name and navigating to it directly.
- The home page should detect when the current workspace no longer exists on the server, display a "Page Not Found" message with a link back to the root, and clear the remembered workspace.
- Workspace name validation should reject names shorter than 2 characters and names 64 or more characters long.
- Error view fallback links should support an option to skip workspace URL prefixing.

## Why This Matters

These issues cause users to get stuck in redirect loops, be unable to access workspaces they know exist, and see stale or incorrect workspace context after a workspace is removed.
