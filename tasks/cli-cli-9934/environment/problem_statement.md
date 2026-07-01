## Description

The GitHub CLI supports installable extensions, but currently there is no mechanism to notify users when a newer version of an installed extension is available. Users have no way to learn about updates unless they manually run upgrade commands. We should add background update checking that can display notifications to users, similar to how the main CLI tool already handles update notifications for itself.

## Expected Behavior

- A dedicated function should check whether an extension has a newer release available, with a 24-hour rate limit to avoid excessive network checks
- Local extensions (installed from a local path rather than a remote repository) should never be checked for updates, since they don't have published releases
- When an extension is removed, any cached update state for that extension should also be cleaned up
- When an extension is reinstalled or newly installed, any stale update state (from a previous installation) should be cleared
- Users should be able to suppress extension update notifications independently from the main CLI tool's update notifications, using a separate opt-out mechanism
- CI environments and hosted development environments should automatically have extension update checks suppressed, just as the main CLI update checks are suppressed in those environments
- The function responsible for constructing the extension command should accept a pluggable update-check function, making it easier to swap out or customize the update-checking logic

## Why This Matters

Without update notifications, users of extensions may miss important fixes or new capabilities. The new mechanism adds visibility into available updates while respecting user preferences and CI/CD environments where notifications would be disruptive or meaningless. Keeping extension names unpadded in upgrade output also improves readability.
