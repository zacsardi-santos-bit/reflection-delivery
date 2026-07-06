## Description

The text-to-speech API has two reliability problems that need to be fixed together.

**Problem 1: No input validation on the phoneme conversion endpoint**

The endpoint that converts text into phonemes has no guard against empty input. When a caller sends an empty string as the text, the server crashes with an unhandled internal error instead of returning a clean, structured error response. It should validate that the text is non-empty and return a proper error (HTTP 500 with an error object in the response body) so callers can handle it gracefully.

**Problem 2: No standard way to access the active model instance**

The model layer does not provide a method to retrieve the currently loaded model or query which hardware device it's running on. Other parts of the system — including the main service class — have to work around this by duplicating initialization logic or bypassing the model lifecycle entirely. This makes the code fragile and impossible to test in isolation: there is no stable hook to replace the model with a lightweight stand-in during testing.

## Expected Behavior

- Sending an empty text string to the phoneme conversion route returns an HTTP 500 response whose body contains a structured error object with at least one error indicator field.
- The model class exposes an instance accessor method that returns the active model object (raising an error if the model has not been initialized), and a device query method that returns the current hardware device string.
- The service class uses the instance accessor during initialization, making the model dependency explicit and testable.
- The model setup flow correctly stores the initialized model object on the class so that the instance accessor can return it.

## Why This Matters

Without these changes, it is impossible to write reliable unit tests for the service layer (every test attempt errors out because the model singleton cannot be injected), and callers of the phoneme endpoint receive cryptic server crashes instead of actionable errors.
