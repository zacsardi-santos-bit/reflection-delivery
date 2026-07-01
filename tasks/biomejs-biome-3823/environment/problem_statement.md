## Description

Biome's JavaScript linter currently has no way to detect hardcoded secrets, API keys, or other sensitive credentials stored directly in source code. Developers frequently accidentally commit things like cloud service access keys, OAuth tokens, private key material, database connection strings with embedded passwords, and other sensitive strings — and today Biome gives no warning about this.

## Expected Behavior

A new lint rule in the nursery category should scan string literals in JavaScript and TypeScript files and flag any that appear to contain secrets. The rule should:

- Detect well-known patterns for major providers (cloud API keys, authentication tokens, private key headers, webhook URLs, service-specific token formats, database URLs with embedded credentials)
- Detect generic high-entropy strings that are likely sensitive, even when they don't match a known provider pattern
- Identify and report the type of secret that was detected
- Provide actionable remediation guidance (how to remove the secret from history, how to use environment variables instead, and how to suppress false positives)
- Leave non-secret values alone: numbers, plain readable text, and connection strings that already use runtime variable references should not be flagged

## Why This Matters

Committing secrets to source code is one of the most common security vulnerabilities in software projects. Having the linter catch these at development time (before commit or CI) gives developers immediate feedback and helps prevent credential leaks. Clear guidance on how to remediate each finding makes it actionable rather than just alarming.
