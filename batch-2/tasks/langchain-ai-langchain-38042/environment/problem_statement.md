## Description

The logic for detecting whether a model supports native structured output has fallen out of date with current model releases. When developers use the agent framework with newer generations of Anthropic models (version 4.5 and above), those models are not recognized as supporting structured output, so the framework falls back to a less reliable strategy. Additionally, some model names that should NOT be treated as structured-output-capable are currently slipping through the detection: models with "pro" and "oss" qualifiers from certain providers, as well as image/video-generation models, are being incorrectly flagged.

## Expected Behavior

- Newer Anthropic model generations (version 4.5 and higher), including dated variants and models with provider prefixes, should be recognized as supporting native structured output.
- Newer Claude model families with version 5 and above (including forward-looking or creative naming schemes) should also be recognized.
- OpenAI model names containing "oss" or ending in "-pro" should be blocked (they do not support this capability).
- Image/video-oriented models from any provider should be blocked.
- When checking a bare model name string, any tool-list argument should be ignored (tool-based filtering only applies to profile objects).
- All existing detection behavior for currently supported models (GPT-4.x, GPT-5.x, grok) should remain unchanged.

## Why This Matters

Agents built on this framework rely on accurate detection to route structured output requests to the right strategy. Misdetection forces a less reliable fallback for models that should use native structured output, and may silently allow unsupported models to attempt the wrong strategy. Keeping the detection current ensures correct behavior as new model generations are released.
