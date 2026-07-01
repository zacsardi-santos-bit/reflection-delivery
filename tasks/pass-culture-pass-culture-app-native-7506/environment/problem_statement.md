## Description

Our app's navigation tab bar went through a visual redesign that was rolled out behind a feature flag so we could test it gradually. That redesign is now stable and ready to become the permanent default for all users. We should remove the old tab bar redesign feature flag from all navigation components so the updated design is always shown.

The problem is that several navigation components still check for this old flag and only apply the updated design when it is explicitly enabled. This creates a confusing situation: when testing with only the reactions feature flag enabled, the navigation components don't render correctly — they fall back to the old visual design because the companion tab bar flag is missing. Tests and workflows have to enable two separate flags at once just to get a correct rendering, which is an unnecessary burden.

## Expected Behavior

- The updated tab bar design should always be applied, with no feature flag required to activate it
- When the reactions feature flag is enabled, the notification badge count (e.g., '99+') should appear correctly in both the tab bar and the web header navigation
- The favorites tab should be visible in the header navigation whenever the reactions feature flag is active, without needing a separate tab bar flag co-enabled
- Removing the old feature flag entry from the codebase entirely so it can't accidentally be referenced

## Why This Matters

Keeping a redundant feature flag in the codebase adds maintenance overhead and makes it easy for developers to forget to enable it during testing. By removing it and making the updated design the permanent default, we simplify the configuration surface and eliminate a confusing multi-flag dependency.
