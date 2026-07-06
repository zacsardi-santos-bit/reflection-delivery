I'm working on the Anthropic integration for Home Assistant and I've noticed that when configuring the extended reasoning feature, users can accidentally set the thinking budget to be larger than the total maximum token limit.

*   The max_tokens configuration option must be moved from the 'advanced' step to the 'model' step of the subentry configuration flow. The 'advanced' step must no longer include max_tokens in its form schema.

*   The 'model' step must always include max_tokens as a form field (even for models without thinking or web search capabilities), so this step is always shown to the user.

*   When both max_tokens and thinking_budget are submitted in the 'model' step and thinking_budget >= max_tokens, the model step must return a validation error with the field key 'thinking_budget' and the error code 'thinking_budget_too_large'. The form must be re-shown without saving.

*   When a valid combination is submitted (thinking_budget < max_tokens), the flow must complete successfully with reason 'reconfigure_successful' and save both max_tokens and thinking_budget to the subentry data.

*   The error translation key 'thinking_budget_too_large' must be defined in the component strings (strings.json) for both the conversation and AI task subentry entry types, with the error message indicating that the thinking budget must be less than the maximum tokens.

*   For models that do not support thinking (no thinking_budget field), only max_tokens is collected in the model step and no thinking_budget validation is performed.

*   When model-specific options (thinking_budget, web_search, web_search_max_uses, web_search_user_location, code_execution) are not applicable to the selected model, those keys must be removed from the saved options rather than stored with default values.


*   Interface details: Type: Class
Name: ConversationSubentryFlowHandler
Location: homeassistant/components/anthropic/config_flow.py
Description: Handles config subentry flows for Anthropic conversation and AI task integrations. Contains the multi-step configuration flow with steps "advanced" and "model".

Type: Method
Name: async_step_advanced
Location: homeassistant/components/anthropic/config_flow.py (within ConversationSubentryFlowHandler)
Signature: async_step_advanced(user_input: dict | None = None) -> FlowResult
Description: Handles the "advanced" configuration step. Must NOT include max_tokens (CONF_MAX_TOKENS) in its form schema. Accepts chat_model, temperature, prompt_caching, and other general options.

Type: Method
Name: async_step_model
Location: homeassistant/components/anthropic/config_flow.py (within ConversationSubentryFlowHandler)
Signature: async_step_model(user_input: dict | None = None) -> FlowResult
Description: Handles the model-specific configuration step. Must always include max_tokens (CONF_MAX_TOKENS) in its form schema. When thinking_budget is present in user_input and thinking_budget >= max_tokens, must return errors={"thinking_budget": "thinking_budget_too_large"} and re-show the form. This step must always be shown (never skipped) because max_tokens is always part of the schema.

Type: File
Name: strings.json
Location: homeassistant/components/anthropic/strings.json
Description: Component translations file. Must define the "thinking_budget_too_large" error key under both the conversation subentry (config_subentries.conversation.error) and the AI task subentry (config_subentries.ai_task.error) sections. The error message for "thinking_budget_too_large" should indicate that the thinking budget must be less than the maximum tokens.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.