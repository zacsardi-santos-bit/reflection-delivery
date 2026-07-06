## Description

The CSRF protection filter in the reactive HTTP extension does not correctly handle token validation for POST requests that carry a non-form request body. When a client sends the CSRF token via the designated custom request header (instead of as a form field), the filter fails to accept the request as valid — even when the token in the header exactly matches the token in the cookie. As a result, POST endpoints that consume non-form content types (such as plain text) reject all requests with HTTP 400, even those that are legitimately protected with a valid CSRF token.

## Expected Behavior

- A GET endpoint can generate and return a CSRF token as a cookie for use in subsequent protected requests.
- A POST endpoint that accepts form-encoded data should validate the CSRF token from the form field and reject mismatched or missing tokens with HTTP 400.
- A POST endpoint that has no request body should validate the CSRF token from the custom request header and reject mismatched or missing tokens with HTTP 400.
- A POST endpoint that accepts a non-form request body (e.g., plain text) should validate the CSRF token from the custom request header and, when the token is valid, proceed to process the request body normally, returning HTTP 200.

## Why This Matters

Any POST endpoint that accepts a non-form content type is currently unprotected even when developers correctly configure CSRF protection and clients supply the token through the header. This is a correctness bug: a valid, properly-authenticated CSRF request is being rejected, making CSRF protection unusable for non-form API endpoints.
