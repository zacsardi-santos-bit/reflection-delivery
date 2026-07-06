## Description

We need a new Spring Security module that demonstrates how to detect and reject passwords that are known to be compromised. Currently there is no example module in the tutorials repository showing how to integrate compromised-password checking into a real user-registration flow.

## Expected Behavior

- A user registration endpoint should accept a new user's email address and password.
- If the submitted password has been flagged as compromised, the registration must be rejected with a 400 Bad Request response. The response body should clearly communicate that the password is compromised and cannot be used.
- If the password is not compromised, the user should be created successfully and the endpoint should return a 200 OK response.
- The same compromised-password check should also be available as a reusable bean-validation constraint that developers can apply directly to their request data objects. When that constraint is violated, it must produce a human-readable validation message indicating that the password is compromised and cannot be used.

## Why This Matters

Many applications accept weak or previously-leaked passwords during registration, exposing users to credential-stuffing attacks. Providing a working reference implementation — at both the REST layer and the validation-constraint layer — gives developers a clear, copy-paste-ready pattern for integrating compromised-password detection into their own Spring Boot applications.
