I'm working on the release notes renderer in VS Code.

*   Must export a function `processConditionalBlocks` from `src/vs/workbench/contrib/update/browser/releaseNotesEditor.ts` with the signature `processConditionalBlocks(text: string, activeConditions: ReadonlySet<string>): string`.

*   When a conditional block `<!-- %IF CONDITION %\n...content...\n%ENDIF % -->` appears in the text and CONDITION (uppercased) is present in `activeConditions`, `processConditionalBlocks` must include that block's content in the output with all IF/ENDIF comment markers removed.

*   When a conditional block's CONDITION (uppercased) is NOT present in `activeConditions`, `processConditionalBlocks` must remove the entire block (including its content) from the output.

*   Text outside of conditional blocks must be preserved unchanged by `processConditionalBlocks`.

*   Condition name matching in `processConditionalBlocks` must be case-insensitive: for example, `%IF in_product %` must match the active condition `'IN_PRODUCT'`.

*   Multiple conditional blocks within the same document must each be processed independently by `processConditionalBlocks`, so some blocks may be revealed while others are removed in the same call.

*   The function `renderReleaseNotesMarkdown` must accept an optional fifth parameter `quality?: string`.

*   When `renderReleaseNotesMarkdown` is called with `quality` equal to `'stable'`, it must build an active conditions set containing at least `'IN_PRODUCT'` and `'STABLE'`, causing STABLE conditional blocks to be revealed and INSIDERS conditional blocks to be removed from the rendered HTML.

*   When `renderReleaseNotesMarkdown` is called with `quality` equal to `'insider'`, it must build an active conditions set containing at least `'IN_PRODUCT'` and `'INSIDERS'`, causing INSIDERS conditional blocks to be revealed and STABLE conditional blocks to be removed from the rendered HTML.

*   `'IN_PRODUCT'` must always be included in the active conditions set used by `renderReleaseNotesMarkdown`, regardless of the `quality` value.


*   Interface details: Type: Function
Name: processConditionalBlocks
Location: src/vs/workbench/contrib/update/browser/releaseNotesEditor.ts
Signature: processConditionalBlocks(text: string, activeConditions: ReadonlySet<string>): string
Description: Processes a markdown string by evaluating conditional blocks in the format `<!-- %IF CONDITION %\n...content...\n%ENDIF % -->`. If the CONDITION (compared case-insensitively) is present in activeConditions, the block's content is included in the output (with the IF/ENDIF comment markers removed). If CONDITION is not in activeConditions, the entire block including its content is removed. Content outside conditional blocks is preserved unchanged.

Type: Function
Name: renderReleaseNotesMarkdown
Location: src/vs/workbench/contrib/update/browser/releaseNotesEditor.ts
Signature: renderReleaseNotesMarkdown(text: string, extensionService: IExtensionService, languageService: ILanguageService, simpleSettingRenderer: SimpleSettingRenderer, quality?: string): Promise<TrustedHTML>
Description: Renders release notes markdown to trusted HTML. Now accepts an optional fifth parameter `quality` (e.g. `'stable'` or `'insider'`) that controls which conditional blocks are revealed. Internally builds the active conditions set (always including `'IN_PRODUCT'`, plus `'STABLE'` when quality is `'stable'`, or `'INSIDERS'` when quality is `'insider'`), then calls processConditionalBlocks before rendering.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.