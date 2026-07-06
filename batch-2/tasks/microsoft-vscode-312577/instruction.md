I'm working on improving how the agent prompt system handles the list of deferred tools in Copilot.

*   The DeferredToolListReminder component must render the deferred tools list in the initial conversation context section (after repoMemory), not inside the tool search instructions block. It must render nothing when tool search is unsupported by the endpoint, when availableTools is undefined, or when no deferred tools exist.

*   The DeferredToolListReminder component must wrap its output in an XML tag named 'availableDeferredTools'. The content must begin with the header line 'Available deferred tools (must be loaded with tool_search before use):' followed by a sorted newline-separated list of deferred tool names.

*   The hasDeferredTool function must return true if availableTools contains at least one tool for which toolDeferralService.isNonDeferredTool() returns false. It must return false if availableTools is undefined, empty, or all tools are non-deferred.

*   The ToolSearchToolPromptProps interface must no longer include a modelFamily property. Only availableTools (readonly LanguageModelToolInformation[] | undefined) should be present.

*   For models that previously used the verbose tool-search instruction format (4.5-generation), the mandatory instruction text must change from 'deferred tools listed below are NOT available' to 'deferred tools are NOT available' (removing the phrase 'listed below').

*   For models that previously used the verbose tool-search instruction format (4.5-generation), the search query guidance text must change from 'Check the availableDeferredTools list below' to 'Consult the availableDeferredTools list (provided in the initial conversation context)'.

*   For models that previously used the verbose tool-search instruction format (4.5-generation), the dynamic tool discovery text must change from 'not listed in the availableDeferredTools list above' to 'not listed in the latest availableDeferredTools list'.

*   The availableDeferredTools list must no longer appear inside the toolSearchInstructions block for any model variant. The toolSearchInstructions block (for 4.6-generation models) must end after the 'Do not retry with different patterns.' line with no tool list following it.

*   DeferredToolListReminder must be imported from toolSearchInstructions and used inside GlobalAgentContext in agentPrompt.tsx, placed after the MemoryContextPrompt conditional and before the cache breakpoint, passing availableTools from the component's own props.


*   Interface details: Type: Class
Name: DeferredToolListReminder
Location: extensions/copilot/src/extension/prompts/node/agent/toolSearchInstructions.tsx
Description: A prompt element that renders the list of available deferred tools. It is placed in the global conversation context (not the system prompt) so the list appears at the start of a new conversation. It gates itself on endpoint.supportsToolSearch — returns nothing when tool search is unsupported. It also returns nothing when no tools are available or when no deferred tools exist. Renders as a `<availableDeferredTools>` XML-tagged block containing the heading "Available deferred tools (must be loaded with tool_search before use):" followed by a sorted newline-separated list of deferred tool names.
Signature: constructor(props: PromptElementProps<DeferredToolListReminderProps>, toolDeferralService: IToolDeferralService)

Type: Interface
Name: DeferredToolListReminderProps
Location: extensions/copilot/src/extension/prompts/node/agent/toolSearchInstructions.tsx
Description: Props for DeferredToolListReminder. Contains a single field: availableTools (readonly LanguageModelToolInformation[] | undefined).

Type: Function
Name: hasDeferredTool
Location: extensions/copilot/src/extension/prompts/node/agent/toolSearchInstructions.tsx
Signature: hasDeferredTool(availableTools: readonly LanguageModelToolInformation[] | undefined, toolDeferralService: IToolDeferralService): boolean
Description: Returns true when availableTools contains at least one tool that the deferral service treats as deferred (i.e., not a non-deferred tool). Returns false when availableTools is undefined or empty, or when all tools are non-deferred.

Type: Interface (modified)
Name: ToolSearchToolPromptProps
Location: extensions/copilot/src/extension/prompts/node/agent/toolSearchInstructions.tsx
Description: Props for the tool search prompt components. The modelFamily property has been removed — the interface now contains only availableTools (readonly LanguageModelToolInformation[] | undefined).

Type: Component usage
Name: DeferredToolListReminder (usage in GlobalAgentContext)
Location: extensions/copilot/src/extension/prompts/node/agent/agentPrompt.tsx
Description: DeferredToolListReminder must be added inside GlobalAgentContext's render output, placed after MemoryContextPrompt and before the cache breakpoint, receiving availableTools={this.props.availableTools}.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.