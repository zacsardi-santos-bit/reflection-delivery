## Description

There are two related issues with how the a2a-server loads settings and constructs its policy configuration:

**Security: Workspace settings are loaded unconditionally**

Currently, when a workspace directory contains a settings file, those settings are always merged into the active configuration. This is a security concern — a malicious or untrusted workspace could craft a settings file that overrides sensitive policy paths (like admin policy paths or user policy paths), potentially changing the behavior of the tool policy system in dangerous ways. Workspace settings should only be applied when the workspace has been verified as trusted.

**Policy engine: Default rules are empty in non-YOLO mode**

When the tool is running in the default (non-unrestricted) mode, the policy engine configuration is currently initialized with an empty set of rules. This means no default policies are applied, leaving the system without a baseline set of access controls. In non-YOLO mode, the policy engine should be initialized with a meaningful default ruleset loaded from default policy files.

## Expected Behavior

- Before loading workspace settings, the system should check whether the workspace directory is trusted. If it is not trusted, workspace settings should be silently ignored.
- Even for trusted workspaces, certain sensitive path-related policy settings must remain protected and cannot be overridden at the workspace level.
- The policy engine configuration should be built by passing a merged set of tool settings (combining legacy and current formats, with legacy taking precedence for the allowed list) to a dedicated configuration builder function.
- In default (non-YOLO) mode, the resulting policy engine configuration should include a default set of rules rather than an empty list.

## Why This Matters

Without workspace trust checking, any project a user opens could silently change the tool access policy in unexpected ways. Without default rules in non-YOLO mode, users running the a2a-server without unrestricted mode have no baseline protections applied.
