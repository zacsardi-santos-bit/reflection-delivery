## Description

The Braintree Android SDK's 3D Secure module has several robustness issues related to null inputs and API consistency that need to be addressed. When certain required objects are absent during the authentication flow, the SDK fails without providing a useful error message, making it very hard to diagnose what went wrong.

## Issues

- When a required challenge observer is missing during the 3D Secure lookup continuation step, the SDK should throw a clear exception rather than proceeding with undefined behavior.
- When the tokenization step receives a payment authentication result that is missing either the security parameters object or the authentication token, the SDK should immediately return a clear failure result with a descriptive error message, and record the appropriate failure analytics events.
- The method for sending HTTP POST requests used throughout the module needs an updated signature that accepts an additional map of headers alongside the existing URL, body, and callback parameters.
- The method for sending analytics events needs an updated signature that accepts an additional parameters object alongside the existing event name.
- An internal launcher component's field for tracking the active result launcher was previously accessible directly; it should be hidden behind a proper accessor method.
- A core data class used throughout the 3D Secure flow previously allowed instances to be created with all fields defaulting to null. All three fields should now be required explicitly, removing any default values.

## Expected Behavior

- Null inputs to key methods should produce descriptive exceptions or failure results immediately.
- POST and analytics calls across the module should use the updated, more expressive method signatures.
- The launcher component should expose a setter method rather than a public field.
- The data class constructor should require explicit values for all fields.

## Why This Matters

These changes improve the developer experience by making failures visible and actionable, and improve the SDK's internal consistency by ensuring APIs carry all necessary contextual information.
