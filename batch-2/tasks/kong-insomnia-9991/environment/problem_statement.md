## Description

When a user uploads a linting ruleset that references remote URLs (via extends entries), those remote resources are currently fetched live during every lint operation rather than being resolved once at upload time. This creates two problems: (1) the lint results can become inconsistent if remote content changes between runs, and (2) malicious or misconfigured ruleset content could be used to trigger requests to internal network addresses, including loopback and unspecified IP ranges that aren't currently blocked.

## Expected Behavior

- When a ruleset is saved, any remote extends references should be fetched, validated, and compiled into a single self-contained local file stored per project. The compiled file should not include the original remote URLs — the remote content should be fully inlined.
- Nested remote extends (a remote ruleset that itself extends another remote URL) should also be resolved recursively.
- Built-in identifiers referenced by remote rulesets should be preserved as-is in the compiled output without fetching.
- Remote rulesets that declare custom functions should be rejected outright, as this is a code execution vector.
- Non-https remote URLs should be rejected before any fetch attempt.
- Remote URLs pointing at loopback hosts should be rejected before any fetch attempt.
- IPv4 addresses in the 0.0.0.0/8 unspecified range should be treated as disallowed hosts, the same as loopback and private ranges.
- The compiled ruleset should be cached per project and reused until content changes or the user explicitly requests a refresh.
- Deleting a project's compiled ruleset should also clear the associated cache so the next write triggers a full recompile.

## Why This Matters

Without compiling rulesets at upload time, users are exposed to stale or changing remote content during linting, and the lack of full IP-range blocking leaves an SSRF-like attack surface open. Compiling once and caching locally makes linting deterministic, faster, and safer.
