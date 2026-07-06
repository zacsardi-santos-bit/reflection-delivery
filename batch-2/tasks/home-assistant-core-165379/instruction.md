I'm working on the OpenAI conversation integration for Home Assistant and need to add support for configuring a service tier.

*   Must define a constant CONF_SERVICE_TIER with value "service_tier" in homeassistant/components/openai_conversation/const.py, importable from that module.

*   Must define a constant RECOMMENDED_SERVICE_TIER with value "auto" in homeassistant/components/openai_conversation/const.py.

*   Must define UNSUPPORTED_FLEX_SERVICE_TIERS_MODELS as a list of model name prefixes in const.py whose models do not support the 'flex' service tier. Models whose names start with any prefix in this list must not be offered the 'flex' option. The following models must NOT have the 'flex' option: gpt-5.3-codex, gpt-5.2-codex, gpt-5.1-codex-max, gpt-5-codex, gpt-4.1, gpt-4.1-mini, gpt-4.1-nano, gpt-4o, gpt-4o-2024-05-13, gpt-4o-mini, gpt-5-chat-latest, gpt-5.2-pro, o3-mini. The following models MUST have the 'flex' option: gpt-5.4, gpt-5.4-pro, gpt-5.2, gpt-5.1, gpt-5, gpt-5-mini, gpt-5-nano, o3, o4-mini.

*   Must define UNSUPPORTED_PRIORITY_SERVICE_TIERS_MODELS as a list of model name prefixes in const.py whose models do not support the 'priority' service tier. Models whose names start with any prefix in this list must not be offered the 'priority' option. The following models must NOT have the 'priority' option: gpt-5-nano, gpt-5-chat-latest, gpt-5.2-pro, o3-mini. The following models MUST have the 'priority' option: gpt-5.4, gpt-5.4-pro, gpt-5.2, gpt-5.1, gpt-5, gpt-5-mini, gpt-5.3-codex, gpt-5.2-codex, gpt-5.1-codex-max, gpt-5-codex, gpt-4.1, gpt-4.1-mini, gpt-4.1-nano, gpt-4o, gpt-4o-2024-05-13, gpt-4o-mini, o3, o4-mini.

*   In the config flow's 'model' step, if the selected model supports at least one of 'flex' or 'priority', a CONF_SERVICE_TIER field must appear in the schema as a SelectSelector dropdown with options ordered as: 'auto' first, 'flex' (if supported), 'default', then 'priority' (if supported). If the model supports neither 'flex' nor 'priority', the CONF_SERVICE_TIER field must be absent from the schema entirely.

*   When completing the config flow, the stored subentry data must include CONF_SERVICE_TIER with the user-selected value. If the previously stored value is no longer in the valid options for the chosen model, it must be cleared from the stored data.

*   The default value for CONF_SERVICE_TIER in the config flow schema must be RECOMMENDED_SERVICE_TIER ("auto").

*   When making API calls, the entity must pass the configured CONF_SERVICE_TIER value as the service_tier parameter. If not configured, it must default to RECOMMENDED_SERVICE_TIER ("auto").

*   When the configured service_tier is "flex" and a rate limit error is received whose message contains "resource unavailable" (case-insensitive), the entity must automatically retry the same request with service_tier set to "default" instead of failing. The retry must succeed if the API responds normally on the second attempt, and the conversation must complete with an ACTION_DONE result.

*   When the flex-to-default fallback retry occurs, the first API call must use service_tier="flex" and the second API call must use service_tier="default". The total number of API calls for such a scenario must be exactly 2.


*   Interface details: Type: Constant
Name: CONF_SERVICE_TIER
Location: homeassistant/components/openai_conversation/const.py
Signature: CONF_SERVICE_TIER = "service_tier"
Description: Configuration key for the service tier setting. Used in the config flow schema, subentry data storage, and API calls.

Type: Constant
Name: RECOMMENDED_SERVICE_TIER
Location: homeassistant/components/openai_conversation/const.py
Signature: RECOMMENDED_SERVICE_TIER = "auto"
Description: Default recommended service tier value, used as the default in the config flow schema and as a fallback when the option is not set.

Type: Constant
Name: UNSUPPORTED_FLEX_SERVICE_TIERS_MODELS
Location: homeassistant/components/openai_conversation/const.py
Signature: UNSUPPORTED_FLEX_SERVICE_TIERS_MODELS: list[str] = [...]
Description: List of model name prefixes that do not support the "flex" service tier. Models whose names start with any entry in this list will not be offered the "flex" option. Must include prefixes such that gpt-5.3-codex, gpt-5.2-codex, gpt-5.1-codex-max, gpt-5-codex, gpt-4.1, gpt-4.1-mini, gpt-4.1-nano, gpt-4o (including gpt-4o-2024-05-13 and gpt-4o-mini), gpt-5-chat-latest, gpt-5.2-pro, o3-mini do NOT have flex; while gpt-5.4, gpt-5.4-pro, gpt-5.2, gpt-5.1, gpt-5, gpt-5-mini, gpt-5-nano, o3, o4-mini DO have flex.

Type: Constant
Name: UNSUPPORTED_PRIORITY_SERVICE_TIERS_MODELS
Location: homeassistant/components/openai_conversation/const.py
Signature: UNSUPPORTED_PRIORITY_SERVICE_TIERS_MODELS: list[str] = [...]
Description: List of model name prefixes that do not support the "priority" service tier. Models whose names start with any entry in this list will not be offered the "priority" option. Must include prefixes such that gpt-5-nano, gpt-5-chat-latest, gpt-5.2-pro, o3-mini do NOT have priority; while gpt-5.4, gpt-5.4-pro, gpt-5.2, gpt-5.1, gpt-5, gpt-5-mini, gpt-5.3-codex, gpt-5.2-codex, gpt-5.1-codex-max, gpt-5-codex, gpt-4.1, gpt-4.1-mini, gpt-4.1-nano, gpt-4o, gpt-4o-2024-05-13, gpt-4o-mini, o3, o4-mini DO have priority.

Type: Config Flow Method
Name: async_step_model (model step of OpenAISubentryFlowHandler)
Location: homeassistant/components/openai_conversation/config_flow.py
Description: The "model" step of the subentry config flow must conditionally include CONF_SERVICE_TIER as a SelectSelector in the step schema when the chosen model supports at least one of "flex" or "priority". The options list must be ordered: ["auto"], optionally "flex", ["default"], optionally "priority". When neither is supported, the CONF_SERVICE_TIER field must be absent from the schema. If the current stored value for CONF_SERVICE_TIER is not in the available options, it must be removed from the options dict. The field must use a dropdown SelectSelector with translation_key=CONF_SERVICE_TIER and default=RECOMMENDED_SERVICE_TIER.

Type: Entity Method (API call logic)
Name: _async_handle_chat_log (in OpenAIBaseLLMEntity or equivalent)
Location: homeassistant/components/openai_conversation/entity.py
Description: When constructing the API request parameters, must include service_tier=options.get(CONF_SERVICE_TIER, RECOMMENDED_SERVICE_TIER). When a RateLimitError is caught and the current service_tier is "flex" and the error message contains "resource unavailable" (case-insensitive), must set service_tier to "default" and retry the API call (using continue in the retry loop) rather than raising an error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.