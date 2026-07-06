Enhance the CLI assistant to provide platform-specific installation instructions for a companion CLI tool and implement model name translation for Google account authentication. Detect the user's operating system to offer appropriate installation commands and ensure model requests are correctly routed.

*   Implement the `getAntigravityInstallInfo` function in `packages/cli/src/ui/utils/antigravityUtils.ts` to return platform-specific installation information:
    *   Return `{ platformName: 'macOS', installCmd: 'curl -fsSL https://antigravity.google/cli/install.sh | bash' }` for `darwin`.
    *   Return `{ platformName: 'Linux', installCmd: 'curl -fsSL https://antigravity.google/cli/install.sh | bash' }` for `linux`.
    *   Return `{ platformName: 'Windows (PowerShell)', installCmd: 'irm https://antigravity.google/cli/install.ps1 | iex' }` for `win32` with `PSModulePath` set.
    *   Return `{ platformName: 'Windows (Command Prompt)', installCmd: 'curl -fsSL https://antigravity.google/cli/install.cmd -o install.cmd && install.cmd && del install.cmd' }` for `win32` without `PSModulePath`.
    *   Return `null` for unsupported platforms.

*   Implement the `ModelMappingContentGenerator` class in `packages/core/src/core/modelMappingContentGenerator.ts`:
    *   Accept a wrapped `ContentGenerator` and a `Record<string, string>` mappings object in the constructor.
    *   Strip the 'models/' prefix, map the model name using the provided dictionary, and restore the prefix if needed in `generateContent`.
    *   Delegate `userTier`, `userTierName`, and `paidTier` to the wrapped generator.
    *   Expose a `getWrapped()` method to return the wrapped `ContentGenerator`.

*   Export the `CCPA_AI_MODEL_MAPPINGS` constant from `packages/core/src/config/models.ts`:
    *   Include at least the mapping `'gemini-3.5-flash'` to `'gemini-3-flash'`.

*   Update the `createContentGenerator` function to:
    *   Wrap the inner generator with `ModelMappingContentGenerator(inner, CCPA_AI_MODEL_MAPPINGS)` for `LOGIN_WITH_GOOGLE` and `COMPUTE_ADC` auth types.
    *   Wrap with `LoggingContentGenerator` and ensure no model mapping for `USE_VERTEX_AI`, `USE_GEMINI`, or `GATEWAY` auth types.

*   Implement the `getCodeAssistServer` function to:
    *   Recursively unwrap `LoggingContentGenerator` and `ModelMappingContentGenerator` layers to find the underlying `CodeAssistServer`.

*   Enhance the `useBanner` hook to:
    *   Append platform-specific installation commands to `bannerText` containing 'Antigravity' using `getAntigravityInstallInfo`.
    *   Format appended text as: '\n \nTo install run "<bold install command>"' using `chalk.bold`.

*   Update the `helpCommand` action to:
    *   Detect queries with 'antigravity' and 'install' or 'migrate', outputting a `MessageType.INFO` with installation instructions.
    *   Output a documentation link for unsupported platforms.
    *   Fall back to default help output if 'install' or 'migrate' is not mentioned.

*   Create a built-in skill file in `packages/core/src/skills/builtin/`:
    *   Ensure it loads with `loadSkillsFromDir` as a skill named 'antigravity-support'.
    *   Include a description with 'Antigravity CLI' and a body with 'https://antigravity.google/docs/cli-getting-started'.

*   Ensure `DEFAULT_GEMINI_FLASH_MODEL` and `PREVIEW_GEMINI_FLASH_MODEL` are set to `'gemini-3.5-flash'` if `hasGemini35FlashGAAccess()` is true and the auth type is not `USE_GEMINI`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.