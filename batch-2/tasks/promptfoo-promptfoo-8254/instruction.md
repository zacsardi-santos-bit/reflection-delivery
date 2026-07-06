I'm trying to track costs accurately across different AI providers, but I can only set a single unified cost per token in the provider configuration.

*   The calculateCost function (src/providers/shared.ts) must accept inputCost and outputCost as separate fields in the config object, and when both are provided they must take precedence over config.cost and any model-defined pricing including long-context tier rates.

*   When calculateCost is called with only one of inputCost or outputCost set alongside config.cost, config.cost must be used as the fallback for the unset direction: if only inputCost is provided then outputCost falls back to config.cost, and if only outputCost is provided then inputCost falls back to config.cost.

*   The calculateAnthropicCost function (src/providers/anthropic/util.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost and any tiered pricing for both tiered models (e.g. claude-sonnet-4-5-20250929) and non-tiered models.

*   When calculateAnthropicCost uses inputCost/outputCost overrides with cache tokens, the inputCost rate must also be used for cache_read tokens (at a 0.1 multiplier) and cache_write tokens (at a 1.25 multiplier), while outputCost is applied to output tokens.

*   When calculateAnthropicCost is called with config.cost alongside only one of inputCost or outputCost, config.cost must serve as the fallback for the unset direction (e.g. only inputCost set means outputCost falls back to config.cost, and vice versa).

*   When calculateAnthropicCost is called with all three of config.cost, inputCost, and outputCost, inputCost and outputCost must take priority over config.cost.

*   The calculateOpenAICost function (src/providers/openai/util.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost. When only one is provided alongside config.cost, config.cost is used as the fallback for the unset direction.

*   The calculateOpenAICost function must also accept audioInputCost and audioOutputCost as separate config fields for audio tokens; when both are provided they override config.audioCost.

*   When calculateOpenAICost is called with only audioInputCost and no audioOutputCost or audioCost, the model default must be used for the audio output side.

*   When calculateOpenAICost is called with audioInputCost and audioCost but no audioOutputCost, audioCost must be used as the fallback for the audio output side.

*   When calculateOpenAICost is called with all three of audioCost, audioInputCost, and audioOutputCost, audioInputCost and audioOutputCost must take priority.

*   The calculateDeepSeekCost function (src/providers/deepseek.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost. Cache hit tokens must be discounted using the inputCost rate when inputCost/outputCost overrides are active.

*   The calculateGoogleCost function (src/providers/google/util.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost, tiered pricing rates, and Vertex-specific pricing for any model.

*   The calculateHyperbolicCost function (src/providers/hyperbolic.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost.

*   The calculateXAICost function (src/providers/xai/chat.ts) must accept inputCost and outputCost as separate config fields; when both are provided they override config.cost.


*   Interface details: Type: Function
Name: calculateCost
Location: src/providers/shared.ts
Signature: calculateCost(modelId: string, config: { cost?: number; inputCost?: number; outputCost?: number }, promptTokens: number, completionTokens: number, models: ModelCostMetadata[]) -> number | undefined
Description: Calculates token cost for a given model. Must support separate inputCost and outputCost config fields that override config.cost and any model-defined long-context tier rates. When only one of inputCost/outputCost is set alongside cost, config.cost acts as the fallback for the unset direction.

Type: Function
Name: calculateAnthropicCost
Location: src/providers/anthropic/util.ts
Signature: calculateAnthropicCost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number }, inputTokens?: number, outputTokens?: number, cacheReadTokens?: number, cacheWriteTokens?: number) -> number | undefined
Description: Calculates token cost for Anthropic models. Must support separate inputCost and outputCost config fields that override config.cost and tiered model pricing. When overrides are active and cache tokens are present, inputCost is also applied to cache_read tokens (×0.1) and cache_write tokens (×1.25). When only one of inputCost/outputCost is set alongside cost, config.cost is the fallback for the unset direction.

Type: Function
Name: calculateOpenAICost
Location: src/providers/openai/util.ts
Signature: calculateOpenAICost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number; audioCost?: number; audioInputCost?: number; audioOutputCost?: number }, promptTokens?: number, completionTokens?: number, audioInputTokens?: number, audioOutputTokens?: number) -> number | undefined
Description: Calculates token cost for OpenAI models. Must support separate inputCost/outputCost fields (taking priority over cost, with config.cost as fallback for the unset direction) and separate audioInputCost/audioOutputCost fields (taking priority over audioCost). When only audioInputCost is set and no audioCost is present, the model default is used for the audio output side; when only audioInputCost is set alongside audioCost, audioCost is used as the fallback for audio output.

Type: Function
Name: calculateDeepSeekCost
Location: src/providers/deepseek.ts
Signature: calculateDeepSeekCost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number }, promptTokens?: number, completionTokens?: number, cacheHitTokens?: number) -> number | undefined
Description: Calculates token cost for DeepSeek models. Must support separate inputCost and outputCost config fields that override config.cost. Cache hit tokens are discounted using the active input cost rate when inputCost/outputCost overrides are in effect.

Type: Function
Name: calculateGoogleCost
Location: src/providers/google/util.ts
Signature: calculateGoogleCost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number }, inputTokens?: number, outputTokens?: number, isVertex?: boolean) -> number | undefined
Description: Calculates token cost for Google models. Must support separate inputCost and outputCost config fields that override config.cost, tiered pricing rates, and Vertex-specific pricing when isVertex is true.

Type: Function
Name: calculateHyperbolicCost
Location: src/providers/hyperbolic.ts
Signature: calculateHyperbolicCost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number }, promptTokens?: number, completionTokens?: number) -> number | undefined
Description: Calculates token cost for Hyperbolic models. Must support separate inputCost and outputCost config fields that override config.cost.

Type: Function
Name: calculateXAICost
Location: src/providers/xai/chat.ts
Signature: calculateXAICost(modelName: string, config: { cost?: number; inputCost?: number; outputCost?: number }, promptTokens?: number, completionTokens?: number, reasoningTokens?: number) -> number | undefined
Description: Calculates token cost for xAI models. Must support separate inputCost and outputCost config fields that override config.cost.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.