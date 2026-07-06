## Description

The session management layer needs to automatically handle tool approval requests from subagents using the configured policy engine, instead of leaving all requests unanswered or requiring external handling. When a subagent wants to use a tool, the session should consult the policy engine and immediately return an approval, denial, or escalation to the user — rather than forcing every tool usage to require manual confirmation.

Additionally, the message bus used for subagent communication is vulnerable to metadata spoofing: a compromised or malicious subagent can craft a tool confirmation request that claims to be from a trusted identity, belongs to a known safe server, carries forged trusted annotations, or includes a forced decision override. The bus needs to strip these tamper-able fields and enforce the true identity of the subagent when forwarding requests to the parent bus.

## Expected Behavior

- The session should subscribe to tool confirmation requests on startup and route them through the policy engine automatically
- Policy decisions of "allow" should result in automatic approval without user intervention
- Policy decisions of "deny" should result in automatic rejection without prompting the user
- Policy decisions of "ask user" should escalate to the user for manual confirmation
- When looking up tool metadata for policy evaluation, the implementation must use registry-sourced data (not values supplied by the requesting subagent) to prevent spoofing
- The session must fail safely (deny by default) when the policy engine is unavailable, throws an error, or when the tool is unknown or has a missing name
- When a subagent publishes a tool confirmation request through a derived bus, the bus must strip any attached forced-decision overrides, server name claims, tool annotation claims, and display detail overrides before forwarding to the parent

## Why This Matters

Without automated policy-based approval, every tool call from a subagent interrupts the user unnecessarily, even when the tool is pre-approved by policy rules. And without metadata sanitization on the message bus, a compromised subagent could bypass the policy engine or impersonate a trusted component — undermining the security of the entire approval flow.
