## Description

The eval API is missing formal schema definitions for two of its response types — the eval table response and the rating submission response. There are also some gaps in input validation for the eval table endpoint's query parameters.

## Expected Behavior

- The eval API type definitions should include schemas that can be used to validate the shape of the eval table response and the rating submission response before they are sent to clients. The eval table response schema should shallowly validate the table's row collection (confirming it is an array) without deep-parsing individual row objects, since those can be extremely large. The schema should also enforce that the version field is a number.

- The eval table endpoint should validate that the format query parameter, when provided, is one of the recognized export formats. Requests with an unrecognized format value should be rejected with a 400 error identifying the invalid field.

- The eval table endpoint should validate that the limit and offset query parameters, when provided, are whole numbers. Passing a decimal value for either should be rejected with a 400 error identifying the invalid field.

- In all invalid-input cases, the validation error must be returned before any database work is attempted.

## Why This Matters

Without a response schema, malformed API responses could reach clients undetected. Without strict query parameter validation on the format, limit, and offset parameters, the server silently accepts inputs that it cannot handle correctly, potentially leading to unexpected runtime errors downstream.
