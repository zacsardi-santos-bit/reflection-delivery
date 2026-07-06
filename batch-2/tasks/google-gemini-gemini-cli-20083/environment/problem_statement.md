## Description

When MCP tools declare semantic behavior hints (such as indicating they are read-only), those annotations are currently consumed at discovery time to create dynamic policy rules, then discarded. As a result, the policy engine has no access to annotation data when it actually checks whether a tool should be allowed, denied, or sent for user confirmation. This creates a gap: policy rules defined in configuration files cannot match tools based on their declared behavior, and annotation information cannot flow through the tool execution pipeline.

## Expected Behavior

- Tool annotations should be preserved on the tool object after discovery, not discarded after rule generation.
- Annotations should be passed through the execution pipeline all the way to the policy decision point, so that policy rules can match tools based on their annotation values.
- The policy engine's rule matching should support annotation-based criteria — rules can specify that they apply only to tools carrying certain annotation values.
- In plan mode, MCP tools that declare themselves as read-only (via their annotations) should receive a "prompt user" decision rather than an unconditional denial, while MCP tools without such annotations remain denied.
- The tool exclusion mechanism should support annotation-aware filtering when annotation metadata is available: when no metadata is provided, annotation-based rules should be skipped rather than causing incorrect exclusions.
- Dynamic policy rule generation based on tool annotations at discovery time should be removed; annotation-based policy rules should live in static configuration files instead.

## Why This Matters

Without this change, users in plan mode cannot use any MCP tools even if those tools are inherently safe (read-only). The current approach of generating dynamic rules at discovery time is fragile and loses the annotation information that would allow fine-grained, annotation-aware policy decisions throughout the session.
