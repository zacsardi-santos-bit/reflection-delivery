I'm working on improving the default guidance in our redteam setup skill documentation.

*   The skill instructions file (SKILL.md) must contain the exact text: 'Use `jailbreak:meta` for the default first setup/generation pass.' — this replaces the previous recommendation to start with the basic strategy.

*   The skill instructions file (SKILL.md) must contain text starting with: 'Use `jailbreak:hydra` instead when the target is stateful' — this provides guidance for stateful multi-turn conversational targets as a separate case from the default.

*   The reference examples file (redteam-setup-patterns.md) must contain the exact phrase: 'Multi-input is not the same as multi-turn.' — this clarifies the distinction between multi-input target configuration and multi-turn session-based testing.

*   The reference examples file must contain 'jailbreak:meta' as the strategy in its YAML example configurations, replacing the previous 'basic' strategy entries.

*   The reference examples file must contain 'jailbreak:hydra' in an example YAML configuration showing the appropriate strategy for stateful conversational targets.

*   The OpenAPI helper script (openapi-operation-to-redteam-config.mjs) must generate a redteam config object where the 'strategies' field equals exactly ['jailbreak:meta'] — changed from the previous default of ['basic'].


*   Interface details: Type: File
Name: SKILL.md
Location: plugins/promptfoo/skills/promptfoo-redteam-setup/SKILL.md
Description: The body of the promptfoo-redteam-setup agent skill. The section "Choose strategies conservatively" must be updated to recommend `jailbreak:meta` as the default strategy for the initial setup/generation pass, and `jailbreak:hydra` as the alternative for stateful multi-turn targets. Must contain exactly: "Use `jailbreak:meta` for the default first setup/generation pass." and "Use `jailbreak:hydra` instead when the target is stateful".

Type: File
Name: redteam-setup-patterns.md
Location: plugins/promptfoo/skills/promptfoo-redteam-setup/references/redteam-setup-patterns.md
Description: The reference/examples file for the promptfoo-redteam-setup skill. All example YAML configs that previously listed `- basic` as their strategy must be updated to `- jailbreak:meta`. A new clarifying note must be added with the exact text "Multi-input is not the same as multi-turn." followed by an example YAML block showing `jailbreak:hydra` as the strategy for stateful conversational targets. Must contain "jailbreak:meta", "jailbreak:hydra", and "Multi-input is not the same as multi-turn."

Type: File
Name: openapi-operation-to-redteam-config.mjs
Location: plugins/promptfoo/skills/promptfoo-redteam-setup/scripts/openapi-operation-to-redteam-config.mjs
Description: The OpenAPI helper script that drafts a redteam configuration from an OpenAPI spec. The `strategies` field in the generated `redteam` config object must be changed from `['basic']` to `['jailbreak:meta']`. The generated config object's `strategies` field must equal exactly `['jailbreak:meta']`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.