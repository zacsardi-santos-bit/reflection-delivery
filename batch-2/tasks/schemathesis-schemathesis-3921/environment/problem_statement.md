## Description

Schemathesis currently supports several web framework error formats — but it doesn't recognize structured validation responses from Microsoft's web framework. When an API built on that stack rejects an invalid request, it returns a standardized error envelope containing per-field validation messages (required field missing, string too short, value out of range, invalid format, etc.). Without understanding this format, Schemathesis cannot learn from these 400 responses and keeps generating inputs that continue to fail validation — potentially masking real bugs (like 500 errors) that only become visible when a valid input slips through.

## Expected Behavior

- The error feedback system should recognize responses that follow Microsoft's standardized validation error envelope format.
- Both the built-in validation attribute style and the popular third-party fluent validation style should be parsed.
- Recognized messages should be converted to structured observations: required-field constraints, string length bounds, numeric range bounds, email format expectations, and regex pattern requirements.
- Field names in PascalCase should be normalized to lowercase when building parameter paths.
- Pseudo-fields (like JSON pointer references and deserialization placeholders) should be ignored.
- Messages that don't match any known pattern should be silently skipped.
- The parser should take priority over the more general parser that can also match these responses when both apply.
- Other framework-specific parsers should not falsely claim these responses.

## Why This Matters

APIs built with Microsoft's stack are extremely common. Without this support, Schemathesis systematically fails to use the structured error feedback those APIs provide, leaving potential bugs permanently hidden behind the validation gate. This change enables full error-feedback-driven testing for this class of API.
