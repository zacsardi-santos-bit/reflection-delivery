## Description

Installing packages from GitHub Packages or other private npm-compatible registries in pnpm currently requires configuring a full scope-to-URL mapping in the project settings. There's no concise shorthand syntax comparable to how other package managers let you express "install this from my private registry."

This issue proposes adding a named-registry prefix system: a built-in short prefix that maps to the GitHub Packages npm registry, so developers can write a short alias followed by a colon and the scoped package name as an installation target. Beyond the built-in prefix, users should be able to define their own aliases in the workspace configuration, mapping custom short names to arbitrary registry URLs. Users running a GitHub Enterprise Server instance should also be able to override the built-in prefix to point at their enterprise host.

Authentication should be picked up automatically from existing per-URL credential entries (the way npm authentication tokens already work), with no additional auth mechanism required.

## Expected Behavior

- A built-in short prefix resolves packages against the GitHub Packages npm registry
- Custom aliases can be configured in the workspace file under a new named-registries configuration section, mapping an alias to a registry URL
- A user-defined entry under the same name as a built-in alias overrides the built-in (enabling GitHub Enterprise Server support)
- Registry URLs in the workspace configuration support environment variable substitution, consistent with how per-URL authentication tokens are already handled
- When a specifier uses a named-registry alias, authentication is looked up by the registry URL, so existing per-URL token entries work automatically
- Creating the resolver fails at startup (not at install time) when a configured registry URL is malformed
- Specifiers that belong to other resolvers (git shorthands, workspace references, file/link paths, catalog entries) are not intercepted

## Why This Matters

Teams using GitHub Packages or internal registries need a low-friction way to declare such dependencies without spelling out full registry URLs in every dependency specifier. The short-prefix approach is familiar from other package managers and fits naturally into the existing pnpm workflow.
