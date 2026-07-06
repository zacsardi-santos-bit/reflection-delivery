## Description

The GitHub repository publishing action fails with a cryptic connection error in corporate environments where network proxies perform deep packet inspection. The git protocol used to push repository content sends binary data in HTTP POST requests, which many enterprise proxies reject or drop, causing the push to fail with connection reset or connection refused errors. When this happens, the entire scaffolding workflow fails and users get no useful guidance.

## Expected Behavior

- When the initial git push fails due to a connection-level error (connection reset or refused), the action should automatically detect this and fall back to an alternative upload mechanism that uses standard JSON-based requests instead of binary git protocol data.
- The fallback should also handle the case where the repository was just created and is still empty, initializing it properly before uploading files.
- If the branch being targeted is not found on a non-empty repository, the action should report a clear error describing the situation.
- If the git push fails for a reason unrelated to connectivity (such as an authentication error), the original error should still be propagated — the fallback should only activate for connection-level failures.
- After a successful fallback push, the action should report the resulting commit identifier as output in the same way the normal path does.

## Why This Matters

Organizations with strict network proxy policies that block binary payloads cannot currently use the scaffolding workflow to create repositories without manual intervention. This fallback would allow template scaffolding to work reliably in those environments without any configuration changes required.
