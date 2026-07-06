## Description

The code scanning action silently drops security findings when a scan response contains both a "skip" signal and real findings. This happens because the action currently treats any response with a skip reason as entirely skipped, without checking whether the response also contains actual security issues. As a result, genuine findings can be swallowed without ever reaching reviewers.

Additionally, file-level security findings — findings tied to a specific file but not a particular line — are currently misclassified and dropped rather than being routed to general PR comments. This means an entire category of security issues is invisible to reviewers.

## Expected Behavior

- When a scan response carries a skip reason alongside real security findings (non-trivial severity), the action should still surface those findings as PR comments and/or security report entries.
- The action should distinguish between "pure skips" (no real findings) and "mixed responses" (skip reason + real findings), and warn operators when a contradictory mixed response is encountered.
- File-level findings (associated with a file but not a specific line) should be routed to general PR comments instead of being silently discarded.
- When a scan response with a skip reason contains only trivial-severity findings, the action should continue to treat it as a pure skip — no comments should be posted and no security report should be generated.
- The security report should only be generated when there are findings that the report format can actually represent; a mixed-skip with only fileless findings should not produce an empty report entry.

## Why This Matters

Security findings that arrive in mixed-skip responses are currently invisible to developers and security teams. This creates a false sense that a clean scan occurred, when in reality real issues were silently discarded.
