## Description

The server-side handlers for processing mutations and queries currently use a positional parameter style that makes it cumbersome to associate an authenticated user's identity with each request. Passing user identity requires a trailing options object that is easy to forget or omit, and the behavior when it is missing is inconsistent — logged-out users aren't clearly distinguished from cases where user identity simply wasn't provided.

We want to move to a unified, object-based calling convention for both the mutation handler and the query handler. This makes it explicit which user's request is being processed, and ensures that logged-out states are clearly reflected in responses. When a user's identity has not yet been resolved, it should be treated the same as an explicitly absent identity so downstream consumers always get a consistent shape.

## Expected Behavior

- Both the mutation handler and the query handler should accept a single options object that bundles all arguments — the database/handler reference, the request body or Request object, and the authenticated user ID.
- When a user is logged in, their ID should be echoed back in the response.
- When a user is logged out, the response should explicitly reflect an absent user identity.
- When a user's identity has not yet been resolved, it should be treated the same as an explicitly absent identity in the response.
- The legacy positional calling convention should remain supported for backwards compatibility, with responses through that path omitting the user ID field.
- The two previously separate query-handling functions should be unified: the behaviors previously covered by a distinct "get queries" handler should be accessible through the main query handler.

## Why This Matters

Developers integrating these handlers need a clear and consistent way to communicate user authentication state through the request pipeline. The old design made it easy to accidentally omit user context or produce responses with unpredictable shapes. Unifying the API also reduces the number of exported symbols that callers need to learn and maintain.
