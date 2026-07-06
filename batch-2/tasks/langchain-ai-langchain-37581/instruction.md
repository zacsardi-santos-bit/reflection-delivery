Update the Fireworks AI chat model integration to be compatible with the latest Fireworks SDK version 1.x. Implement changes to accommodate the new error hierarchy, method naming conventions, and configuration requirements.

*   Import error classes from the top-level `fireworks` package.
    *   Replace `InvalidRequestError` with `BadRequestError`.
    *   Replace `ServiceUnavailableError` with `InternalServerError`.
    *   Ensure `AuthenticationError`, `RateLimitError`, and `FireworksError` are imported from the top-level.
*   Define `FireworksContextOverflowError` in `libs/partners/fireworks/langchain_fireworks/chat_models.py`.
    *   Subclass it from `ContextOverflowError` and `BadRequestError`.
    *   Preserve `.response` and `.body` attributes when promoting from `BadRequestError`.
*   Promote `BadRequestError` with context-overflow message to `FireworksContextOverflowError`.
    *   Do not promote unrelated `BadRequestError` exceptions.
*   Update async client method naming:
    *   Use `create` for async calls, defined as `async def create(...)`.
*   Handle streaming usage tracking:
    *   When `stream_usage=True`, pass `stream_options` inside `extra_body["stream_options"]`.
    *   When `stream_usage=False`, omit `stream_options` and `extra_body`.
    *   If `stream_options` are provided both at the top level and inside `extra_body`, prioritize `extra_body` and log a warning.
*   Construct SDK clients with `max_retries=0` to disable internal retries.
*   Normalize legacy timeout values:
    *   Convert tuple `(connect, read)` to `httpx.Timeout` object.
    *   Pass the `httpx.Timeout` object to both sync and async clients.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.