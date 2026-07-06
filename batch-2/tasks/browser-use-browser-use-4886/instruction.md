Implement a module to accurately track token costs for AI agents using OpenRouter as the model provider. Ensure the system can fetch pricing from OpenRouter's API when local data is unavailable, handle distinct pricing for cache-read tokens, and use the correct pricing source for registered OpenRouter models.

*   Create a module at `browser_use/tokens/openrouter_pricing.py`:
    *   Importable as `browser_use.tokens.openrouter_pricing`.
    *   Expose a function `model_pricing_from_openrouter_metadata(model_name: str, metadata: dict) -> ModelPricing | None` to convert OpenRouter metadata to a `ModelPricing` object.
    *   Expose an async function `get_openrouter_models_metadata(refresh: bool = False) -> dict[str, dict]` to fetch OpenRouter model metadata.
    *   Expose an async function `get_openrouter_model_pricing(model_name: str) -> ModelPricing | None` to return pricing using OpenRouter metadata, handling provider prefixes.

*   Update `TokenCost` in `browser_use/tokens/service.py`:
    *   Implement `get_model_pricing(model_name: str) -> ModelPricing | None` to fall back on `get_openrouter_model_pricing` if a model is not found locally.
    *   Implement `calculate_cost(model: str, usage: ChatInvokeUsage) -> TokenCostCalculated | None` to correctly split prompt token costs using distinct rates for cached and non-cached tokens.
    *   Implement `register_llm(llm)` to ensure OpenRouter-backed models use OpenRouter pricing with the correct prefixed identifier.

*   Ensure `ModelPricing` in `browser_use/tokens/views.py` supports:
    *   Fields: `model`, `input_cost_per_token`, `output_cost_per_token`, `cache_read_input_token_cost`, `cache_creation_input_token_cost`, `max_tokens`, `max_input_tokens`, `max_output_tokens`.

*   Ensure `ChatInvokeUsage` in `browser_use/llm/views.py` supports:
    *   Fields: `prompt_tokens`, `prompt_cached_tokens`, `prompt_cache_creation_tokens`, `prompt_image_tokens`, `completion_tokens`, `total_tokens`.

*   Ensure `ChatOpenRouter` in `browser_use/llm/openrouter/chat.py`:
    *   Instantiable with `model` and `api_key`.
    *   Recognized by `TokenCost.register_llm` to redirect cost lookups to OpenRouter pricing with the correct prefixed model name.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.