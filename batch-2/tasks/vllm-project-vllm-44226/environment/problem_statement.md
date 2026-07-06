## Description

The prompt rendering endpoints currently return token IDs but provide no way for callers to know where each token starts and ends in the original prompt text. This makes it necessary to re-run the tokenizer separately with character-offset tracking enabled in order to align tokens with character positions — adding complexity and extra work.

We should add an opt-in flag to both the completions render endpoint and the chat completions render endpoint that, when set, causes the response to include per-token character offset pairs. Each pair should indicate the start and end character positions of the token within the source text.

## Expected Behavior

- When the flag is enabled in a request to the completions render endpoint, each item in the response should include a list of character offset pairs (one per token), where each pair gives the start and end position of the token within the prompt string.
- When the flag is not set, the offset field in the response should be null, preserving backward compatibility.
- The same flag and behavior should apply to the chat completions render endpoint, where offsets are relative to the fully templated prompt string.
- For batch requests to the completions render endpoint, each prompt in the batch should independently receive its own offset list.
- If the tokenizer does not support fast offset computation (e.g. it is a slow tokenizer), or if the request includes multimodal content, the offset field should be null rather than raising an error.
- The internal tokenization parameters must propagate the flag so that all layers from the request object through to the renderer correctly honor the setting.
- The serialization format used between components must be able to carry the offset data through without loss.

## Why This Matters

Token-to-character alignment is needed in use cases such as span extraction, highlighting, and structured output post-processing. Without built-in support, callers must perform this alignment externally, which is redundant and error-prone. Exposing offsets directly from the render endpoint makes such use cases easier to implement correctly.
