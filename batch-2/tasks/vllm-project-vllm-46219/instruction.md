Update the completions API to support echo functionality with token-ID prompts. Ensure that the server decodes token IDs back to text for echo purposes and handles related options correctly.

*   Modify the `prepare_completion_request` function in `rust/src/server/src/routes/openai/completions/convert.rs`:
    *   Accept a new fourth parameter of type `&dyn Tokenizer` to decode token-ID prompts.
    *   When echo is enabled and the prompt is token IDs without `return_token_ids`, decode the token IDs using the tokenizer's `decode` method and store the result as the echo text in `PreparedRequest.options.echo`.
    *   When echo is enabled with `return_token_ids`, set `options.echo` to an empty string and ensure token IDs are returned via separate response fields.
    *   Ensure all callers of `prepare_completion_request` pass a tokenizer reference.

*   Update the `validate_request_compat` function in `rust/src/server/src/routes/openai/completions/validate.rs`:
    *   Remove the restriction that rejects requests combining a token-ID prompt with echo enabled.

*   Implement the following expected behaviors:
    *   For non-streaming requests with echo enabled and a token-ID prompt:
        *   Prepend the decoded prompt text to the completion text in `choices[0]['text']`.
    *   For non-streaming requests with both echo and `return_token_ids` enabled:
        *   Return only the generated completion text in `choices[0]['text']`.
        *   Include `choices[0]['prompt_token_ids']` with the original input token IDs and `choices[0]['token_ids']` with the completion's token IDs.
    *   For streaming requests with echo enabled and a token-ID prompt:
        *   Emit a chunk containing the decoded prompt text before any generated content chunks.

*   Ensure usage statistics correctly report `prompt_tokens` as the count of input token IDs.
*   Ensure all valid token-ID echo requests return an HTTP status of 200 OK.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.