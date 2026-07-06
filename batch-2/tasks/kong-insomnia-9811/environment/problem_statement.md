## Description

When syncing routes from the Kong managed gateway platform into an Insomnia workspace, routes that use pattern-based path matching (regular expressions) are not being converted into a usable format. The raw expression syntax ends up in the request URL, making those requests effectively unusable for actual API testing. Developers have no way to fill in the variable parts of the path.

At the same time, the gateway control plane publishes proxy endpoint information that could be used to automatically populate environment variables, but the sync always creates those variables empty and forces users to look up and manually enter the values. On the other hand, once a user has filled in these values, subsequent syncs should never overwrite them.

## Expected Behavior

- Routes whose paths contain named capture group patterns should be translated into standard parameterized URLs with the capture group names becoming path parameters the user can fill in directly.
- Routes whose paths use more complex regex syntax that cannot be cleanly parsed should fall back to a generic parameterized URL (with an appropriate path parameter placeholder), and the original pattern should be preserved in the request name for reference.
- When the gateway control plane provides proxy URL information, the sync should use it to pre-populate the proxy host environment variables instead of leaving them blank.
- If a user has already filled in a proxy host value manually, the sync must leave that value alone, even if the gateway provides a different one.
- When syncing multiple times, user-entered path parameter values must be preserved as long as the underlying route path definition has not changed.

## Why This Matters

Without this improvement, developers syncing routes with pattern-based paths get requests they cannot immediately use — they must manually edit the URL to figure out what parameters to use. Pre-filling proxy host variables from available gateway metadata removes a friction point that previously required developers to look up connection information from a separate source.
