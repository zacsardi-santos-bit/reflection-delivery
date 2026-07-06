I'm running evaluations against a Codex agent that uses skill files, and I'm running into two issues that make the results unreliable.

*   When callApi processes a response containing command_execution items where SKILL.md reads failed (non-zero exit_code or status other than 'completed'), those failed reads must appear in result.metadata.attemptedSkillCalls and must NOT appear in result.metadata.skillCalls.

*   Each entry in metadata.attemptedSkillCalls must have exactly these fields: name (the skill directory name string), path (the relative or absolute path to the SKILL.md file string), and source (the string value 'heuristic').

*   result.metadata.skillCalls must contain only skill reads where the command execution succeeded (status 'completed' and exit_code 0). If there are no successful skill reads, the skillCalls key must be absent from metadata.

*   result.metadata.attemptedSkillCalls is only included in metadata when its length is greater than the number of confirmed skillCalls (i.e., there are more attempted reads than successful reads). When all skill reads succeed, attemptedSkillCalls must be omitted.

*   When cli_env is provided (without inherit_process_env set to true), the environment variables passed to the Codex SDK must still include the PATH variable as a non-empty string sourced from the process environment.

*   getCompletionAttributesForItem must include the codex.exit_code attribute (as a number) in its returned object when the input item has an exit_code field.

*   getCompletionAttributesForItem must only include skill trace attributes (promptfoo.skill.count, promptfoo.skill.names, promptfoo.skill.paths) when the command execution was successful — i.e., status is 'completed' and exit_code is 0.

*   getCompletionAttributesForItem must NOT include any skill trace attributes (promptfoo.skill.count, promptfoo.skill.names, promptfoo.skill.paths) for command execution items that have a failed status or non-zero exit_code.

*   getCompletionAttributesForItem, when called with a command_execution item that has no status or exit_code fields, must return only the codex.command attribute and no skill trace attributes.


*   Interface details: Type: Class
Name: OpenAICodexSDKProvider
Location: src/providers/openai/codex-sdk.ts
Description: Provider that wraps the OpenAI Codex SDK for promptfoo evaluations. Implements callApi and tracing helpers.

Type: Method
Name: callApi
Location: src/providers/openai/codex-sdk.ts (method of OpenAICodexSDKProvider)
Signature: callApi(prompt: string, context?: any): Promise<ProviderResponse>
Description: Calls the Codex SDK with the given prompt. The returned ProviderResponse.metadata must separate confirmed skill reads (metadata.skillCalls) from failed skill read attempts (metadata.attemptedSkillCalls). Each entry in both arrays has the shape { name: string, path: string, source: 'heuristic' }. metadata.skillCalls is only present when there is at least one successful skill read. metadata.attemptedSkillCalls is only present when it contains entries that are NOT already in skillCalls (i.e., when attemptedSkillCalls.length > skillCalls.length). When cli_env is provided (and inherit_process_env is not true), the environment passed to the SDK must still include PATH (and other minimal shell variables) from the process environment.

Type: Method
Name: getCompletionAttributesForItem
Location: src/providers/openai/codex-sdk.ts (private method of OpenAICodexSDKProvider)
Signature: getCompletionAttributesForItem(item: any, skillRootPrefixes?: readonly string[]): Record<string, string | number | boolean>
Description: Returns tracing attributes for a single completed Codex item. For command_execution items with no status or exit_code, returns only { 'codex.command': string }. For command_execution items with status 'completed' and exit_code 0, returns { 'codex.exit_code': 0, 'codex.status': 'completed', 'codex.output': string } plus skill attributes ('promptfoo.skill.count', 'promptfoo.skill.names', 'promptfoo.skill.paths') when the command reads SKILL.md files from known skill roots. For command_execution items with a failed status or non-zero exit_code, returns { 'codex.exit_code': number, 'codex.status': string, 'codex.output': string } WITHOUT skill attributes. This private method is accessed in tests via TypeScript any-casting.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.