I'm running evaluations through the Vertex AI integration and I've noticed that cost is always missing from the results — it comes back as undefined for every call, even for well-known models that have published pricing.

*   The calculateGoogleCost function must accept an optional fifth parameter isVertexMode (boolean) that, when true and the model has Vertex-specific pricing defined, causes it to use that Vertex pricing instead of the standard AI Studio pricing.

*   When isVertexMode is true and the model does not have Vertex-specific pricing, calculateGoogleCost must fall back to the standard pricing and return the same value it would in non-Vertex mode.

*   The GoogleModel data structure must support an optional vertexCost field (with input and output cost-per-token sub-fields) for models whose Vertex AI pricing differs from AI Studio pricing.

*   The model gemini-2.0-flash must have Vertex-specific pricing of input=0.15/1M tokens and output=0.60/1M tokens (compared to AI Studio pricing of input=0.10/1M and output=0.40/1M).

*   The model gemini-embedding-001 must have pricing defined as input=0.15/1M tokens and output=0 tokens.

*   The model gemini-robotics-er-1.5-preview must have pricing defined as input=0.30/1M tokens and output=2.50/1M tokens.

*   The model gemini-3-pro-preview must have tiered pricing: base rates of input=2.0/1M and output=12.0/1M tokens, with higher rates of input=4.0/1M and output=18.0/1M tokens applied when prompt tokens exceed 200,000.

*   GoogleProvider.callApi must calculate and return cost for Vertex AI mode calls (not return undefined for all Vertex calls), passing the Vertex mode flag to calculateGoogleCost.

*   GoogleProvider.callApi must continue to return undefined cost for cached responses regardless of mode.

*   VertexChatProvider.callGeminiApi must calculate and include a cost field in its return value, using Vertex AI pricing when applicable.

*   VertexChatProvider.callGeminiApi must return cost as undefined when the API response does not include usage metadata.

*   VertexChatProvider.callGeminiApi must preserve all fields from a cached response — including cost and metadata — when returning a cached result.


*   Interface details: Type: Function
Name: calculateGoogleCost
Location: src/providers/google/util.ts
Signature: calculateGoogleCost(modelName: string, config: ProviderConfig, promptTokens?: number, completionTokens?: number, isVertexMode?: boolean): number | undefined
Description: Calculates the cost of a Google API call based on model name, token counts, and whether the call is made via Vertex AI. When isVertexMode is true and the model has Vertex-specific pricing, that pricing is used instead of the standard AI Studio pricing. Falls back to standard pricing for models without Vertex-specific rates.

Type: Interface
Name: GoogleModel
Location: src/providers/google/shared.ts
Description: Describes a Google AI model entry in the GOOGLE_MODELS array. Must include an optional vertexCost field of type GoogleModelCost (with input and output numeric properties) to support models where Vertex AI pricing differs from AI Studio pricing.
Signature: { id: string; cost?: GoogleModelCost; tieredCost?: GoogleModelTieredCost; vertexCost?: GoogleModelCost; }

Type: Constant
Name: GOOGLE_MODELS
Location: src/providers/google/shared.ts
Description: Array of GoogleModel objects. Must include entries for gemini-embedding-001 (input=0.15/1M, output=0), gemini-robotics-er-1.5-preview (input=0.3/1M, output=2.5/1M), gemini-3-pro-preview with tiered pricing (base: input=2.0/1M, output=12.0/1M; above 200k tokens: input=4.0/1M, output=18.0/1M), and gemini-2.0-flash must have vertexCost set to input=0.15/1M, output=0.6/1M.

Type: Method
Name: callGeminiApi
Location: src/providers/google/vertex.ts
Signature: callGeminiApi(prompt: string, context?: CallApiContextParams, callApiOptions?: CallApiOptionsParams): Promise<ProviderResponse>
Description: Calls the Vertex AI Gemini API and returns a ProviderResponse that includes a cost field. Cost is calculated using Vertex AI pricing when available. Returns cost as undefined when the response lacks usage metadata. When returning a cached response, must preserve the cost and metadata fields from the cached value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.