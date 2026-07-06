I'm working in a monorepo where my workspace folder is nested inside a parent repository.

*   Must add PromptsConfig.SEARCH_ROOT_REPO_CUSTOMIZATIONS as a new exported string constant with value 'chat.searchRootRepositoryCustomizations' inside the PromptsConfig namespace in the config module.

*   When the SEARCH_ROOT_REPO_CUSTOMIZATIONS configuration value is true, ComputeAutomaticInstructions.collect() must discover and include customization files (CLAUDE.md, .claude/CLAUDE.md, .github/copilot-instructions.md, and AGENTS.md) located in parent directories above the workspace root folder.

*   When the SEARCH_ROOT_REPO_CUSTOMIZATIONS configuration value is false, ComputeAutomaticInstructions.collect() must NOT include any customization files from parent directories above the workspace root folder.

*   IPromptsService must expose a new public method getPromptDiscoveryInfo(type: PromptsType, token: CancellationToken, sessionResource?: URI): Promise<IPromptDiscoveryInfo> that returns detailed discovery information for prompt files of the given type.

*   When the workspace is untrusted and hook files are present, getPromptDiscoveryInfo(PromptsType.hook, token) must return an IPromptDiscoveryInfo whose files array contains one entry per hook file found, each with status equal to the string 'skipped' and skipReason equal to the string 'workspace-untrusted'.

*   listPromptFiles(), findAgentSkills(), and getPromptSlashCommands() must no longer return any items with PromptsStorage.internal as their storage value. Internal built-in customizations should be removed from the public API output of these methods.

*   The SEARCH_ROOT_REPO_CUSTOMIZATIONS config key must be included in the configuration setup of PromptFilesLocator (defaulting to false) so that file locator tests can correctly configure the new setting.


*   Interface details: Type: Constant
Name: PromptsConfig.SEARCH_ROOT_REPO_CUSTOMIZATIONS
Location: src/vs/workbench/contrib/chat/common/promptSyntax/config/config.ts
Signature: SEARCH_ROOT_REPO_CUSTOMIZATIONS: string = 'chat.searchRootRepositoryCustomizations'
Description: Configuration key that controls whether configuration files should be searched in parent folders of the workspace folder if those parent folders are repositories. When the configuration value is true, the system searches ancestor directories for customization files. When false (default), only workspace-root and user-home directories are searched.

Type: Method
Name: getPromptDiscoveryInfo
Location: src/vs/workbench/contrib/chat/common/promptSyntax/service/promptsService.ts (interface IPromptsService) and src/vs/workbench/contrib/chat/common/promptSyntax/service/promptsServiceImpl.ts (implementation class PromptsService)
Signature: getPromptDiscoveryInfo(type: PromptsType, token: CancellationToken, sessionResource?: URI): Promise<IPromptDiscoveryInfo>
Description: Returns detailed discovery information for a given prompt type. Each discovered file is reported with a status ('loaded' or 'skipped') and, when skipped, a skipReason string. When the workspace is untrusted and hook files exist, they appear in the result with status 'skipped' and skipReason 'workspace-untrusted'.

Type: Interface
Name: IPromptDiscoveryInfo
Location: src/vs/workbench/contrib/chat/common/promptSyntax/service/promptsService.ts
Description: Return type of getPromptDiscoveryInfo. Contains a files array of IPromptFileDiscoveryResult entries.
Signature: { files: IPromptFileDiscoveryResult[]; ... }

Type: Interface
Name: IPromptFileDiscoveryResult
Location: src/vs/workbench/contrib/chat/common/promptSyntax/service/promptsService.ts
Description: Describes the discovery outcome for a single prompt file. The status field is 'loaded' when the file was used, or 'skipped' when it was not. When status is 'skipped', skipReason provides the reason (e.g., 'workspace-untrusted').
Signature: { status: 'loaded' | 'skipped'; skipReason?: string; ... }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.