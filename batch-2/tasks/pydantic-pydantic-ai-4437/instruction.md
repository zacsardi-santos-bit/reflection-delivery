I'm working with the Google AI model in streaming mode and I've noticed that errors raised mid-stream aren't being wrapped the same way as errors from non-streaming calls.

*   When an HTTP error (status code >= 400) is raised by the Google AI SDK during stream iteration, the streaming response must raise ModelHTTPError instead of allowing the raw SDK exception to propagate.

*   The ModelHTTPError raised for streaming HTTP errors must have its status_code attribute set to the HTTP status code from the SDK error (e.g., 503 for service unavailable, 429 for rate limit exceeded).

*   The ModelHTTPError raised for streaming HTTP errors must have its body attribute set to the SDK error's details such that str(body) contains the error message string from the original error response.

*   When a non-HTTP API error (status code < 400, e.g., a redirect with code 302) is raised by the Google AI SDK during stream iteration, the streaming response must raise ModelAPIError instead of allowing the raw SDK exception to propagate.

*   The ModelAPIError raised for streaming non-HTTP errors must have its model_name attribute set to the name of the model that was being used (e.g., 'gemini-1.5-flash').

*   The error wrapping must apply even when the error occurs after partial content has already been yielded — i.e., mid-stream errors are caught and translated consistently.


*   Interface details: NO INTERFACES NEEDED

The change required is entirely internal to an existing method in an existing class. No new public functions or classes need to be introduced. The tests exercise the behavior through the existing public streaming API (`Agent.run_stream`) and verify the types and attributes of the exceptions that propagate out. The existing `ModelHTTPError` and `ModelAPIError` exception classes are already part of the public API and are unchanged.

The implementation change lives inside `GeminiStreamedResponse._get_event_iterator()` in `pydantic_ai_slim/pydantic_ai/models/google.py`. The async-for loop over the response stream must be wrapped in a try/except block that catches `errors.APIError` from the Google AI SDK:
- If `error.code >= 400`, raise `ModelHTTPError(status_code=error.code, model_name=self._model_name, body=error.details)`.
- Otherwise, raise `ModelAPIError(model_name=self._model_name, message=str(error))`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.