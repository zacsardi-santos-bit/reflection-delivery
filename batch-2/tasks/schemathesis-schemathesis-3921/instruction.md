I'm working on adding support for parsing structured validation error responses from Microsoft's web framework to the Schemathesis error feedback system.

*   AspNetParser must be importable from schemathesis.core.error_feedback.parsers.aspnet and registered in the PARSERS registry so it participates in the error-feedback pipeline.

*   AspNetParser.priority (class attribute, int) must be strictly greater than DRFParser.priority (which is 3).

*   AspNetParser.can_parse() must return True only when the body is a dict with RFC 7807 ProblemDetails markers (type, title, status fields) plus an 'errors' key whose values are lists of strings where at least one string uses ASP.NET-specific validation vocabulary.

*   AspNetParser.can_parse() must return False for: non-dict bodies (None, string, list), partial envelopes missing any of type/title/status/errors, envelopes where any errors value is not a list of strings, and envelopes whose messages do not contain ASP.NET-specific vocabulary.

*   AspNetParser.parse() must map DataAnnotations messages to observations: 'The X field is required.' -> MUST_NOT_BE_BLANK (payload=None); 'The X field is not a valid e-mail address.' -> FORMAT (FormatPayload(name='email')); 'The field X must be a string or array type with a minimum length of N.' -> SIZE_BOUND (SizeBoundPayload(min=N, max=None)); 'The field X must be a string or array type with a maximum length of N.' -> SIZE_BOUND (SizeBoundPayload(min=None, max=N)); 'The field X must be a string with a minimum length of N and a maximum length of M.' -> SIZE_BOUND (SizeBoundPayload(min=N, max=M)); 'The field X must be between A and B.' -> two NUMERIC_BOUND observations: NumericBoundPayload(bound=A, direction=BoundDirection.MIN, exclusive=False) and NumericBoundPayload(bound=B, direction=BoundDirection.MAX, exclusive=False); 'The field X must match the regular expression pattern.' -> PATTERN (PatternPayload(regex=pattern)).

*   AspNetParser.parse() must map FluentValidation messages to observations: "'X' must not be empty." -> MUST_NOT_BE_BLANK (payload=None); "'X' is not a valid email address." -> FORMAT (FormatPayload(name='email')); "The length of 'X' must be at least N characters. You entered M characters." -> SIZE_BOUND (SizeBoundPayload(min=N, max=None)); "The length of 'X' must be N characters or fewer. You entered M characters." -> SIZE_BOUND (SizeBoundPayload(min=None, max=N)); "'X' must be greater than 'N'." -> NUMERIC_BOUND (NumericBoundPayload(bound=N, direction=BoundDirection.MIN, exclusive=True)); "'X' must be less than 'N'." -> NUMERIC_BOUND (NumericBoundPayload(bound=N, direction=BoundDirection.MAX, exclusive=True)); "'X' must be between A and B. You entered N." -> two NUMERIC_BOUND observations: NumericBoundPayload(bound=A, direction=BoundDirection.MIN, exclusive=False) and NumericBoundPayload(bound=B, direction=BoundDirection.MAX, exclusive=False).

*   AspNetParser.parse() must normalize CamelCase field names to lowercase (e.g. 'Username' -> 'username', 'Email' -> 'email') when building parameter_path tuples; already-lowercase names must pass through unchanged.

*   AspNetParser.parse() must drop pseudo-fields: keys starting with '$.' (JSON pointer paths like '$.age') and the key 'input' must not produce any observations.

*   AspNetParser.parse() must silently drop empty string messages and messages that do not match any recognized DataAnnotations or FluentValidation pattern; no observations are generated for unrecognized messages.

*   AspNetParser.parse() must assign ParameterLocation.BODY for POST (and other non-GET) operations and ParameterLocation.QUERY for GET operations.

*   AspNetParser.parse() must return an empty tuple when the body does not match the ASP.NET ProblemDetails envelope format.

*   LaravelParser, RailsParser, PydanticParser, SpringParser, and JacksonParser must each return False from can_parse() for all bodies that AspNetParser recognizes as valid ASP.NET ProblemDetails envelopes.

*   When the CLI runs coverage and fuzzing phases against an API that returns ASP.NET-style validation error responses gating a hidden 500 error, it must use the validation feedback to generate inputs that satisfy the server's constraints, thereby uncovering the 500 server error alongside the original 400 failure.


*   Interface details: Type: Class
Name: AspNetParser
Location: src/schemathesis/core/error_feedback/parsers/aspnet.py
Description: Parser for ASP.NET Core Model Validation ProblemDetails-style 400 error responses. Implements the ResponseParser protocol. Recognizes both DataAnnotations and FluentValidation error messages and converts them to structured Observation objects. Must be registered in the PARSERS registry (e.g., via the @PARSERS.register decorator used by the other parsers).
Attribute: priority: int  (class-level integer, must be strictly greater than DRFParser.priority which is 3; existing code sets it to 6)
Signature: can_parse(self, *, body: object) -> bool
Signature: parse(self, *, operation: APIOperation, body: object) -> tuple[Observation, ...]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.