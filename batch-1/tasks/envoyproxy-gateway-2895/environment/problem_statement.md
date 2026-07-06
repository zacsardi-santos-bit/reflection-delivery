## Description

The policy resource for controlling client traffic behavior currently defines its own custom status type to report conditions. This is inconsistent with how similar policy resources across the project report their status — those resources all use the standard status type from the shared API specification, which properly tracks conditions per gateway ancestor rather than as a flat list directly on the policy.

As a result, the auto-generated API reference documentation contains extra blank lines in the area where the custom status type definition appears, because the documentation generator emits formatting for this bespoke type. Once the custom status type is replaced with the standard shared type (which is defined in an external package and not re-documented locally), those formatting artifacts disappear and the documentation becomes clean.

## Expected Behavior

- The client traffic policy resource should report its status using the same standard type used by other policy resources in the project
- The custom status type specific to this resource should be removed
- The auto-generated API documentation should not contain extra blank lines between the client connection timeout section and the client validation context section
- The API documentation file checked into the repository must stay in sync with what the documentation generator produces from the source types

## Why This Matters

Using a bespoke status type creates maintenance overhead and makes the API inconsistent for users who work with multiple policy resource types. Adopting the standard shared status type aligns the resource with upstream conventions, makes status reporting consistent across all policy types, and keeps the documentation accurate and cleanly formatted.
