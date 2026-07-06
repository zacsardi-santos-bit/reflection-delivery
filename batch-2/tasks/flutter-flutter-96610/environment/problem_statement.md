## Description

Flutter plugins written entirely in Dart can provide platform implementations directly (without a separate implementation package) for desktop platforms such as Linux, macOS, and Windows. On mobile and web, such "app-facing" plugins are automatically treated as their own default implementation and get resolved correctly. On desktop, however, this was deliberately disabled as a backward-compatibility measure, because enabling it for all existing plugins at once would have broken already-published packages that did not expect this behavior.

We now have a mechanism to opt in: plugins that declare a minimum Flutter framework version of 2.11 or later should be treated as their own default inline implementation on desktop, just like mobile plugins. Plugins that declare no Flutter SDK version requirement, or declare a minimum version older than 2.11, must continue to behave as before.

## Expected Behavior

- A desktop plugin with no declared minimum Flutter version is **not** auto-selected as its own default implementation (0 resolutions).
- A desktop plugin declaring a minimum Flutter version below 2.11 is **not** auto-selected (0 resolutions).
- A desktop plugin declaring a minimum Flutter version of 2.11 or higher **is** auto-selected as its own default implementation, producing one resolution per declared desktop platform.
- The plugin's version constraint must be readable from the plugin metadata so the resolution logic can use it.

## Why This Matters

Dart-only desktop plugins published after Flutter 2.11 should work the same way as mobile plugins — users shouldn't have to manually wire up an implementation that is already bundled in the app-facing package. At the same time, older plugins must not be broken by this change.
