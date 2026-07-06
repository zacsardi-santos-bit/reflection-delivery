## Description

When the Codex agent initiates network access as part of executing a command (for instance, making an HTTP request from a shell command), the guardian review system should present a clear and tailored explanation to the reviewer about what they're being asked to evaluate.

Currently, the network access approval prompt reuses generic action-review language designed for file edits or command execution. This means the reviewer sees labels like "Planned action JSON" and text like "The Codex agent has requested the following action", which are not appropriate for a network access request. There is also no guidance explaining how to assess network access specifically, and the omission note (a policy-blocked-access message) is shown even though it doesn't belong in the approval prompt body.

## Expected Behavior

- The network access approval prompt should use network-specific introductory language indicating that a proposed network access request is under review.
- When a triggering command is present, the reviewer should be told to focus on whether that triggering command was authorized by the user, and that explicit prior authorization for the exact network connection is not required if the access is a reasonable consequence of the command.
- The JSON section should be labeled as "Network access JSON", not "Planned action JSON".
- Generic action-review labels ("The Codex agent has requested the following action", "Retry reason") should not appear for network access requests.
- The omission note (if any) should not appear in the body of the network access approval prompt.

## Why This Matters

Reviewers evaluating network access approvals need context-specific guidance to make good decisions. Using generic action-review language makes the approval process confusing and may lead to incorrect decisions. Tailoring the prompt to the type of approval helps reviewers understand what they are evaluating and what criteria to apply.
