I'm building long-running agentic loops with certain Anthropic models and I'd like to configure a loop-wide token budget so the model can pace itself and wrap up gracefully as the budget runs low.

*   AnthropicModelSettings must accept a new 'anthropic_task_budget' field that takes a dict with keys 'type' (value 'tokens'), 'total' (int), and optionally 'remaining' (int).

*   When 'anthropic_task_budget' is set, the model must include it in 'output_config' under the 'task_budget' key in the API request body.

*   When 'anthropic_task_budget' is set, the beta string 'task-budgets-2026-03-13' must be automatically added to the betas list, merged with any other configured betas.

*   When 'anthropic_task_budget' is combined with 'anthropic_effort', both values must appear together in 'output_config' as {'effort': '...', 'task_budget': {...}}.

*   AnthropicModel._build_output_config() must include 'task_budget' from 'anthropic_task_budget' settings when building the output config, merging it with any 'effort' value already present. With thinking='high' and anthropic_task_budget={'type': 'tokens', 'total': 2000} on a model with anthropic_supports_task_budgets=True, the result must be {'effort': 'high', 'task_budget': {'type': 'tokens', 'total': 2000}}.

*   AnthropicModelProfile must have a new boolean field 'anthropic_supports_task_budgets' with a default value of False.

*   anthropic_model_profile('claude-opus-4-7') must return a profile with anthropic_supports_task_budgets=True. Models not starting with 'claude-opus-4-7' must have anthropic_supports_task_budgets=False.

*   If 'anthropic_task_budget' is configured but the model profile has anthropic_supports_task_budgets=False, a UserError must be raised with a message matching 'does not support `anthropic_task_budget`'.

*   If 'anthropic_task_budget' includes a 'remaining' key and AnthropicCompaction is among the agent's capabilities (which adds a compact edit to context_management), a UserError must be raised with a message matching 'cannot be combined with `AnthropicCompaction`'.

*   If 'anthropic_task_budget' includes a 'remaining' key and the message history contains a CompactionPart (which implicitly triggers a compact_20260112 edit), the same UserError must be raised matching 'cannot be combined with `AnthropicCompaction`'.

*   If 'anthropic_task_budget' includes a 'remaining' key but context management uses only non-compact edit types (e.g. clear_tool_uses_20250919), the request must succeed without raising an error.

*   Sampling parameter warnings for models that disallow sampling settings must be deduplicated across direct settings fields and extra_body. When a parameter appears in both, only a single combined warning must be issued. The warning format must be: "Sampling parameters ['<param1>', '<param2>'] are not supported by '<model_name>'. These settings will be ignored." The original settings dict passed to the model must not be mutated.

*   Error messages for unsupported thinking budget types must use the model name in quoted form (e.g., "'claude-opus-4-7' does not support") rather than a human-readable name.


*   Interface details: Type: Class
Name: AnthropicModelSettings
Location: pydantic_ai_slim/pydantic_ai/models/anthropic.py
Description: TypedDict for Anthropic-specific model settings. Must gain a new 'anthropic_task_budget' field.
Signature: anthropic_task_budget: AnthropicTaskBudget  (optional field, TypedDict-style)
  - AnthropicTaskBudget is a dict with keys: 'type' (str, value 'tokens'), 'total' (int), and optionally 'remaining' (int).

Type: Class
Name: AnthropicModelProfile
Location: pydantic_ai_slim/pydantic_ai/profiles/anthropic.py
Description: Dataclass holding Anthropic-specific model capability flags. Must gain a new 'anthropic_supports_task_budgets' field.
Signature: anthropic_supports_task_budgets: bool = False

Type: Function
Name: anthropic_model_profile
Location: pydantic_ai_slim/pydantic_ai/profiles/anthropic.py
Description: Returns an AnthropicModelProfile for a given model name. Must set anthropic_supports_task_budgets=True for model names starting with 'claude-opus-4-7'.
Signature: anthropic_model_profile(model_name: str) -> ModelProfile | None

Type: Method
Name: _build_output_config
Location: pydantic_ai_slim/pydantic_ai/models/anthropic.py
Description: Builds the output_config dict sent to the Anthropic API. Must include 'task_budget' from anthropic_task_budget settings when present, merged alongside any 'effort' key. Returns None when no output format, effort, or task budget is set.
Signature: _build_output_config(self, model_request_parameters: ModelRequestParameters, model_settings: AnthropicModelSettings) -> BetaOutputConfigParam | None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.