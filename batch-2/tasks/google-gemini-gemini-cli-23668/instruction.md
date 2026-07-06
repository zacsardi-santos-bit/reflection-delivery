Implement a more expressive policy engine that distinguishes between interactive and non-interactive sessions. Add support for an optional mode indicator on individual policy rules to specify their applicability. Update the configuration function to accept an interactive flag for setting session mode and default decisions.

*   Update the `createPolicyEngineConfig` function in `packages/core/src/policy/config.ts`:
    *   Add a fourth parameter, `interactive` (boolean, defaults to true).
    *   When `interactive` is false, set `nonInteractive` to true and `defaultDecision` to `PolicyDecision.DENY`.
    *   When `interactive` is true or omitted, set `defaultDecision` to `PolicyDecision.ASK_USER`.

*   Update the `createPolicyEngineConfig` function in `packages/cli/src/config/policy.ts`:
    *   Accept the same `interactive` parameter and pass it through to the core function.
    *   Ensure `workspacePoliciesDir` is passed as undefined if not provided.

*   Modify the `PolicyRule` type in `packages/core/src/policy/`:
    *   Add an optional `interactive` field (boolean).
        *   When `interactive` is true, the rule applies only in interactive mode.
        *   When `interactive` is false, the rule applies only in non-interactive mode.
        *   When absent, the rule applies in both modes.

*   Ensure the `PolicyEngine`:
    *   Does not globally convert `ASK_USER` decisions to `DENY` in non-interactive mode.
    *   Respects rules with the `interactive` field for mode-specific behavior.
    *   Does not automatically deny redirected shell commands in non-interactive mode if a rule with `allowRedirection: true` grants `ALLOW`.
    *   Includes tools in the exclusion list only when there is an explicit rule with `interactive: false` and decision `DENY`.

*   Ensure `getExcludedTools` method:
    *   Includes a tool in the exclusion list only when there is an explicit rule with `interactive: false` and decision `DENY`.
    *   Excludes the `ask_user` tool in non-interactive mode when there is an explicit rule with `toolName 'ask_user'`, decision `DENY`, and `interactive: false`.

*   Update all call sites invoking `createPolicyEngineConfig`:
    *   Pass `workspacePoliciesDir` and `interactive` as positional arguments in that order.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.