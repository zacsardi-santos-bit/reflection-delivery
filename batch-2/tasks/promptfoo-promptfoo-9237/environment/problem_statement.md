# xAI Provider: Support Grok 4.3 Models, Fix Legacy Pricing, and Improve Streamed Response Handling

## Description

The xAI provider needs several updates to keep pace with the latest model catalog from xAI.

First, the new Grok 4.3 model family needs to be recognized as supporting reasoning effort configuration. Currently, if you try to set a reasoning effort parameter when using Grok 4.3 models, the provider strips it because the model isn't flagged as supporting that feature. The parameters that are not supported on the Grok 4.x family (like presence penalty, frequency penalty, and stop sequences) should still be filtered out for these models.

Second, a number of older model slugs that have been retired or redirected by xAI are currently returning incorrect or missing cost estimates. Models like various Grok-3 and Grok-4 variants (including fast, beta, and dated slug variants) should redirect to the Grok 4.3 pricing tier (approximately $3.75 per million tokens for both input and output).

Third, a new line of image generation models ("quality" variants) has been released by xAI. These need to be registered as valid model identifiers. The dated and "latest" alias variants should route to the canonical quality model slug when making API requests. Unknown or future model slugs should be passed through as-is rather than silently replaced with a fallback. Pricing for quality-tier generation and editing should be $0.05 per image (generation) and $0.07 per image (editing with resolution or source images). The existing pro model's editing cost should follow quality-tier pricing (approximately $0.06).

Finally, when using the streaming responses API with models that support reasoning, the internal reasoning summaries that appear in the stream should not leak into the visible output text. Currently, reasoning summary content can appear in the output or interfere with parsing. The streamed output should only contain the final answer text. Additionally:
- Annotations attached to output text in the completed response event should be preserved and accessible in the result
- Non-message output items (such as web search tool calls) should be preserved in the output rather than being silently dropped
- If malformed events appear in the stream, the provider should recover gracefully by falling back to the completed response rather than returning truncated output
- Encrypted reasoning content from completed response events should be accessible in the raw response data without leaking into the visible output

## Expected Behavior

- Grok 4.3 and Grok 4.3-latest are recognized as supporting reasoning effort control
- Reasoning effort is preserved in requests to Grok 4.3; incompatible parameters are stripped
- Legacy and redirected model slugs produce correct cost estimates based on Grok 4.3 pricing
- New image quality model variants are supported as valid identifiers
- Quality-latest and dated aliases route to the canonical quality slug in API requests
- Unknown future image slugs are passed through unchanged
- Streamed responses exclude reasoning summaries from visible output
- Annotations and non-message tool call items from completed stream events are preserved
- Encrypted reasoning content stays in raw data, not in visible output
- Malformed stream events trigger fallback to the completed response text

## Why This Matters

Without these updates, users cannot take advantage of the new Grok 4.3 models with reasoning control, cost estimates for many model variants are inaccurate, newly released image models are unrecognized, and streamed responses from reasoning models expose internal chain-of-thought text to end users rather than cleanly separating it from the final answer.
