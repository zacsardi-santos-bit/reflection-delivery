## Description

The policy engine currently handles non-interactive (automated/pipeline) mode by applying a blanket global override: any "ask the user" decision from any policy rule is silently converted to "deny." This means policy rules cannot express mode-specific intent — there is no way to write a rule that says "ask the user in interactive mode, but deny in non-interactive mode" as separate, explicit entries. Instead, all rules are written for interactive use and non-interactive mode just overrides them globally.

This approach also has unintended side effects: shell commands that involve output redirection are automatically blocked in non-interactive mode, even when the system has been explicitly configured to allow them.

## Expected Behavior

- Policy rules should be able to carry an optional field indicating whether they apply only in interactive mode, only in non-interactive mode, or both (when unspecified).
- The function that builds a policy engine configuration should accept an interactive flag so it can set the non-interactive mode property and appropriate default decision directly, rather than requiring the caller to patch the configuration after the fact.
- When operating in non-interactive mode, the engine should respect explicit deny rules for each tool rather than applying a global automatic conversion of prompting-the-user decisions to denials.
- Shell redirection commands should not be automatically denied in non-interactive mode if a rule explicitly allows them.
- The tool exclusion list in non-interactive mode should be derived from explicit deny rules scoped to non-interactive mode, not from global behavior overrides.

## Why This Matters

Non-interactive and interactive sessions have fundamentally different trust models, and the policy system should be expressive enough to capture both. Operators should be able to write policies that grant broader access in interactive sessions while restricting automated pipelines — without the engine silently overriding their configurations.
