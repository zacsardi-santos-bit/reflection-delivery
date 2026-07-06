## Description

Insomnia currently allows custom linting rulesets only for projects connected to a git repository. This is too restrictive — teams using cloud-synced or local projects have no way to apply custom validation rules to their OpenAPI specs. Additionally, when a ruleset references other local files via the extends mechanism, those dependencies need to be resolved and bundled at upload time so the saved ruleset is self-contained.

Beyond availability, the existing handling of remotely-referenced rulesets is unsafe: there are no checks to prevent rulesets from pointing to internal or private network hosts, which could be used to make the application issue requests to infrastructure that should not be reachable. There are also no checks to block the use of custom JavaScript execution features that rulesets can declare.

## Expected Behavior

- A new utility should detect whether a hostname or IP address is private or loopback (covering localhost and its subdomains, all standard private IPv4 ranges, IPv4 and IPv6 loopback, link-local addresses, and IPv6 private ranges). Public addresses and generic hostnames should not be flagged.

- A validator for Spectral ruleset content should parse the YAML, enforce a safe structure, and return a clear result indicating whether the content is valid or invalid (with an error description). It should reject empty content, non-object YAML, unsupported top-level keys (such as custom function declarations), prototype-polluting identifiers in rule names or path expressions, unsafe URL schemes in documentation links, path-traversal characters in field specifiers, and functions that are not in the allowed built-in set.

- A bundler for Spectral rulesets should read a ruleset file, recursively resolve any local file references in the extends field, and return a single merged YAML string. It should pass through built-in spectral identifiers unchanged, deduplicate them, enforce a maximum extends depth, detect cycles, and reject unsafe configurations. For remote HTTPS references, it should validate the remote content (without merging it into the output) and perform DNS resolution to block references to private or internal hosts.

## Why This Matters

These changes let any project type benefit from custom linting rules, while the validation and bundling pipeline ensures that neither locally-uploaded nor remotely-referenced rulesets can be used to probe internal infrastructure or execute arbitrary code.
