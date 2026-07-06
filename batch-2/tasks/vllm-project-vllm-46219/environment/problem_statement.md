## Description

The completions API currently rejects any request that uses a list of token IDs as the prompt when the echo option is also enabled. This is unnecessarily restrictive — clients that work at the token level (rather than raw text) should be able to use echo just like text-prompt clients can.

When a user provides a list of token IDs as their prompt and requests echo, the server should decode those token IDs back to text and include the decoded text as the echo prefix in the response, exactly as it would if the prompt had been provided as a string.

There is also an interaction with the "return token IDs" option that needs to work correctly: if a client requests both echo and token ID output, the prompt's token IDs should be returned separately in their own field rather than being mixed into the main text output.

## Expected Behavior

- Sending a token-ID prompt with echo enabled (non-streaming) returns a successful response where the decoded prompt text is prepended to the completion text in the output.
- Usage counts must correctly reflect the number of input token IDs as the prompt token count.
- Sending a token-ID prompt with both echo and "return token IDs" enabled returns the completion text alone (without the decoded prefix), plus separate fields for the prompt token IDs and the completion token IDs.
- Sending a token-ID prompt with echo enabled in streaming mode emits the decoded prompt text as a dedicated chunk before any generated content chunks.

## Why This Matters

This prevents users who work with token IDs from being able to use the echo feature at all, which is an arbitrary limitation. The echo feature should be available regardless of whether the prompt is specified as text or as token IDs.
