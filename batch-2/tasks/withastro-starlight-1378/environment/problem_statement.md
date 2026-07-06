## Description

The Starlight Tailwind integration's generated stylesheet includes a default font family variable for sans-serif text. This font family list is based on the underlying Tailwind CSS library's default fonts. A newer version of Tailwind CSS simplified the default sans-serif font stack — removing a long list of OS-specific font name fallbacks in favor of relying on modern browser support for system font keywords, resulting in a shorter, cleaner list.

Currently, the integration still reflects the older, longer font stack. The library dependency needs to be updated so that the generated stylesheets use the new, simplified font family list from the updated Tailwind CSS release.

## Expected Behavior

- The generated CSS should include the updated, shorter sans-serif font family: starting with the system font keywords and followed only by emoji fallbacks.
- The old list of explicit OS-specific font names (targeting specific browser rendering engines on Windows, macOS, and older Android) should no longer appear in the output.
- This should be consistent regardless of whether Tailwind's built-in CSS reset is enabled or disabled.
- All other generated CSS custom properties (monospace font, color variables) should remain unchanged.

## Why This Matters

Keeping dependencies up to date ensures the Starlight integration correctly reflects the behavior and output of the version of Tailwind CSS it ships with. Users expect the generated CSS to match what the current Tailwind release produces for default font settings. Stale snapshots and mismatched output can confuse contributors and signal that the integration has drifted from its dependencies.
