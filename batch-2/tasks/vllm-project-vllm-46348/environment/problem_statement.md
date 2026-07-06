## Description

The chat completions and completions API endpoints do not validate whether the list of allowed token IDs is empty before processing the request. Submitting a request with an empty token ID constraint list is logically invalid — if no tokens are allowed, generation cannot proceed meaningfully — but instead of returning a clear error, the server currently propagates this to the backend where it causes an internal server error (HTTP 500).

## Expected Behavior

- When a client submits a generation request (to either the chat completions or the plain completions endpoint) with an empty list of allowed token IDs, the server should immediately reject it with a 400 Bad Request response.
- The response body should follow the standard OpenAI error format, with the error type set to indicate an invalid request and a message that clearly explains the problem with the empty token ID list.

## Current Behavior

Both endpoints currently return HTTP 500 (Internal Server Error) when an empty allowed token ID list is provided, rather than giving the client an actionable error message.

## Why This Matters

Returning 500 for what is clearly a client-side input error is confusing and unhelpful. Users have no indication of what went wrong or how to fix it. Proper input validation at the API boundary makes the service more robust and developer-friendly, and keeps the error response consistent with the OpenAI API error format that clients already expect.
