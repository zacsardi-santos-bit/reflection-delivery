## Description

When Flutter plugin authors build the example app that ships with their plugin, the tools currently have no way to warn them if their plugin is missing support for the newer Swift Package Manager dependency system. Plugin authors may unknowingly ship a plugin that only supports the legacy CocoaPods approach, or they may have a Swift Package Manager manifest that is incomplete (missing the required framework dependency). This leaves downstream app developers in a difficult situation when they try to use Swift Package Manager with these plugins.

## Expected Behavior

- When building a plugin's example app, Flutter tools should inspect the parent plugin and emit a developer-friendly warning if:
  - The plugin only provides a legacy CocoaPods podspec with no Swift Package Manager manifest at all.
  - The plugin provides a Swift Package Manager manifest but the manifest is missing the required framework dependency.
- The warning should include a link to the official documentation so plugin authors know how to fix the issue.
- The warning should include the name of the platform (iOS or macOS) that has the compatibility gap.
- For plugins that share a single source directory for both iOS and macOS, the check should apply to both platforms.
- If the plugin already provides a complete and correct Swift Package Manager manifest, no warning should be emitted.
- If the plugin does not support the given platform at all, no warning should be emitted.

## Smart Suppression

The warning logic must only fire in the right context. It must remain silent when:
- The current project is not a plugin's example app (i.e., the directory layout does not match the expected pattern).
- The parent package metadata is absent, malformed, or does not describe a Flutter plugin.
- The parent plugin name cannot be matched to a known plugin.

## Why This Matters

Plugin authors often build and test via their bundled example app. Surfacing compatibility warnings at that point — with a direct link to documentation — gives authors an early, actionable signal to add or fix Swift Package Manager support before their plugin is published.
