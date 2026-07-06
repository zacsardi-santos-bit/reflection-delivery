## Description

pnpm currently has no way to supply URL-scoped authentication credentials for package registries through environment variables. The only supported approach is to write authentication tokens directly into `.npmrc` configuration files. This is a problem in CI pipelines and containerized environments where secrets should be injected at runtime via environment variables rather than baked into tracked configuration files.

## Expected Behavior

- Setting an environment variable whose name follows the pattern of a well-known config prefix followed by a URL-scoped registry key (e.g. the registry URL starting with `//`) should cause that credential to appear in the effective auth configuration.
- Both the npm-style and pnpm-style config environment variable prefixes should be recognized (case-insensitively).
- When both prefixes specify credentials for the same registry URL, the pnpm-prefixed variable should win.
- Environment-variable credentials should override the same key found in a project-level `.npmrc` file.
- Explicit command-line options should still take highest priority, overriding environment variables.
- Sensitive execution-based credential fields (those that run external binaries) must not be imported from environment variables regardless of prefix.
- Only URL-scoped keys (those whose key portion starts with `//`) should be recognized; other config keys from the same prefix should be ignored.
- Non-token credential fields such as username and password should be supported alongside auth tokens.

## Why This Matters

CI systems and secret management tools (Vault, Kubernetes secrets, etc.) inject credentials as environment variables. Currently, pnpm users must write credentials into `.npmrc` files which risks accidental exposure in source control. Supporting URL-scoped auth via environment variables brings pnpm in line with standard npm behavior and makes it straightforward to authenticate against private registries in ephemeral build environments without touching any configuration files.
